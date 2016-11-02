---
layout: post
title: 学习 javascript 
categories: javascript
description: 给JavaScript初学者的24条最佳实践
keywords: Javascript  
---

JavaScript是一种解释型的语言，它不需要提前编译。通常情况下，JavaScript会放在网页中，在浏览器中运行。我们也可以找到一些在线的可以运行JavaScript的平台，当然我们也可以在Firefox或者Chrome里运行。

**1.使用 === 代替 == **

JavaScript 使用2种不同的等值运算符：===|!== 和 ==|!=，在比较操作中使用前者是最佳实践。

“如果两边的操作数具有相同的类型和值，===返回true，!==返回false。”——《JavaScript：语言精粹》

然而，当使用==和！=时，你可能会遇到类型不同的情况，这种情况下，操作数的类型会被强制转换成一样的再做比较，这可能不是你想要的结果。

**2.Eval=邪恶**

起初不太熟悉时，“eval”让我们能够访问JavaScript的编译器（译注：这看起来很强大）。从本质上讲，我们可以将字符串传递给eval作为参数，而执行它。

这不仅大幅降低脚本的性能（译注：JIT编译器无法预知字符串内容，而无法预编译和优化），而且这也会带来巨大的安全风险，因为这样付给要执行的文本太高的权限，避而远之。

**3.省略未必省事**

从技术上讲，你可以省略大多数花括号和分号。大多数浏览器都能正确理解下面的代码:

```java

if(someVariableExists)  

   x = false
```
然后，如果像下面这样：
Shell
if(someVariableExists)  
   x = false  
   anotherFunctionCall();
1
2
3
	
if(someVariableExists)  
   x = false  
   anotherFunctionCall();

有人可能会认为上面的代码等价于下面这样:
Shell
if(someVariableExists) {  
   x = false;  
   anotherFunctionCall();  
}
1
2
3
4
	
if(someVariableExists) {  
   x = false;  
   anotherFunctionCall();  
}

不幸的是，这种理解是错误的。实际上的意思如下:
Shell
```javascript

if(someVariableExists) {  
   x = false;  
}  
anotherFunctionCall();
	
if(someVariableExists) {  
   x = false;  
}  
anotherFunctionCall();

```

你可能注意到了，上面的缩进容易给人花括号的假象。无可非议，这是一种可怕的实践，应不惜一切代价避免。仅有一种情况下，即只有一行的时候，花括号是可以省略的，但这点是饱受争议的。

```javascript
	
if(2 + 2 === 4) return 'nicely done';

```
4.使用JSLint

JSLint是由大名鼎鼎的道格拉斯（Douglas Crockford）编写的调试器。简单的将你的代码粘贴进JSLint中，它会迅速找出代码中明显的问题和错误。

“JSLint扫面输入的源代码。如果发现一个问题，它返回一条描述问题和一个代码中的所在位置的消息。问题并不一定是语法错误，尽管通常是这样。JSLint还会查看一些编码风格和程序结构问题。这并不能保证你的程序是正确的。它只是提供了另一双帮助发现问题的眼睛。”——JSLing 文档

部署脚本之前，运行JSLint，只是为了确保你没有做出任何愚蠢的错误。

 
5.将脚本放在页面的底部

在本系列前面的文章里已经提到过这个技巧，我粘贴信息在这里。

 

记住——首要目标是让页面尽可能快的呈献给用户，脚本的夹在是阻塞的，脚本加载并执行完之前，浏览器不能继续渲染下面的内容。因此，用户将被迫等待更长时间。

如果你的js只是用来增强效果——例如，按钮的单击事件——马上将脚本放在body结束之前。这绝对是最佳实践。

建议

```html

<p>And now you know my favorite kinds of corn. </p>  
<script type="text/javascript" src="path/to/file.js"></script>  
<script type="text/javascript" src="path/to/anotherFile.js"></script>  
</body>  
</html>
	
<p>And now you know my favorite kinds of corn. </p>  
<script type="text/javascript" src="path/to/file.js"></script>  
<script type="text/javascript" src="path/to/anotherFile.js"></script>  
</body>  
</html>

```

