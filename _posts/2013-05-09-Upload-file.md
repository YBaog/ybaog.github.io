---
layout: post
title: PHP如何实现文件上传
categories: PHP
description: PHP 文件上传。
keywords: PHP, Upload
---
文件上传时，须注意的是：表单form标签中添加enctype属性，enctype="multipart/form-data";

### 表单部分 

  允许用户上传文件，在HTML表单的声明中要加上一个上传的属性： 


` enctype = 'multipart/form-data' `

  表单的method必须是POST 

  表单选项 `MAX_FILE_SIZE `隐藏域用于限制上传文件大小，它必须放在文件表单元素前面，单位为字节。 

  如： 
  复制代码代码如下:

```html

<form enctype='multipart/form-data' id='aa' name='aaa' method='post' action='xxx.php'> 
<input type='hidden' name='MAX_FILE_SIZE' value='2621114' /> 
<input name='upload_file' type='file' /> 
</form> 

```

### 处理上传文件 

  上传时，PHP收到关于该文件的信息数组，这些信息可以在`$_FILES`这个超级全局数组中找到。 

  如：如果表单中的文件输入框名字为upload_file，那么关于该文件的所有信息都包含在数组$_FILES['upload_file']里面。 

  如：客户上传了一个“aaa.jpg”的图片数组值如下： 

-  name "p5pp.jpg" 上传时文件的名字 

-  type “image/jpeg" 文件类型 

-  tmp_name "/tmp/phpjksdf" 服务器端的临时文件名 

-  error 上传错误的返回值 

-  size 2045 文件实际大小 

  上面数组里面的error会返回不同的常量值，如下: 

```php

UPLOAD_ERR_OK 没有错误发生，文件上传成功 

UPLOAD_ERR_INI_SIZE 文件大小超过了PHP.INI中upload_max_filesize选项限制的值 

UPLOAD_ERR_FORM_SIZE 上传文件的大小超过了HTML表单中MAX_FILE_SIZE选项值。可在程序中检查表单$FILES ['up_file']['size']来处理 

UPLOAD_ERR_PARTIAL 文件只有部分被上传 

UPLOAD_ERR_NO_FILE 用户没有提供任何文件上传 

```

  上传后处理的具体例子： 

  复制代码代码如下:

```php

if(!move_uploaded_file($_FILES['f']['tmp_name'],"uploads/".$_FILES['f']['name'].".jpg")){ 
echo "error"; 
} 

```

  函数： 

  `move_uploaded_file` 移动上传的临时文件到指定的目录下 

  例子： 

  `move_uploaded_file`('临时文件名','指定文件路径') 

  `is_uploaded_file` 判断是否是通过http Post上传的文件 

  例子: 

  复制代码代码如下:

```php

if(!is_uploaded_file($_FILES['f']['tmp_name'])){ 
echo '非法'; 
} 

```

### 相关参数 

  PHP上传设计到的php.ini中的参数： 

> file_uploads 是否允许上传文件，默认ON 

> upload_tmp_dir 上传文件防止的临时目录，未指定则使用系统默认位置 

> upload_max_filesize 允许上传文件的大小的最大值，默认为2M 

> post_max_size 控制采用POST方法进行一次表单提交中PHP所能接受的最大数据量，如果希望用PHP文件上传，则此值要改为比upload_max_filesize要大 

> max_input_time 以秒为单位对通过POST/GET/PUT方式接受数据时间进行限制。 

> memory_limit 为了避免正在运行的脚本大量使用系统内存，PHP允许定义内存使用限额。通过设置此参数来制定单个脚本程序可以使用的最大内存容量，应适当大于post_max_size值 

> max_execution_time 用来设置在强制终止脚本前PHP等待脚本执行完毕的时间，单位秒。次选项可限制死循环脚本，但当存在一个长时间的合法活动时（如：上传大文件），这项功能也会导致操作失败。这样情况下必须考虑将此变量增加。 

### 考虑多文件上传 

  可以利用$_FILES数组就可以轻松实现多文件上传。$_FILES数组可以获取客户端表单里面所有的file域内容，从而获得所有在同一表单上传的文件。 

### 突破上传的内存限制 

  方法一： 

  修改php.ini中memory_limit值，改为更大，如 64M 

  方法二：

  使用Apache Rewrite方法，动态修改memory_limit的值。首先建立一个.htaccess文件，保存在上传文件程序的 当前目录即可.代码如下： 

> php_value memory_limit 100M 

> php_value post_max_size 30M 

> php_value upload_max_filesize 30M 

> php_value max_execution_time 300 

> php_value max_input_tim 300 

> php_value display_errors On