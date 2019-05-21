---
layout: post
title: "常用的负载均衡实现"
date: 2019-05-21 09:45:00 
description: "常用的负载均衡实现"
tag: Nginx 负载均衡
--- 

####  七层负载均衡的实现
* 实现原理：
    - 基于 URL 等应用层的信息实现负载均衡；
     
    - nginx 的 proxy 是它一个很强大的功能，实现了七层负载均衡；
    
- 七层负载均衡的特点：
    - 配置简单，性能卓越，功能强大，运行稳定；
    
    - 能够自动剔除工作不正常的后端服务器；
    
    - 上传文件使用异步模式；
    
    - 支持多种分配策略，可以分配权重，配置简单；
- nginx 负载均衡策略
    - 内置策略
    
    - 扩展策略 

##### 内置策略
* 内置策略时安装nginx时已经安装到内核中的；
    - 加权轮训策略
    
        > - 首先将请求都分给高权重的机器，直到该机器的权值降到了比其他机器低的时候，才开始将请求分给下一个高权重的机器；
        > - 当所有后端机器都宕掉时，nginx 会将所有机器的标识为置为初始状态，避免所有机器都处于 timeout 状态；
    
    - IP hash 策略

        > - nginx 内置的另一个负载均衡策略，流程和轮训很类似，只是其中的算法和具体策略有一些变化，是一种变相的轮训算法；
        
##### 扩展策略
* 需要安装一些额外的模块实现负载均衡；
    - fair 策略

        >- 根据后端服务器的响应时间判断负载情况，从中选出负载最轻的机器进行分流；
    - 通用hash，一致性 hash 策略
        >- 通用 hash 比较简单，可以以nginx的内置变量 key 进行 hash ;
        >- 一致性 hash 使用 nginx 内置的一次性 hash 环，也支持 memcach ;
        
##### nginx 的配置    
* nginx 配置中，集群中的服务器名称后可以加 权重 weight ，nginx 会根据权重的大小依次进行分配不同的机器；

* 如果使用 IP hash 的话，可以在集群中加上 IP hash ，此时轮训策略就变成了 IP hash 策略了；

    ```nginx
    # 创建一个负载均衡组
    upstream imageserver {
        server 192.168.1.204:8080 weight=1 max_fails=2 fail_timeout=30s;
        server 192.168.1.204:8081 weight=1 max_fails=2 fail_timeout=30s;
    }
    ```
    * 2: 下游调用
    
    ```nginx
    location ~ \.(jpg|jpeg|png|gif)$ {
            proxy_set_header X-Forwarded-For $remote_addr;
            proxy_pass_header Server;
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            # 在这里使用创建的负载均衡组
            proxy_pass http://imageserver;
    }
    ```
    
#### 四层负载均衡的实现
* LVS 虚拟服务器
* 什么是 LVS？
    - LVS 是虚拟服务器，一个虚拟的服务器集群系统。1998 年由章文嵩博士成立，中国最早的软件之一；
- 为什么使用 LVS 呢？
    - 当数据量增大时，单台服务器的压力会增大，就会使用多台机器进行分担压力；
    -  通过 LVS 提供的负载均衡技术和 Linux 系统实现一个 高性能，高可用的服务器集群架构，具有良好的 可靠性，可扩展性 和 可操作性；
    -  使用低廉的成本实现最优的服务性能；
-  实现原理：
    - LVS 是基于 IP 地址调度的方法，是最高效的方法；
    
##### 三种 LVS 的三种调度模式：


* TUN Ip 隧道模式。IP 负载均衡技术：
    * IP 负载均衡技术是通过 IPVS 内核模块来实现的，IPVS 是负载均衡的核心软件；
    * 安装在 Director Server [DR 负载均衡调度器] 上，同时  DR 调度器上虚拟出来一个 IP地址 [Vip],将来域名解析的时候，解析到这个 VIP 中即可；
    * 访问请求先经过 VIP 然后达到负载均衡调度器上；
    * 由 DR 到 realServer [真实服务器] 列表中选取一个服务器节点响应用户的请求；

    ![流程如](https://upload-images.jianshu.io/upload_images/7303277-2121ead3a0ab29b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
* NAT 地址转发模式：
    * 地址转发技术
    * DR 需要将 VIP 配置到 DR上；
    * 将收到的集群服务，请求报文目标地址转换成根据算法得到的后端服务器主机的地址；
    * 然后后端服务器将响应报文发送给 DR；
    * 再由 DR 将源地址改成 VIP 地址；
    ![-w619](https://upload-images.jianshu.io/upload_images/7303277-3039abf8d598be14.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    
    * NAT 模式的特点：
        * LVS 上需要两块网卡，DIP 和 realServer 相同，VIP 由外网访问；
        * 内网的 realServer 的 IP 必须和 VIP 在同一个 网络中，并且要求 realServer 的网关都指向 DIP 的地址，返回数据要走 DIP ；
        * RIP [realServer 的 IP]，都是私有地址，仅用于各节点通信，公网不能访问；
        * DR 位于 client 和 realServer 之间，负责所有进出站的请求；
        * 因所有进出站请求都会经过 DR  应用在较大规模的系统中容易造成瓶颈。
        * 支持端口映射；
        
 * DR 模式。直接路由模式
        * client 发送一个请求给 VIP；
        * VIP 收到这个请求后会根据 LB 算法 选择一个 LB 较为合理的 realServer;
        * 把此请求的数据包的 MAC 地址修改为 realServer 的 MAC 地址；
        * ARP 协议 根据 MAC 地址 找到 realServer 会把这个数据包发送给真正的 realServer;
        * realServer 收到这个数据包，首先判断目标 IP 是不是自己，如果不是则把包丢弃。如果是自己的话就处理；[要在所有的 realServer 中绑定 VIP]
        * realServer 处理这个包后，会把目标 IP 修改成 client IP ，不再经过 DR，直接发送给客户端，这样效率会很高
        ![-w718](https://upload-images.jianshu.io/upload_images/7303277-4a44b3b37450da21.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 