6.避免在For语句内声明变量

当执行冗长的for语句时，要保持语句块的尽量简洁，例如：

糟糕
```javascript

for(var i = 0; i < someArray.length; i++) {  
   var container = document.getElementById('container');  
   container.innerHtml += 'my number: ' + i;  
   console.log(i);  
}
	
for(var i = 0; i < someArray.length; i++) {  
   var container = document.getElementById('container');  
   container.innerHtml += 'my number: ' + i;  
   console.log(i);  
}

```
注意每次循环都要计算数组的长度，并且每次都要遍历dom查询“container”元素——效率严重地下！

建议

```javascript

var container = document.getElementById('container');  
for(var i = 0, len = someArray.length; i < len;  i++) {  
   container.innerHtml += 'my number: ' + i;  
   console.log(i);  
}	
var container = document.getElementById('container');  
for(var i = 0, len = someArray.length; i < len;  i++) {  
   container.innerHtml += 'my number: ' + i;  
   console.log(i);  
}

```
感兴趣可以思考如何继续优化上面的代码，欢迎留下评论大家分享。

 
7.构建字符串的最优方法

当你需要遍历数组或对象的时候，不要总想着“for”语句，要有创造性，总能找到更好的办法，例如，像下面这样。

```javascript

var arr = ['item 1', 'item 2', 'item 3', ...];  

var list = '<ul><li>' + arr.join('</li><li>') + '</li></ul>';

var arr = ['item 1', 'item 2', 'item 3', ...];  

var list = '<ul><li>' + arr.join('</li><li>') + '</li></ul>';

我不是你心中的神，但请你相信我（不信你自己测试）——这是迄今为止最快的方法！使用原生代码（如 join()），不管系统内部做了什么，通常比非原生快很多。——James Padolsey, james.padolsey.com

 
8.减少全局变量

 只要把多个全局变量都整理在一个名称空间下，拟将显著降低与其他应用程序、组件或类库之间产生糟糕的相互影响的可能性。——Douglas Crockford
```javascript
var name = 'Jeffrey';  
var lastName = 'Way';  

function doSomething() {...}  

console.log(name); // Jeffrey -- 或 window.name	
var name = 'Jeffrey';  
var lastName = 'Way';  
 
function doSomething() {...}  
 
console.log(name); // Jeffrey -- 或 window.name

更好的做法

var DudeNameSpace = {  
   name : 'Jeffrey',  
   lastName : 'Way',  
   doSomething : function() {...}  
}  
console.log(DudeNameSpace.name); // Jeffrey
	
var DudeNameSpace = {  
   name : 'Jeffrey',  
   lastName : 'Way',  
   doSomething : function() {...}  
}  
console.log(DudeNameSpace.name); // Jeffrey

```

注：这里只是简单命名为 “DudeNameSpace”，实际当中要取更合理的名字。

9.给代码添加注释

似乎没有必要，当请相信我，尽量给你的代码添加更合理的注释。当几个月后，重看你的项目，你可能记不清当初你的思路。或者，假如你的一位同事需要修改你的代码呢？总而言之，给代码添加注释是重要的部分。
JavaScript
```javascript

