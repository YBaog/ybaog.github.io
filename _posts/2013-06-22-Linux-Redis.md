---
layout: post
title:  Linux 下 安装 Redis  
categories: Linux
description:Linux 下 安装 Redis 
keywords: Linux,Redis
---
Redis 端口号 6379

### Redis介绍

Redis是当前比较热门的NOSQL系统之一，它是一个key-value存储系统。
和Memcache类似，但很大程度补偿了Memcache的不足，它支持存储的value类型相对更多,包括string、list、set、zset和hash。
这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作。在此基础上，Redis支持各种不同方式的排序。
和Memcache一样，Redis数据都是缓存在计算机内存中，不同的是，Memcache只能将数据缓存到内存中，无法自动定期写入硬盘，这就表示，
一断电或重启，内存清空，数据丢失。所以Memcache的应用场景适用于缓存无需持久化的数据。
而Redis不同的是它会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，实现数据的持久化。

### Redis安装

```
//redis 的下载地址  
[root@localhost ybaog]#  wget http://download.redis.io/releases/redis-2.8.7.tar.gz  
  
 //解压下载好的 redis 压缩包  
[root@localhost ybaog]# tar -xzf redis-2.8.7.tar.gz  
  
 //解压完毕 进入redis-2.8.7 目录  
[root@localhost ybaog]# cd redis-2.8.7  
  
 //make一下  
[root@localhost redis-2.8.7]# make  
  
 //指定 redis 的安装目录  
[root@localhost redis-2.8.7]# make PREFIX=/usr/local/redis install  
  
 //将redis的配置文件复制一份 redis 的安装目录  
[root@localhost redis-2.8.7]# cp redis.conf /usr/local/redis  
  
 至此，编译安装完毕。   
  
 //进入 制定的安装目录   
[root@localhost ~]# cd /usr/local/redis/   
  
 //修改配置文件，将其中的"daemonize no"行改为"daemonize yes"，让其在后台运行。  
[root@localhost redis]# vim redis.conf   
  
 //启动redis服务   
[root@localhost redis]# ./bin/redis-server redis.conf   
  
 //关闭服务：   
[root@localhost redis]# ./bin/redis-cli -p 6379 shutdown   
  
 //简单的 set 与 get 的操作、看看 redis 是否能用   
[root@localhost redis]# ./bin/redis-cli   
127.0.0.1:6379> set name 'ybaog'   
OK   
127.0.0.1:6379> get name   
"ybaog"   
  
 // 如果想进入其他IP 则需要加上 -h  
[root@localhost redis]# ./bin/redis-cli -h 192.168.1.75   
192.168.1.75:6379> get name   
"ybaog"  

```