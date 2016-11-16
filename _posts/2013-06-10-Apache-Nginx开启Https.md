---
layout: post
title: Apache和Nginx开启Https  
categories: Linux
description: Apache和Nginx开启Https
keywords: Apache,Nginx,Https,Linux
---
### Apache服务器

> 1.安装mod_ssl和openssl  
> yum -y install mod_ssl openssl 

> 2.建立服务器密钥  
> mkdir /etc/httpd/conf.d/ssl.key/  
> cd /etc/httpd/conf.d/ssl.key/  
> openssl genrsa -out server.key 1024 

> 3.建立服务器公钥  
> openssl req -new -key server.key -out server.csr  

> 4.建立服务器证书   
> openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt  

> 5.最后对/etc/httpd/conf.d/ssl.conf 进行修改：将SSLCertificateFile和SSLCertificateKeyFile改成如下  
> SSLCertificateFile /etc/httpd/conf.d/ssl.key/server.cert  
> SSLCertificateKeyFile /etc/httpd/conf.d/ssl.key/server.key  

> 6.重启apache  

> 7.高级-->继续访问  

上面是apache开启https但是没有认证，nginx开启免费认证按照下面流程

### Nginx服务器

**1.第一步**

配置一个https站点需要有一个ssl的证书，我们可以到以下网址去申请一个免费的ssl证书：

` https://buy.wosign.com/Free/#ssl `   

**2.第二步**

有了ssl的证书后，我们可以将需要的服务器类型的压缩包解压后上传到服务器中。一共有两个文件，一个是.crt文件，还有一个是.key文件。

**3.第三步**

在原有的nginx的server配置中添加以下内容：

```
listen       443 ssl;    
server_name  xxx; #你的域名    
ssl                  on;    
ssl_certificate      xxx; #crt文件位置    
ssl_certificate_key     xxx;#key文件位置    
ssl_session_timeout  5m;    
ssl_protocols TLSv1 TLSv1.1 TLSv1.2;    
ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;    
ssl_prefer_server_ciphers   on;  

```

然后重启nginx，关闭浏览器并且清空缓存，就能够用https访问网站啦！

**4.第四步**

你可能会发现现在用http进不去网站了！怎么办呢？很简单，只要添加以下配置即可！

```
    server {    
            listen 80;    
            listen [::]:80 ssl ipv6only=on;    
            server_name   xxx;#域名    
            return 301 https://xxx$request_uri; #xxx为你的域名    
    }    
```

这样，用户即使不输入https，输入http也能访问了，所有连接都会被重定向到https页面！  