// 循环数组，输出每项名字（译者注：这样的注释似乎有点多余吧）.   
for(var i = 0, len = array.length; i < len; i++) {  
   console.log(array[i]);  
	
// 循环数组，输出每项名字（译者注：这样的注释似乎有点多余吧）.   
for(var i = 0, len = array.length; i < len; i++) {  
   console.log(array[i]);  
}

```
 10.拥抱渐进增强

确保javascript被禁用的情况下能平稳退化。我们总是被这样的想法吸引，“大多数我的访客已经启用JavaScript,所以我不必担心。”然而，这是个很大的误区。

你可曾花费片刻查看下你漂亮的页面在javascript被关闭时是什么样的吗？（下载 Web Developer 工具就能很容易做到（译者注：chrome用户在应用商店里自行下载，ie用户在Internet选项中设置）），这有可能让你的网站支离破碎。作为一个经验法则，设计你的网站时假设JavaScript是被禁用的，然后，在此基础上，逐步增强你的网站。

 
11.不要给”setInterval”或”setTimeout”传递字符串参数

考虑下面的代码:

```javascript

setInterval(  
"document.getElementById('container').innerHTML += 'My new number: ' + i", 3000  
);	
setInterval(  
"document.getElementById('container').innerHTML += 'My new number: ' + i", 3000  
);

不仅效率低下，而且这种做法和”eval”如出一辙。从不给setInterval和setTimeout传递字符串作为参数，而是像下面这样传递函数名。

setInterval(someFunction, 3000);
1
	
setInterval(someFunction, 3000);

```

12.不要使用”with”语句

乍一看，”with”语句看起来像一个聪明的主意。基本理念是,它可以为访问深度嵌套对象提供缩写，例如……

```javascript
with (being.person.man.bodyparts) {  
   arms = true;  
   legs = true;  
}
	
with (being.person.man.bodyparts) {  
   arms = true;  
   legs = true;  
}

而不是像下面这样：

being.person.man.bodyparts.arms = true;  

being.person.man.bodyparts.legs= true;

being.person.man.bodyparts.arms = true;  

being.person.man.bodyparts.legs= true;

不幸的是，经过测试后，发现这时“设置新成员时表现得非常糟糕。作为代替，您应该使用变量，像下面这样。

```javascript
var o = being.person.man.bodyparts;  
o.arms = true;  
o.legs = true;
var o = being.person.man.bodyparts;  
o.arms = true;  
o.legs = true;
```
13.使用{}代替 new Ojbect()

在JavaScript中创建对象的方法有多种。可能是传统的方法是使用”new”加构造函数，像下面这样:

```javascript

var o = new Object();  
o.name = 'Jeffrey';  
o.lastName = 'Way';  
o.someFunction = function() {  
   console.log(this.name);  
}
	
var o = new Object();  
o.name = 'Jeffrey';  
o.lastName = 'Way';  
o.someFunction = function() {  
   console.log(this.name);  
}

```

然而，这种方法的受到的诟病不及实际上多。作为代替,我建议你使用更健壮的对象字面量方法。

更好的做法

```javascript
var o = {  
   name: 'Jeffrey',  
   lastName = 'Way',  
   someFunction : function() {  
      console.log(this.name);  
   }  
};	
var o = {  
   name: 'Jeffrey',  
   lastName = 'Way',  
   someFunction : function() {  
      console.log(this.name);  
   }  
};

```

注意，果你只是想创建一个空对象，{}更好。

```javascript
var o = {};
1
	
var o = {};

```

“对象字面量使我们能够编写更具特色的代码，而且相对简单的多。不需要直接调用构造函数或维持传递给函数的参数的正确顺序，等”——dyn-web.com

 
14.使用[]代替 new Array()

这同样适用于创建一个新的数组。

例如：

```javascript
var a = new Array();  
a[0] = "Joe";  
a[1] = 'Plumber';
	
var a = new Array();  
a[0] = "Joe";  
a[1] = 'Plumber';

更好的做法：

var a = ['Joe','Plumber'];
1
	
var a = ['Joe','Plumber'];

“javascript程序中常见的错误是在需要对象的时候使用数组，而需要数组的时候却使用对象。规则很简单：当属性名是连续的整数时，你应该使用数组。否则，请使用对象”——Douglas Crockford

 ```

15.定义多个变量时，省略var关键字，用逗号代替

```javascript

var someItem = 'some string';  

var anotherItem = 'another string';  

var oneMoreItem = 'one more string';

var someItem = 'some string';  

var anotherItem = 'another string';  

var oneMoreItem = 'one more string';


var someItem = 'some string',  

    anotherItem = 'another string',  

    oneMoreItem = 'one more string';
	
var someItem = 'some string', 

    anotherItem = 'another string',  

    oneMoreItem = 'one more string';
```
…应而不言自明。我怀疑这里真的有所提速，但它能是你的代码更清晰。

 

 


