---
layout: post
title:  初窥Linux 之 常用命令
categories: Linux
description:下面就说说我最常用的Linux命令。
keywords: Linux,命令
---

### Linux 常用的命令

- `cd` 它用于切换当前目录，它的参数是要切换到的目录的路径，可以是绝对路径，也可以是相对路径。

- `ls` 这是一个非常有用的查看文件与目录的命令

```
    -l ：列出长数据串，包含文件的属性与权限数据等  
    -a ：列出全部的文件，连同隐藏文件（开头为.的文件）一起列出来（常用）  
    -d ：仅列出目录本身，而不是列出目录的文件数据  
    -h ：将文件容量以较易读的方式（GB，kB等）列出来  
    -R ：连同子目录的内容一起列出（递归列出），等于该目录下的所有文件都会显示出来  
```

- `grep` 该命令常用于分析一行的信息，若当中有我们所需要的信息，就将该行显示出来，该命令通常与管道命令一起使用，用于对一些命令的输出进行筛选加工等等
   
```
    grep [-acinv] [--color=auto] '查找字符串' filename 

-a ：将binary文件以text文件的方式查找数据  
-c ：计算找到‘查找字符串’的次数  
-i ：忽略大小写的区别，即把大小写视为相同  
-v ：反向选择，即显示出没有‘查找字符串’内容的那一行  
# 例如：  
# 取出文件/etc/man.config中包含MANPATH的行，并把找到的关键字加上颜色  
grep --color=auto 'MANPATH' /etc/man.config  
# 把ls -l的输出中包含字母file（不区分大小写）的内容输出  
ls -l | grep -i file 

```

- `find` 是一个基于查找的功能非常强大的命令，相对而言，它的使用也相对较为复杂，参数也比较多，所以在这里将给把它们分类列出

```
    find [PATH] [option] [action]  
      
    # 与时间有关的参数：  
    -mtime n : n为数字，意思为在n天之前的“一天内”被更改过的文件；  
    -mtime +n : 列出在n天之前（不含n天本身）被更改过的文件名；  
    -mtime -n : 列出在n天之内（含n天本身）被更改过的文件名；  
    -newer file : 列出比file还要新的文件名  
    # 例如：  
    find /root -mtime 0 # 在当前目录下查找今天之内有改动的文件  
      
    # 与用户或用户组名有关的参数：  
    -user name : 列出文件所有者为name的文件  
    -group name : 列出文件所属用户组为name的文件  
    -uid n : 列出文件所有者为用户ID为n的文件  
    -gid n : 列出文件所属用户组为用户组ID为n的文件  
    # 例如：  
    find /home/ljianhui -user ljianhui # 在目录/home/ljianhui中找出所有者为ljianhui的文件  
      
    # 与文件权限及名称有关的参数：  
    -name filename ：找出文件名为filename的文件  
    -size [+-]SIZE ：找出比SIZE还要大（+）或小（-）的文件  
    -tpye TYPE ：查找文件的类型为TYPE的文件，TYPE的值主要有：一般文件（f)、设备文件（b、c）、  
                 目录（d）、连接文件（l）、socket（s）、FIFO管道文件（p）；  
    -perm mode ：查找文件权限刚好等于mode的文件，mode用数字表示，如0755；  
    -perm -mode ：查找文件权限必须要全部包括mode权限的文件，mode用数字表示  
    -perm +mode ：查找文件权限包含任一mode的权限的文件，mode用数字表示  
    # 例如：  
    find / -name passwd # 查找文件名为passwd的文件  
    find . -perm 0755 # 查找当前目录中文件权限的0755的文件  
    find . -size +12k # 查找当前目录中大于12KB的文件，注意c表示byte  
```

- `cp` 该命令用于复制文件，copy之意，它还可以把多个文件一次性地复制到一个目录下

```
    -a ：将文件的特性一起复制  
    -p ：连同文件的属性一起复制，而非使用默认方式，与-a相似，常用于备份  
    -i ：若目标文件已经存在时，在覆盖时会先询问操作的进行  
    -r ：递归持续复制，用于目录的复制行为  
    -u ：目标文件与源文件有差异时才会复制  
```

- `mv` 该命令用于移动文件、目录或更名，move之意

```
    -f ：force强制的意思，如果目标文件已经存在，不会询问而直接覆盖  
    -i ：若目标文件已经存在，就会询问是否覆盖  
    -u ：若目标文件已经存在，且比目标文件新，才会更新 
    
```

- `rm` 该命令用于删除文件或目录，remove之间

```
    -f ：就是force的意思，忽略不存在的文件，不会出现警告消息  
    -i ：互动模式，在删除前会询问用户是否操作  
    -r ：递归删除，最常用于目录删除，它是一个非常危险的参数  
```

- `ps` 该命令用于将某个时间点的进程运行情况选取下来并输出，process之意

```
    -A ：所有的进程均显示出来  
    -a ：不与terminal有关的所有进程  
    -u ：有效用户的相关进程  
    -x ：一般与a参数一起使用，可列出较完整的信息  
    -l ：较长，较详细地将PID的信息列出  
    例: 
        ps aux # 查看系统所有的进程数据  
	ps ax # 查看不与terminal有关的所有进程  
	ps -lA # 查看系统所有的进程数据  
	ps axjf # 查看连同一部分进程树状态 
```

- `kill` 该命令用于向某个工作（%jobnumber）或者是某个PID（数字）传送一个信号，它通常与ps和jobs命令一起使用

