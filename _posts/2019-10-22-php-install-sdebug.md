---
layout: post
title: "配置安装 Sdebug swoole协程调试必备工具」"
date: 2019-10-22
description: "配置安装 Sdebug "
tag: 工具 
---


* 什么是 sdebug ?
    * sdebug 是 swoole 编程中常用的调试工具，和在传统 MVC 框架中使用 Xdebug 调试工具一样
    
    > Xdebug作为PHP的代码调试工具，提供了丰富的调试函数，也可将其配置为zend studio、editplus、phpstorm等IDE的第三方调试插件。通过开启Xdebug的自动跟踪(auto_trace)和分析器功能，更可以直观察PHP源代码的性能数据，从而优化PHP代码。
但无奈Xdebug的官方文档的语言是英文，PHP新手想按照官方文档把Xdebug配置好，也并非一件容易之事。本文就是关于PHP如何开启xdebug调试的一份简单指南，希望越来越多的人都能用上调试利器Xdebug。

* 为什么使用 sdebug ?
    * 在 swoole 官方文档中明确标注 swoole 协程与 一些PHP扩展冲突，其中就标明与 Xdebug 会冲突；
    ![-w803](https://raw.githubusercontent.com/xiaoshuaizhou/pic/master/chongtu.png)
    * 文档中也写了尝试使用 sdebug 进行调试；
* 怎么安装 sdebug ?
    
    * 自动安装
    
        #### 克隆
        
        * 首先使用 git 命令将 Sdebug 克隆下来
            
            > `git clone https://github.com/mabu233/sdebug.git`
            
        * 进入 sdebug 目录
            
            > `cd sdebug`
            
        #### 编译
        
        * 运行以下脚本
    
            > ./rebuild.sh
    
    
            - 这将运行 `phpize`，`./configure` ，`make clean` ，`make` 和 `make install` 。
    
    * 推荐安装方式「手动安装」
        
        * 运行 `phpize`

            > phpize   \#「如果 phpize 不在环境变量中，请使用 /path/to/phpize」 
        
        * 配置扩展
            > ./configure --enable-xdebug # 或 ../configure --enable-xdebug --with-php-config=/path/to/php-config
        
        * 执行编译
            
            >* make clean
            
            >* make && make install
        * php.ini 中添加以下配置
            > zend_extension="xdebug.so"
            \# 可以使用 `php -i | grep php.ini` 查看 php.ini 的位置
            
            ![sdebug-config](https://raw.githubusercontent.com/xiaoshuaizhou/pic/master/sdebug-config.jpeg)
            
            * 注意：有时php.ini命令行和Web服务器的文件是不同的。确保选择正确的一个。
        
        * 命令行检验 sdebug 是否安装成功
            
            ![php-version](https://raw.githubusercontent.com/xiaoshuaizhou/pic/master/php-version.jpeg)
            
        * 至此 sdebug 就安装成功了         
* 注意事项
    > 为了避免 swoole 的检测 xdebug 警告, 扩展注册的名称是 sdebug, 如果想使用 Phpunit CodeCoverage , 需要手动把检测 xdebug 的判断修改成 sdebug
单步调试: 如果php不是7.3的, 建议使用 sdebug_2_6(https://github.com/mabu233/sdebug/tree/sdebug), sdebug_2_7 可能需要与phpstorm2019搭配使用

* phpstorm 中配置
    * 首先进入 phpstorm 设置中 `Languages&Framworks` 中选择 `php` ,  在 `CLI Interpreter` 中添加 `php7.3`「版本选择查看上文中的注意事项」
    
    ![-w1121](https://raw.githubusercontent.com/xiaoshuaizhou/pic/master/Language.png)

    * Debug 中 `Debug port` 输入 9999
    
    ![-w851](https://raw.githubusercontent.com/xiaoshuaizhou/pic/master/debugport.png)    
    * `DBGp Proxy` 中填写 `IDE key` , `Host` , `Port` 
    
    ![-w915](https://raw.githubusercontent.com/xiaoshuaizhou/pic/master/dbgpport.png)

    * 重启 phpstorm ，打个断点试一下吧！
    
    ![](https://raw.githubusercontent.com/xiaoshuaizhou/pic/master/final.png)

    * 又看到熟悉的页面了！bingo！！
