---
layout: post
title:  Linux安装redis扩展 
categories: [redis PHP]
description: Linux下Centos7 PHP使用redis 安装redis扩展 
keywords: Redis,php,Linux
---
安装环境: Centos7

pecl.php.net，PECL库是一个PHP扩展，提供一个目录的所有已知的扩展和托管设备下载PHP扩展，PHP很多扩展都可以在这里面找到。
今天主要讲解下Linux下php安装redis扩展，附有windows下redis扩展安装。

```
    第一步：开始在 PHP 中使用 Redis 前，我们需要确保已经安装了 redis 驱动，且你的机器上能正常使用 PHP。接下来让我们安装 PHP redis 驱动  
      //redis扩展的下载地址 有时候可能链接不上 毕竟是人家的下载源  
     [root@localhost ybaog]# wget http://pecl.php.net/get/redis-2.2.8.tgz  
      
      //解压刚刚下载好的压缩包  
     [root@localhost ybaog]# tar -zxvf 2.2.8.tar.gz  
      //进入主目录  
     [root@localhost ybaog]# cd phpredis-2.2.8/  
      //执行完这一步（用phpize生成configure配置文件） 会出现下面的错误   根据错误提示 可以看出 没有 php-devel 这个扩展  
      [root@localhost phpredis-2.2.8]# /usr/bin/phpize  
      
      Can't find PHP headers in /usr/include/php  
      The php-devel package is required for use of this command.  
      
     //安装 php-devel  会有以下提示   
      [root@localhost phpredis-2.2.8]# yum install php-devel  
      Another app is currently holding the yum lock; waiting for it to exit...  
      另一个应用程序是：PackageKit  
        内存：147 M RSS （477 MB VSZ）  
        已启动： Sun Aug 14 18:35:24 2016 - 58:34之前  
        状态  ：睡眠中，进程ID：3620  
      
      //杀死进程  
      [root@localhost phpredis-2.2.8]# kill -9 3620  
      
      //继续安装 php-devel 如果还提示 有一个进程、就继续杀死、知道没有提示为止。如果没有提示就 根据下载提示、一路属于 y 即可  
      [root@localhost phpredis-2.2.8]# yum install php-devel  
      //出现以下提示 代表安装成功  
        
       已安装:  
      php-devel.x86_64 0:5.4.16-36.3.el7_2                                                    
      
       作为依赖被安装:  
      pcre-devel.x86_64 0:8.32-15.el7_2.1                                                     
      
       作为依赖被升级:  
      pcre.x86_64 0:8.32-15.el7_2.1                                                           
      
       完毕！  
         
       //在执行 /usr/bin/phpize  这个密令、意思就是（用phpize生成configure配置文件）、 出现以下提示、说明 OK 了  
       [root@localhost phpredis-2.2.8]# /usr/bin/phpize  
       Configuring for:  
       PHP Api Version:         20100412  
       Zend Module Api No:      20100525  
       Zend Extension Api No:   220100525  
        
      //查看php-config 的路径  
      [root@localhost phpredis-2.2.8]# whereis php-config  
      
      php-config: /usr/bin/php-config /usr/share/man/man1/php-config.1.gz  
        
      //配置  
      [root@localhost phpredis-2.2.8]# ./configure --with-php-config=/usr/bin/php-config  
        
      //编译并且安装、（出现以下提示说明 OK）  
      [root@localhost phpredis-2.2.8]# make && make instll  
      Build complete.  
      Don't forget to run 'make test'.  
      
      Installing shared extensions:     /usr/lib64/php/modules/  
      
     //修改php.ini配置文件 让php支持redis    
     [root@localhost phpredis-2.2.8]#vi /etc/php.ini   
     //在 php.ini 的最后一行加上  
     extension=redis.so  
      
    ; directive because it is not set or is mistyped, a default value will be used.  
    extension=redis.so （加上）  
    ; The value can be a string, a number, a PHP constant (e.g. E_ALL or M_PI), one  
    ; of the INI constants (On, Off, True, False, Yes, No and None) or an expression  
    ; (e.g. E_ALL & ~E_NOTICE), a quoted string ("bar"), or a reference to a  
    ; previously set variable or directive (e.g. ${foo})  
      
    ; Expressions in the INI file are limited to bitwise operators and parentheses:  
    ; |  bitwise OR  
    ; ^  bitwise XOR  
    ; &  bitwise AND  
    :wq （保存并退出）  
       
     //如果安装的是 lamp 的环境、则重行启动 Apache   
     [root@localhost etc]# systemctl restart httpd  
       
     //如果安装的是 lnmp 的环境、则重新启动 php-fpm  
     [root@localhost html]# systemctl restart php-fpm  

     查看phpinfo，Redis扩展是否加载

     至此，Redis扩展已经安装成功并加载，可以用PHP操作Redis了。
      
```  
附：

> windows下安装Redis扩展就更加简单了，找到对应的版本，下载dll文件，
> 放到PHP目录下面的ext，修改PHP的配置文件php.ini，
> 加载extension=php_redis.dll，重启Apache，
> 查看phpinfo是否有Redis,
> 如果有就恭喜你，已经安装成功了，就是这么简单。
  