---
layout: post
title:  Linux安装Memcache 
categories: [Linux Windows]
description: Windows下的Memcache安装 linux下的Memcache安装 
keywords: Windows,Linux,Memcache
---
Memcached 端口号 :11211

Memcached 是一个高性能的分布式内存对象缓存系统，用于动态Web应用以减轻数据库负载。
它通过在内存中缓存数据和对象来减少读取数据库的次数，从而提高动态、数据库驱动网站的速度。Memcached基于一个存储键/值对的hashmap。
其守护进程（daemon ）是用C写的，但是客户端可以用任何语言来编写，并通过memcached协议与守护进程通信。

### linux下的Memcache安装：  

1. 下载 memcache的linux版本，注意 memcached 用 libevent 来作事件驱动，所以要先安装有 libevent。  

官方网址：http://memcached.org/  

2. 安装 pecl::memcache。  

用 pecl 命令行工具安装：

`1pecl install memcache ` 

或直接从源码安装： 

> 1. phpize  
> 2. ./configure  
> 3. make  
> 4. make install  



### Windows下的Memcache安装：  

1. 下载memcache的windows稳定版，解压放某个盘下面，比如在c:/memcached  

2. 开始→运行→输入cmd回车→输入 ‘c:/memcached/memcached.exe -d install’ 安装  

3. 再输入： ‘c:/memcached/memcached.exe -d start’ 启动。  

NOTE: 以后memcached将作为windows的一个服务每次开机时自动启动。这样服务器端已经安装完毕了。  

4.下载php_memcache.dll，请自己查找对应的php版本的文件  

5. 在C:/winnt/php.ini 加入一行 ‘extension=php_memcache.dll’  

6.重新启动Apache，然后查看一下phpinfo，如果有memcache，那么就说明安装成功！  

附件：  

memcache下载：http://download.csdn.net/detail/x171306523c/7863679  

php_memcache.dll下载：http://download.csdn.net/detail/x171306523c/7863693  



**Windows下的Memcache卸载：**  

`memcached -d stop  `

`memcached -d remove ` 

`sc delete "Memcached Server" ` 



**memcached的基本设置：**  

> -p 监听的端口  
> -l 连接的IP地址, 默认是本机  
> -d start 启动memcached服务  
> -d restart 重起memcached服务  
> -d stop|shutdown 关闭正在运行的memcached服务  
> -d install 安装memcached服务  
> -d uninstall 卸载memcached服务  
> -u 以的身份运行 (仅在以root运行的时候有效)  
> -m 最大内存使用，单位MB。默认64MB  
> -M 内存耗尽时返回错误，而不是删除项  
> -c 最大同时连接数，默认是1024  
> -f 块大小增长因子，默认是1.25-n 最小分配空间，key+value+flags默认是48  
> -h 显示帮助  



**php.ini中的配置:**  

; 是否在遇到错误时透明地向其他服务器进行故障转移。  
  `memcache.allow_failover = On ` 

; 接受和发送数据时最多尝试多少个服务器，只在打开memcache.allow_failover时有效。  
`memcache.max_failover_attempts = 20  `

; 数据将按照此值设定的块大小进行转移。此值越小所需的额外网络传输越多。  
; 如果发现无法解释的速度降低，可以尝试将此值增加到32768。  
 ` memcache.chunk_size = 8192 ` 

; 连接到memcached服务器时使用的默认TCP端口。  
`memcache.default_port = 11211  `

; 控制将key映射到server的策略。默认值"standard"表示使用先前版本的老hash策略。  
; 设为"consistent"可以允许在连接池中添加/删除服务器时不必重新计算key与server之间的映射关系。  
;memcache.hash_strategy = "standard"; 控制将key映射到server的散列函数。默认值"crc32"使用CRC32算法，而"fnv"则表示使用FNV-1a算法。  
; FNV-1a比CRC32速度稍低，但是散列效果更好。  
`memcache.hash_function = "crc32" ` 



;memcache也可以作为session的存储模块,具体参看:memcache PHP 的 session.save_handler.  

http://hi.baidu.com/whzkinger/item/6cc85c5c01fbc011db16355f  



**memcache的测试代码:** 

```php
<?php   
$memcache = new Memcache;    
$memcache->connect('localhost', 11211) or die ("Could not connect");    

$version = $memcache->getVersion();    
echo "Server's version: ".$version."<br>/n";    

$tmp_object = new stdClass;    
$tmp_object->str_attr = 'test';    
$tmp_object->int_attr = 123;    

$memcache->set('key', $tmp_object, false, 10) or die ("Failed to save data at the server");    
echo "Store data in the cache (data will expire in 10 seconds)<br>/n";    

$get_result = $memcache->get('key');    
echo "Data from the cache:<br>/n";    

var_dump($get_result);  
?>  

```