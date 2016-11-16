---
layout: post
title:  PHP通过SSH2扩展控制Linux  
categories: [Linux PHP]
description: PHP通过SSH2扩展控制Linux  
keywords: SSH2,php,Linux
---
### PHP通过SSH2扩展控制Linux,模拟阿里云web控制台 

注意：我们用PHP来控制Linux，php环境可以在windows也可以在linux，但是我们要控制的机器是一台linux（被控制的linux关闭selinux和firewalld）。

然后我们的php环境要想实现控制linux，必须安装php的一个扩展ssh2

 **0.下载ssh2扩展，从http://pecl.php.net/package/ssh2，有linux扩展包，也有windows的dll，如果是php5安装ssh2-0.13或0.12扩展,php7安装1.0**
 
 **1.windows中的php安装扩展，需要下载dll文件，在php.ini中引入即可**
 
 **2.这里是centos中的php安装ssh2扩展，记得关闭selinux和firewalld**

`yum install libssh2 libssh2-devel php-devel gcc-c++`

**3.如果是php5安装ssh2-0.13扩展,php7安装1.0**

`cd ssh2-0.13/`

```
/usr/bin/phpize 
./configure --with-php-config=/usr/bin/php-config LIBS=-ldl
make
make install

```
**4. vi /etc/php.ini**

加入`extension=ssh2.so`

**5.重启apache**

**6.vi test.php**

```php

    <?php  
      
    $host='127.0.0.1';//被控制的linux的ip  
      
    $user='root';//用户名  
      
    $passwd='123456';//密码  
      
    // 链接远程服务器  
      
    $connection = ssh2_connect($host, 22);  
      
    if (!$connection) die('connection to '.$host.':22 failed');  
      
    echo 'connection OK<br/>';  
      
    // 获取验证方式并打印  
      
    $auth_methods = ssh2_auth_none($connection, $user);  
      
    print_r( $auth_methods.'<br/>');  
      
    if (in_array('password', $auth_methods ))  
    {  
      
        // 通过password方式登录远程服务器  
      
        if (ssh2_auth_password($connection, $user, $passwd))  
      
        {  
      
            echo $user.' login OK<br/>';  
      
            $stream = ssh2_exec($connection, "pwd"); // 执行php  
      
            stream_set_blocking($stream, true); // 获取执行pwd后的内容  
      
             if ($stream === FALSE) die("pwd failed");  
      
            echo 'pwd: '.stream_get_contents($stream).'<br/>';  
      
        }  
      
        else  
      
        {  
      
            die( $user.' login Failed<br/>');  
      
        }  
      
    }  

```