```
    kill -signal PID 

    1：SIGHUP，启动被终止的进程  
    2：SIGINT，相当于输入ctrl+c，中断一个程序的进行  
    9：SIGKILL，强制中断一个进程的进行  
    15：SIGTERM，以正常的结束进程方式来终止进程  
    17：SIGSTOP，相当于输入ctrl+z，暂停一个进程的进行  

```

- `killall` 该命令用于向一个命令启动的进程发送一个信号

``` 
     killall [-iIe] [command name]  

    -i ：交互式的意思，若需要删除时，会询问用户  
    -e ：表示后面接的command name要一致，但command name不能超过15个字符  
    -I ：命令名称忽略大小写  
    # 例如：  
    killall -SIGHUP syslogd # 重新启动syslogd  
```

- `file` 该命令用于判断接在file命令后的文件的基本数据，因为在Linux下文件的类型并不是以后缀为分的，所以这个命令对我们来说就很有用了

```
    file filename  
    #例如：  
    file ./test  
```

- `tar` 该命令用于对文件进行打包，默认情况并不会压缩，如果指定了相应的参数，它还会调用相应的压缩程序（如gzip和bzip等）进行压缩和解压。

```
    -c ：新建打包文件  
    -t ：查看打包文件的内容含有哪些文件名  
    -x ：解打包或解压缩的功能，可以搭配-C（大写）指定解压的目录，注意-c,-t,-x不能同时出现在同一条命令中  
    -j ：通过bzip2的支持进行压缩/解压缩  
    -z ：通过gzip的支持进行压缩/解压缩  
    -v ：在压缩/解压缩过程中，将正在处理的文件名显示出来  
    -f filename ：filename为要处理的文件  
    -C dir ：指定压缩/解压缩的目录dir  

    压缩：tar -jcv -f filename.tar.bz2 要被处理的文件或目录名称  
    查询：tar -jtv -f filename.tar.bz2  
    解压：tar -jxv -f filename.tar.bz2 -C 欲解压缩的目录  

```

- `cat`  该命令用于查看文本文件的内容，后接要查看的文件名，通常可用管道与more和less一起使用，从而可以一页页地查看数据

```
    cat text | less # 查看text文件中的内容  
    # 注：这条命令也可以使用less text来代替  

```

- `chgrp` 该命令用于改变文件所属用户组

```
    chgrp [-R] dirname/filename  
    -R ：进行递归的持续对所有文件和子目录更改  

    # 例如：  
    chgrp users -R ./dir # 递归地把dir目录下中的所有文件和子目录下所有文件的用户组修改为users  

```

- `chmod` 该命令用于改变文件的权限

```
    chmod [-R] xyz 文件或目录  
    -R：进行递归的持续更改，即连同子目录下的所有文件都会更改  

    同时，chmod还可以使用u（user）、g（group）、o（other）、a（all）和+（加入）、-（删除）、=（设置）跟rwx搭配来对文件的权限进行更改。

    # 例如：  
    chmod 0755 file # 把file的文件权限改变为-rxwr-xr-x  
    chmod g+w file # 向file的文件权限中加入用户组可写权限  
```

- `gcc` 对于一个用Linux开发C程序的人来说，这个命令就非常重要了，它用于把C语言的源程序文件，编译成可执行程序，由于g++的很多参数跟它非常相似

```
    -o ：output之意，用于指定生成一个可执行文件的文件名  
    -c ：用于把源文件生成目标文件（.o)，并阻止编译器创建一个完整的程序  
    -I ：增加编译时搜索头文件的路径  
    -L ：增加编译时搜索静态连接库的路径  
    -S ：把源文件生成汇编代码文件  
    -lm：表示标准库的目录中名为libm.a的函数库  
    -lpthread ：连接NPTL实现的线程库  
    -std= ：用于指定把使用的C语言的版本  
      
    # 例如：  
    # 把源文件test.c按照c99标准编译成可执行程序test  
    gcc -o test test.c -lm -std=c99  
    #把源文件test.c转换为相应的汇编程序源文件test.s  
    gcc -S test.c  

```

- `time` 该命令用于测算一个命令（即程序）的执行时间。它的使用非常简单，就像平时输入命令一样，不过在命令的前面加入一个time即可

```
    time ./process  
    time ps aux  

```

- `shutdown` 关机指令
   
```
   -t :sec : -t后面加秒数, [过几秒关机]的意思
   -k :不是真的关机,只是发出警告!
   -r : 在将系统的服务停掉之后就重新启动
   -h : 将系统停掉后,立即关机
   -n : 不经过init程序,直接以 shutdown 的功能来关机
   -f : 关机并开机之后, 强制略过 fsck 的磁盘检查
   -c : 取消已将在进行的 shutdown指令内容
   时间:必要的参数!指定系统关机的时间!

   例: shutdown -h now(20:25/+10)

   重启,关机: reboot,halt,poweroff

```

- `mkdir [-mp]目录名称` 建立新目录

```
   -m : 配置文件案的权限! 直接设定,不需要看预设权限
   -p :帮助你直接将所需要的目录(包含上层目录)递归建立起来!

```
- `rmdir` 删除目录        `-p : 连同上一层[空] 目录一起删除!`
   
- `date` 显示日期

- `cal`  显示日历

- `bc`  好用的计数器

- `sync` 数据写入磁盘

- `uname -r` 查看版本信息

- `pwd` 查看当前所在目录

- `whereis,locate, which` 寻找特定的档案  

- `su` 切换用户

- `setup` 显示防火墙

> 安装和登陆命令: login,shutdown,halt,reboot,install,mount,umount,chsh,exit,last;
> 文件处理命令: file,mkdir,grep,dd,find,mv,ls,diff,cat,In;
> 系统管理命令: df,top,free,quota,at,lp,adduser,groupadd,kill;

