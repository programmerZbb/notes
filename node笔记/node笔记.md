## Node.js第一天



### 1. 初识Node.js

#### 1.1 Node.js是什么

* Node.js® is a JavaScript runtime built on [Chrome's V8 JavaScript engine](https://developers.google.com/v8/). 
  * Node.js不是一门语言
  * Node.js不是库(library)、不是框架(frame)
  * ==Node.js是一个JavaScript运行环境==
  * <u>简单点来讲就是Node.js可以解析和执行JavaScript代码</u>
  * ==以前只有浏览器可以解析和执行JavaScript代码==
  * 也就是说现在的JavaScript可以完全脱落浏览器来运行，一切都归功于：Node.js
  * 构建于chrome的v8引擎之上
    * 代码只是具有特定格式的字符串
    * 引擎可以帮你去解析和执行
    * chrome的V8引擎是目前工人的解析执行js代码最快的
    * node.js作者把Google Chrome中的V8引擎移植了出来，开发了一个独立的js运行环境
* 浏览器中的JavaScript
  * ECMAscript
    * js基本语法
  * BOM
  * DOM
* Node.js中的JavaScript
  * 没有BOM、DOM
  * 有ECMAScript
  * 在Node这个JavaScript执行环境中为JavaScript提供了一些服务器级别的操作API
    * 例如文件的读写
    * 网络服务的构建
    * 网络通信
    * http服务器
    * 等处理(相当于开发web服务器，学习对应的api即可)



* Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient. 
  * event-driven 事件驱动
  * non-blocking I/O model 非阻塞IO模型（异步）
  * lightweight and efficient.  轻量和高效
  * 随着课程慢慢学习会明白事件驱动、非阻塞IO模型
  * ==NodeJS以事件驱动著名，通过异步的编程达到高吞吐量高性能。==
* Node.js' package ecosystem, [npm](https://www.npmjs.com/), is the largest ecosystem of open source libraries in the world.
  * npm是世界上最大的开源库生态系统
  * 绝大多数JavaScript相关的包都存放在了npm上，这样做的目的是为了让开发人员更方便的去下载使用
  * npm是基于node.js开发的包管理工具



#### 1.2 ==node的API参考文档的使用：==

* 黑点后面代表参数，例：

  * request   <http.IncomingMessage>

  如果是方法它会告诉你方法的参数是什么，是否可选的。

  上述`request`代表绑定函数的参数，因为它是一个事件因此肯定需要绑定一个函数，后面的连接为这个参数的类型，功能上能够调用什么变量和方法。

* 加（）的代表方法，不加（）的代表属性





#### 1.3 Node.js能做什么

* web服务器后台
* 命令行工具
  * npm
  * git(C语言)
  * hexo(node)
  * 。。。
* 对于前端开发工程师来讲，接触node最多的是使用它的命令行工具
  * 自己写的很少，主要是使用别人第三方发布的
  * webpack
  * gulp
  * npm

#### 1.4 达到目标

* B/S编程模型
  * browser-Server
  * back-end
  * 任何服务端技术这种BS编程模型都是一样，和语言无关
  * Node只是一个工具
* 模块化编程
  * RequireJS
  * SeaJS
  * css中可以使用`@import('文件路径')`来实现引入外部文件
  * 以前认知的js智能通过`script`标签来加载
  * 在Node中可以像`@import`一样来引用加载JavaScript脚本文件
* Node常用API
* 异步编程
  * 回调函数
  * Promise
  * async
  * generator
* Express web开发框架
* ECMAScript 6
  * 在课程中穿插讲解
  * 它只是一个新的语法
* 。。。
* 学习node不仅会帮助打开服务端黑盒子，同时有助于学习高级内容
  * Vue.js
  * React
  * angular

### 2. 起步

#### 2.1. 安装Node

* 查看当前Node环境的版本号
* 官网下载，LTS(长期支持版)稳定版，Current体验版
* 安装，一直下一步
* 查看，打开命令行 cmd，输入`node --version` 或者`node -v` 查看版本
* 环境变量

#### 2.2 node练习

* node只有js文件，代码一般为 .js文件

1. 创建编写js文件
2. 打开终端（命令行），定位到脚本文件所属目录
3. 输入`node 文件名`执行对应的文件，显示结果

==注意：文件名不能使用`node`，最好也不要使用中文==

* 解析执行JavaScript

* 读写文件

  * 使用`fs`模块

    ```javascript
    var fs = require('fs');
    ```

  * 读取文件

    ```
    fs.readFile('路径', '可选编码', function (error, data) {});//具体查看代码
    ```

  * ==readFile方法的第二个参数为可选参数，规定解析文件的编码，可选择`utf-8`编码，让他以网页原文件的形式读取，不是二进制数据==

  * 写文件

    ```
    fs.writeFile();
    ```

==Node.js中常常使用回调函数作为参数，因为是异步的==

* HTTP
  * 详见代码。
  * ==服务器和web页面一般使用字符串进行通信==，因此json的传递需要转化为字符串 
  * `Ctrl`+`c`可以关闭服务器（bash和cmd一样）
  * 在浏览器查看http请求中，有一个`/favicon.ico`路径，该请求是浏览器的默认行为，目的是请求网页的网站头像
  * ==响应内容只能是字符串或二进制数据（Buffer）。对象、数字、数组、布尔值都不行==，**因此`response.end()`方法能够响应字符串和二进制数据**


### 3. Node中的JavaScript

### 什么是模块化

- 现实中的模块化
  + 生产效率高
  + 维护方便，成本低
- 程序中的模块化
  + 开发效率高 一次编写多次使用
  + 方便维护了（维护的成本更低）模块之间有高耦合低内聚的特点

- 为什么要在 程序 中使用 模块化的开发方式
  + 命名冲突
  + 文件依赖





#### 3.1  Node中使用模块

* ECMAScript
  * 没有DOM、BOM
* 核心模块
* 第三方模块
* 用户自定义模块

#### 3.2 核心模块

* Node为JavaScript提供了很多服务器级别的API，这些API绝大多数都被包装到了一个具名的核心模块中了。

  * 例如文件操作的`fs`核心模块，http服务构建`http`模块，`path`路劲操作模块、`os`操作系统信息模块。

  * 只要提到一个核心模块，就要想到引入该模块。使用require[^1]。

    ~~~javascript
    var fs = require('fs');
    var http = require('http');
    ~~~

    [^1]: ==require就是用来引入模块的==

* ==模块的好处：可以完全避免变量命名冲突污染的问题==

* 但是模块之间的通信需要使用模块中的`exports`对象

* ==使用的所有文件操作的API都是异步的，即核心模块fs的方法。执行顺序可能要放到其他执行过程的后边执行==

#### 3.3 用户自定义模块

​	Node.js不能同时执行多个js文件，需要使用模块化编程

* 模块化相当于同一个文件中的script标签
* ==模块的功能要单一==


* require

  * require是一个方法，作用是用来加载模块的
  * 在Node中，模块有三种
    * 具名的核心模块，例如fs、http
    * 用户自己编写的模块（就是一个js文件），可以使用require方法加载。==相对路径必须加`./`==，否则会报错，因为会将模块认定为一个核心模块

  在node中没有全局作用域，只有==模块作用域==，相当于两个script标签。require方法加载时可以省略后缀名的。

  * 推荐使用省略后缀名的写法
  * 既然是模块作用域，如何让模块与模块之间通信

  require方法有两个作用：

  1. 加载文件模块并执行里面的代码
  2. 拿到被加载文件模块导出的接口对象



​	每个文件模块中都提供了一个对象：exports

​	exports默认是一个空对象，是require方法的返回值



* exports
  * exports用来实现不同模块之间的通信
  * ==每个模块中都提供了一个exports对象，默认是一个空对象==
  * 该对象类似于原型链中的prototype对象，能够向对象中添加数据，另一个模块可以通过`require`方法返回的exports对象使用这些添加的数据

### 4. web服务器开发

####4.1端口的概念

计算机中只有一个物理网卡，而且同一个局域网中，网卡的地址必须是唯一的。

* 网卡是通过唯一的ip地址来进行定位的

- ip地址用来定位计算机
- 端口号用来定位具体的应用程序（所有需要联网通信的软件都必须具有端口号），通信的软件占用
- 端口号的范围从0~65536之间
- 在计算机中有一些默认的端口号，最好不要去占用
  * 例如http服务的80 



* 可以同时开启多个服务，但一定要确保不同服务占用的端口号不一致。同一个端口号只能被一个程序占用
* ==目前发现，node的网络编程主要是通过request和response两个对象来实现的。一般就是监听request事件来获取客户端和服务端的数据。==
* ==request代表着客户端的信息，response代表着服务器的信息==
* 开启服务器流程

```javascript
var http = require('http')

//1.  创建server
var server = http.createServer()

//2.  监听Server的request请求事件，设置请求处理函数
		// 请求
			// 处理
		// 响应
		// 一个请求对应一个响应，如果一个请求过程中，已经结束响应了，则不能重发发送响应。
		// 没有请求就没有响应

server.on('request', function (req, res) {
	console.log(req.url)
})

// 3.绑定端口号，启动服务

server.listen(3000, function () {
	console.log('server running');
})
```



#### 4.2 Content-Type


* 在服务端默认发送的数据，其实都是utf-8编码的内容
  但在浏览器不知道你是utf-8编码的内容
  浏览器在不知道服务器响应内容的编码情况下会按照==当前操作系统的默认编码==去解析
* ==在http协议中，Content-Type就是用来告知对方我给你发送的数据内容是什么类型==，响应和请求都可以设置
  * Content-Type的值：
    * `text/plain`为普通文本，浏览器不会解析
    * `text/html`为html文本，浏览器会当做html文件解析该文本
    * 图片不需要指定编码，编码一般指定的是：字符编码

==网址http://tool.oschina.net能够查询文件对应的Content-Type==

==URL==：统一资源定位符，一个url对应一个资源

### 5. 注意事项

* node不管什么模块，涉及到路径的都是从app所在的目录为起点的

## Node.js第二天

### 1. 代码规范问题

* 采用无分号代码风格的时候，需要注意一下情况：

  * 当一行的代码是以：`()`、`[]`、`` `[^1]  为开头，需要在这行代码的前面加上分号防止语法错误

    [^1]: ==```` ``` 是ES6中新增的一种字符串包裹方式，叫做：模板字符串。它支持```换行```和非常方便的拼接变量==

  * 在ES6中使用`` `（反引号）包围的字符串时，字符串中拼接变量不用使用+变量+的形式，直接在字符串中使用${}来引用变量

    ```javascript
    str = `adadadadd${str1}ppppppp`;
    ```

    

  ~~~javascript
  say()
  ;(function(){//此处需要加分号
      
  })()//这种形式绝对不会报错
  ~~~

  * 能在一些第三方开发的代码中一上来就以一个`;`开头。
  * 不论使用什么风格的代码，在符合上述情况的时候，最好都在开头加上`；`
  * 有些人也使用`!`或者`~`等

  ```javascript
  //连续调用两个方法,规范如下
  http
  	.createServer()
  	.listen()
  ```

  

* ==进程是资源的分配和调度的一个独立单元，而线程是CPU调度的基本单元==


### 2. 目录斜杠问题

* 字符串目录一般采用斜杠`/`，也可以使用`\\`，`\`反斜杠的作用是用来转义
* Windows的目录采用了`\`来显示目录，这点与字符串和网址的url相反

### 3. Node 安装第三方库

* 在需要安装的目录下  输入npm install +包名，安装到执行目录中新建的node_modules目录下
* 一般安装在与服务器代码同级目录下
* node_modules目录不能够修改

### 4. 服务器端渲染

* 在服务端使用模板引擎（见代码）
* 浏览器端的渲染在script标签中进行，服务端的渲染在服务器端进行，服务器渲染是可以减少请求，提升页面性能的，在实际操作时视情况选用
* 客户端渲染不利于SEO（爬虫查找）搜索引擎优化，例如ajax等异步渲染（想想全部刷新的页面一般都是服务器端渲染）
* 服务端渲染是可以被爬虫抓取到的，客户端异步渲染是很难被爬虫抓取到的
* 真正的网站是异步和服务端渲染相结合的
* 例如京东的商品列表，服务器喧嚷是为了SEO搜索引擎优化；商品评论为了用户体验，而且也不需要SEO优化，所以采用客户端渲染
* 服务器渲染一般会刷新整个页面，页面源码会看到呈现的文字（方便SEO）；客户端渲染一般不会刷新页面。
* ==服务端和浏览器端使用模板引擎有本质区别，浏览器端是将要解析的模板放在script标签找中的，服务器是将整个web页面当做要解析的字符串，因此服务器端使用模板引擎会在没有解析时暴露源码内容，浏览器端使用模板引擎则不会出现这种情况==

###5. 浏览器解析静态资源

* js的执行会阻塞DOM树的解析和渲染
* css的加载不会阻塞DOM树的解析，会阻塞DOM树的渲染

******

* 浏览器收到HTML响应内容之后，会开始从上到下一次解析

  当在解析的过程中，如果发现：

  ​	link

  ​	script

  ​	img

  ​	iframe

  ​	video

  ​	audio

  等带有src或者是href（a标签除外）属性的标签（具有外链的资源标签）的时候，浏览器会自动对这些资源发起新的请求

### 6. 开发文件规范问题（处理网站中的静态资源）

* 为了让目录结构保持统一清晰，约定把所有的HTML文件都放在views目录下
* 为了方便的统一处理静态资源（css,img,js,第三方资源），我们约定把所有的静态资源都存放在public目录下
  * css目录中存放css文件
  * img目录下存放图片文件
  * js目录中存放js文件
  * lib目录下存放第三方资源
* 使用代码控制，可以控制用户访问哪些资源。即可以访问index.html和public目录中的资源
* ==客户端的请求路径不再使用文件路径了（相对路径），web页面的请求url一般使用`/`（根目录）的url地址来引用资源。服务端代码的路径还是相对路径==
* 浏览器在真正发送请求的时候会自动把 http://127.0.0.1:3000 拼上，所以在浏览器显示的请求路径一律写成根路径形式，例如`/public`，这种形式叫做url路径


### 7. url模块

* url模块能够用于 URL 处理与解析
  * `url.parse()`方法能够解析请求网址，返回一个包含各个请求数据的对象。==记住第二个参数设置为true==
  * 该方法返回的路劲也为端口号后面的路径

==网页中的路径都是url路径，不是文件路径==

### 8. ==服务器让客户端重定向==

* 如何通过服务器让客户端重定向，重定向就是为了减少代码的书写

  1. 状态码设置为302临时重定向（还有个301为永久重定向）

     ​	通过res.statusCode

  2. 在响应头中通过Location告诉客户端往哪重定向

     ​	通过res.setHeader来

  3. 设置完重定向之后一定要关闭请求，res.end()

  ```javascript
  res.statusCode = 302
  res.setHeader('Location', '/')
  res.end()
  ```

  

  如果客户端发现收到服务器的响应的状态码是302，就会自动去响应头中找Location，然后对该地址发起新的请求。所有就可以看到客户端自动跳转

* 301永久重定向 浏览器会记住

  * 比如访问a.com 会跳转至b.com 下次浏览器不在向a.com发请求会直接跳转至b.com

* 302 临时重定向 浏览器不会记忆

  * a.com临时，下次浏览器还会请求a.com  然后告诉浏览器访问b.com。每次请求都尝试一下

### 9. Node中使用Console测试

* 在任何目录下打开cmd，输入node回车即可进入console控制台，可以在console中测试方法
* 在node的console中所有核心模块可以直接使用，不需要require引入
* 按Ctrl+c两次即可退出console
* 这个专用名称是REPL
  * read
  * eval
  * print
  * loop


### 10.Node 常用指令

* 目前就一个node 进入node环境执行

## 11. 注意：

* ==原生的node 用 res.end() 来结束请求；express 框架用 res.send() 结束请求。==

## Node.js 第三天

### 知识点

* 模块系统
  * 核心模块
  * 第三方模块
  * 自己写的模块
  * 加载规则以及加载机制
  * 循环加载的问题
* npm
* package.json
* Express
  * 第三方web开发框架
  * ==高度封装了http模块==
  * 提高编码效率，更加专注于业务，而非底层细节
  * 学习过程知其所以然
* 增删改查
  * 使用文件来保存数据（锻炼异步编码），遇到业务能够自己封装
* MongoDB（数据库）
  * （所有方法都封装好了）

### 软件版本

* 涉及到软件工程知识
* x.x.x
  * 第一个数字代表大版本，新增功能比较多，甚至可能去除了某些功能
  * 第二个数字：加入了新功能
  * 第三个数字：修复bug，提升了性能
  * 一般是客户端软件、技术框架开发者比较理解的多
  * 做网站很少涉及到版本的概念，网站目的就是快

### each和forEach解析

* jquery中each使用，一般用于遍历jQuery选择器选择到的伪数组实例对象
  * $.each(数组，function(index,element){})，该方法一个参数为要操作的数组，另一个为具体执行函数
  * $(selector).each(function(index,element){})，该方法为选取元素执行函数
  * jQuery的function中的参数与原生的forEach方法刚好相反
  * each方法的作用
    1. 方便的遍历jquery元素的
    2. 可以在不兼容forEach的低版本浏览器中使用jQuery的each方法
* forEach(function(item,index){})为原生js中的方法，可以遍历任何可以被遍历的成员
* ==forEach只能够遍历数组，each可以遍历jquery获取到的伪数组==
* jQuery的each方法支持低版本的IE浏览器，ECMAScript5中的forEach方法不支持IE8版本以及以下的浏览器
* ==$('div')的jQuery获取元素的方法，返回的是一个伪数组，伪数组是对象不是数组，不能够使用forEach方法，forEach()是Array.prototype原型链中的方法==


### 伪数组转化为数组

* 伪数组使用`[].slice.call(eles)`可以使用数组中的方法
* 伪数组是一个key为数字的对象


### node和PHP

* php是文件型的
* node定制性更强
* node比较底层，很多功能需要自己实现。
* 在Node中开启的服务器，默认是黑盒子，所有资源都不允许用户来访问，用户可以访问哪些资源具体有开发人员编写设计的代码为准。可以处理url，php则会显示index.html的形式

### 1.Node中的模块系统

使用node编写应用程序主要就是在使用：

* ECMAScript语言
* 核心模块
  * 文件操作的fs
  
  * http服务的http
  
  * url路径操作系统
  
  * path路径处理模块
  
    path.reslove() 方法会从右到左一直匹配直到匹配到 `/name ` 以`/`开头的字符串。如有一直都没有匹配到 `/` 开头的字符串，则会自动拼接当前的工作目录。
  
  * os操作系统信息
* 第三方模块
  * art-template
  * 必须通过npm来下载可以使用
* 自己写 的模块
  
  * 自己创建的模块

#### 1.1 什么是模块化

* 具有文件作用域
* 通信规则
  * 加载 require
  * 导出 
* ==模块的好处：可以完全避免变量命名冲突污染的问题==

#### 1.2 CommonJs模块规范

在Node中的js还有一个重要的概念：模块系统

* 模块作用域
* 使用require方法用来加载模块
* 使用exports接口对象用来导出模块中的成员

如果一个模块需要直接导出某个成员，而非挂载的方式，需要使用下面的方式

```javascript
module.exports = 'hello'//这样另一个模块加载这个模块就会返回'hello'，而不是exports对象
```

##### 1.2.1 加载 `require`

语法：

```javascript
var 自定义变量名 = require('模块')
```

两个作用：

* 执行被加载模块中的代码
* 得到被加载模块中的`exports`导出接口对象

##### 1.2.2 导出`exports`

* Node中是模块作用域，默认文件中所有的成员只在当前文件模块中有效
* 对于希望可以被其他模块访问的成员，我们就需要把这些公开的成员都挂载到`exports`接口对象中

导出多个成员（必须在对象中）：

```javascript
exports.a = 123
exports.b = {
    foo : 123
}
```

导出单个成员(拿到的就是：函数、字符串)：

```javascript
module.exports = 'hello'
module.exports = function(){//后者会覆盖前者
    
}
```

因此，也可以如下方式导出多个成员：

```javascript
module.exports = {
    //加成员
}
```

##### 1.2.3 原理解析

* 可以认为在node中，没一个模块都有一个自己的`module`对象
* module对象中，有一个成员叫：`exports`成员也是一个对象
* 也就是说需要对外导出成员，只需要把导出的成员挂载到`module.exports`对象中

```javascript
var module = {
    exports : {
        
    }
}
//谁require这个模块，就会得到 module.exports
//为了简化操作，在模块中还有一句代码
var exports = module.exports  //exports和module.exports挂载是一样的。这行代码应该是在第一行
console.log(exports === module.exports)//结果为true
//默认在代码的最后又一句：
return module.exports
//一定要记住return的是module.exports，不能通过直接修改exports影响返回的对象，因此导出单个成员需要修改module.exports对象
```

* exports是`module.exports`的一个引用

##### 1.2.4 require方法加载规则

* 优先从缓存加载

* 不会重复加载相同的模块

* 可以拿到其中的接口对象，不会重复执行模块中的代码

* 这样做是为了避免重复加载模块，提高加载效率

* require的参数为==模块标识==

* ==加载时要判断模块标识==

  1. 核心模块
  2. 第三方模块
     * 凡是第三方模块都可以通过`npm`来下载
     * 使用的时候可以同require('包名')的方式进行加载才可以使用（非路径形式）
     * 不可能有一个第三方名字与核心模块名字相同
     * 查找规则（以art-template模块为例）
       1. 先找到当前文件所处目录中的node_modules目录（这个目录就是用来存放第三方模块的）
       2. 找node_modules/art-template
       3. 找node_modules/art-template/package.json 文件
       4. 找node_modules/art-template/package.json文件中的“main”属性
       5. mian属性中就记录了art-template的入口模块
       6. 然后加载使用这个第三方包，实际上最后加载的还是文件
       7. 如果package.json文件不存在或者main指定的入口模块也没有，则node会自动找该目录下的index.js作为默认备选项
       8. 若果以上所有任何一个条件都不成立，则会进入上一级目录中的node_modules目录查找，规则如上
       9. 如果上一级还没有，则继续往上上一级查找，知道磁盘根目录还找不到，最后报错：`can not find module xxx`，==反正要找到`node_modules`目录==
  3. 自己写的模块

  ==注意：==

  * 路径形式的模块采用`./`，当前目录不可省略，`../``上一节目录不可省略
  * 核心模块的加载按名字来加载，核心模块的本质也是文件。核心模块的文件已经被编译到二进制文件中了，我们只需要按照名字来加载就可以了
  * 一个项目有且只有一个node_modules，不会出现多个node_modules。该目录放在项目同目录（项目根目录）中，这样的话项目中的代码都可以加载到第三方包。
  * 自定义模块的加载规则和 node_mudles 加载规则一样，找到 package.json 文件中的 main 属性对应的的入口，没有找到的话，默认找该文件夹下的 index.js 文件作为入口。

#### 1.3 npm

* node package manager，是一种命令行工具
* npm install art-template jquery bootstrap（可以一次性装几个包）
* install 也可以用 `i`代替

##### 1.3.1 npm网站

> npmjs.com  npm官方网站，可以查询包和发包

##### 1.3.2 npm命令行工具

npm第二层含义就是一个命令行工具，只要你安装node就已经安装好了npm。

npm也有版本的概念（独立的软件）

* npm查看版本输入：

```javascript
npm --version 
npm -v
```

* npm升级

```javascript
npm install --global npm//自己升级自己
```

* npm初始化

```javascript
npm init
npm init -y//可以跳过向导，快速生成
```

* npm install
  * 一次性把dependencies选项中的依赖项全部安装
  * ==npm i         install可以使用i简写代替==
* npm install 包名
  * 只下载
  * npm i 包名
* npm安装指定的版本
  * npm i bootstrap@版本号 （中间没有空格）
* npm install --save 包名
  * 下载并保存依赖项（package.json文件中的dependencies选项）
  * npm i -S 包名
* npm uninstall 包名
  * 只删除，如果有依赖项会依然保存
  * npm un 包名
* npm uninstall --save 包名
  * 删除的同时也会把依赖信息也删除
  * npm un -S 包名
* npm help
  * 查看使用帮助
* npm 命令 --help
  * 查看指定命令的帮助
* --global 为全局安装，安装到node的安装目录下，一般用于影响操作的软件更新或替换
* npm install 包名 --save-dev 这样安装的包和版本号就会存在 package.json 的 devDependencies 这个里面，--save 会会存在 dependencies 里面。简写为 -D

##### 1.3.3 解决npm被墙问题

* npm速度很慢，http://npm.taobao.org/ 淘宝的开发团队吧npm在国内做了一个备份
* 安装淘宝的 cnpm：

```shell
# 在任意目录下执行都可以
# --global表示安装到全局，而非当前目录，因此在任意目录都可以执行
# --global不可以省略
npm install --global cnpm
```

​	接下来你安装包的时候吧之前的npm替换成    `cnpm`

​	举个例子：

```shell
# 这里走国外的npm服务器，速度较慢
npm install jquery

#使用淘宝的服务器下载：jquery
cnpm install jquery
```

如果不想安装`cnpm`又想使用淘宝的服务器来下载：

```shell
npm install jquery --registry=https://registry.npm.taobao.org
```

但是每一次手动这样加参数很麻烦，所以我们可以把这个选项加入到配置文件中：

```shell
npm config set registry https://registry.npm.taobao.org
# 查看 npm 配置信息
npm config list
```

只要经过了上面命令的配置，则你以后所有的`npm install`都会默认通过淘宝的服务器来下载。

#### 1.4 package.json

* package.json，包描述文件，包的说明书，说明包引用了什么依赖
* npm install art-template --save（放到包后面）或者npm install --save art-template （放到包前面，前后都可以），package.json文件中多了一个属性`"dependencies"`，该属性的值的对象中保存着项目的依赖（依赖什么插件、插件的版本）
* ==建议每一个项目都有一个package.json文件，就像说明书==
* package.json文件可以通过`npm init`的方式自动初始化出来，`npm init`会以向导的方式（问一句答一句），提问项如下（不填的直接回车）：
  * name：
  * version
  * description:（项目的描述）
  * entry point:(index.js) （项目的入口一般使用`main.js`作为入口，与json文件同目录）
  * test command:（测试命令）
  * git repository:（github地址）
  * keywords:
  * author:作者
  * license: (ISC)（许可证）
  * --save 第三方包会出现"dependencies"属性
* 目前最有用的是"dependencies"选项，包含第三方包的依赖信息
* 建议执行`npm install +第三方包名`都加上`--save`这个选项，用来保存（项目依赖信息）
* 如果node_modules删除了，只要"dependencies"选项中保存有依赖包，下次直接npm install来加载包即可

==补充：==

npm 5 以前是不会有`package-lock.json`这个文件的

npm 5 之后才加入这个文件

当你安装包的时候，npm 都会生成或者更新`package-lock.json`文件

*******

### 2. Express

* experss : 表达，快速的

原生的==http==在某些方面表现不足以应对我们的开发需求，所以我们就需要使用框架来加快我们的开发效率，框架的目的就是提高效率，让我们的代码更高度统一。

* ==注意 express 也可以使用原生的方法==


* Express 是一个简洁而灵活的 node.js Web应用框架, 提供了一系列强大特性帮助你创建各种 Web 应用，和丰富的 HTTP 工具。使用 Express 可以快速地搭建一个完整功能的网站。


* 在Node中，有很多的web开发框架，我们这里以学习`express`为主。
* http://expressjs.com/

```javascript
//先建一个app.js
// 安装依赖
// 初始化应用
// 引包开发

var express = require('express')

// 2.创建你的服务器应用程序
// 也就是原来的 http.createServer

var app = express()   //相当于原来的server
//当服务器收到get请求/的时候，执行回调处理函数。必须为get请求
app.get('/', function (req, res) {
	res.send('hello world')
})
//不需要一个一个再判断了
app.get('/about', function (req, res) {
	res.send('你好') //此处的编码问题框架已经帮我们处理好了，不需要res.setHeader()
})
//相当于server.listen
app.listen(3000, function () {
	console.log('app is running at port 3000.')
})
```

* express找不到的路径，会返回Cannot GET /a   (比如说‘a’)  页面，同时返回状态码404
* express的get方法判断的路径为纯路径，不带传递的参数和锚等内容
* ==app.get()方法会返回app对象，可以链式编程，继续执行get方法==

#### express的request对象

```javascript
//req对象中直接包含了 原生 url 模块中返回的方法
req.query   //返回参数对象
```

* request对象的其他属性和方法可以查看文档 http://www.runoob.com/nodejs/nodejs-express-framework.html

####express公开指定的目录（处理静态资源）

```javascript
// 公开指定目录
//只要这样做了，就可以直接通过 /public/xx 的方式访问 public 目录中的所有资源了
app.use('/public/', express.static('./public/'))  //相当于拼接的'. + 路径'
```

* 不公开目录默认找不到路径处理。
* 上述代码公开的是public文件夹下的文件，因此次用`‘/public/’`的形式，也可以使用`/public`，它只是在检测url是否以你定义的开头
* ==浏览器的访问路径只有在端口号后没有'/'的时候补一个‘/’，浏览器在文件路径后面补一个‘/’，其他的路径都是在浏览器地址栏看到的路径，没有区别。==

总结：

* get方法是用来判断访问路径，作出处理的
* use方法是用来开发静态资源的。
* ==浏览器路径确认是文件，会在后面自动拼接一个`/`==
* 在Express中开放资源就是一个API的事儿；模板引擎在Express中也是一个API的事儿

### 伪数组

* 一般伪数组的形式：(如下的一种对象)

```javascript
var fakeArr = {
    0 : 'abc',
    1 : 'efg',
    2 : 'aaa',
    length : 3
}
```

## Node.js 第四天

### 知识点

* express
* 基于文件做一套 CRUD
  * C - Creation 增加 
    R - Retrieve 查询 
    U - Update 修改 
    D - DELETE 删除 


### 文件路径和模块标识

* 文件路径`/`为磁盘根目录
* 模块标识的`./`中的`.`不能省去

### 1. 修改完代码自动重启

​	我们这里可以使用一个第三方命令行工具：`nodemon`来帮助我们解决频繁修改代码重启服务器问题

​	`nodemon`是一个基于Node.js开发的一个第三方命令行工具，我们使用的时候需要独立安装：



```shell
npm install --global nodemon
```

安装完毕之后，使用：

```shell
node aap.js

# 使用 nodemon启动app
nodemon app.js
```

只要通过`nodemon app.js`启动服务，则他会自动监视你的文件变化。当文件发生变化的时候，自动帮你重启服务器。

### 2. express 基本路由（router）

路由（routing）是指分组从源到目的地时，决定端到端路径的网络范围的进程。

* 路由其实就是一张表。我们已经了解了 HTTP 请求的基本应用，而路由决定了由谁(指定脚本)去响应客户端请求。
* 这个表里面有具体的映射关系
  * 当请求'/'，执行什么语句，分配什么
* ==app.get()方法会返回app对象，可以链式编程，继续执行get方法==
* express 中的get方法就相当于一个路由，以什么路径执行的时候，执行对应的处理函数，就是一个路由处理
* ==千万不要纠结端口后面的请求url是否加`/`开头，不加也可以==

路由器（映射关系）:

* 请求方法
* 请求路径
* 请求处理函数

get:

```javascript
//当以 GET方法请求 / 的时候，执行对应的处理函数
app.get('/', function (req, res) {
    res.send('hello')
})
```

post:

```javascript
//当以 POST 方法请求 / 的时候，执行对应的处理函数
app.post('/', function (req, res) {
    res.send('hello')
})
```

* 同一路径使用不同的请求方式，就可以让同一个路径被多次使用

### 3. express静态服务

1. 当以`/public/`开头的时候，添加`.`去`./public/`目录中寻找对应的资源

```javascript
app.use('/public/', express.static('./public/'))
```

2. 当省略第一个参数的时候，则可以通过省略  /public  的方式来访问。直接写该目录下的资源路径即可，例如public目录下的index.html，url为 127.0.0.1:3000/inde.html

```javascript
app.use(express.static('./public/'))
```

3. 当指定第一个参数的时候，必须是/a/为开头取代 /public/ 访问public中的资源，是为了方便指定定制访问路径。例：1207.0.0.1:3000/a/index.html  才能访问如下的开放目录。

```javascript
app.use('/a/', express.static('./public/'))
```

4 第四种还没学

```javascript
app.use('/static', express.static(path.join(_dirname, './public/')))
```



web浏览器中的路径为  URL，不是传统的路径，是一个定位符

### 4. 在express中配置使用`art-template`模板引擎

* [art-template](https://aui.github.io/art-template/zh-cn/docs/)
  * 注：上边的语法为[]+()

安装

```shell
npm install --save art-template
npm install --save express-art-template
```

配置：

```javascript
app.engine('art', require('express-art-template'))
// 第一个参数标识，当前以 .art 结尾的文件的时候，使用art-template模板引擎
// 也可以把第一个参数改写为html
app.engine('html', require('express-art-template'))
```

使用：

```javascript
// Express 为 Response 响应对象提供了一个方法 ： render
// render 方法默认是不可以使用的，但是如果配置了模板引擎就可以使用了
// res.render('html模板名', {模板数据})
// render方法中第一个参数不能写路径，默认会去项目中的 views 目录查找该模板文件
// 也就是说 Express 有一个约定：开发人员把所有的视图文件都放到 views 目录中 
// Express 默认查找目录就是 views 目录
// 呈现视图并将呈现的HTML字符串发送到客户端
app.get('/', function (req, res) {
	res.render('404.art')
})

// 如果想要修改默认的views 为别的目录
// app.set('views', 'render函数的默认路径')
app.set('views', './views')


// 查找views 目录下的文件
app.get('/admin', function (req, res) {
	res.render('admin/index.html', {
		title : '管理系统'
	})
})
```

==当需要直接返回页面的时候，也可以使用render方法==

```javascript
//当需要返回路径的时候
app.get('/', function (req, res) {
    res.render('index.html')
})
```

### 5. express 中重定向的使用

```javascript
//原生的重定向
res.statusCode = 302
res.setHeader('Location', '/')

//express 中封装的重定向
res.redirect('/')
```

### GET和POST请求获取数据

* `res.query`是通过查询字符串来获取参数，只能获取get请求参数

1.  get请求数据在请求行中，post需要设置请求行
2. GET没有请求体，post的请求数据在请求体中

#### 在express 中获取表单 POST 请求体数据

* 在Express 中没有内置获取表单 POST 请求体的API，这里我们需要使用一个第三方*中间件*：`body-parser`

安装：

```shell
$ npm install body-parser
```

配置：

```javascript
var express = require('express')
//0.先引包
var bodyParser = require('body-parser')

var app = express()

//配置 body-parser
//只要加入这个配置，则在 req 请求对象上会多出一个属性：body
//也就是说你可以直接通过 req.body 来获取表单 POST 请求体数据了
// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }))

// parse application/json
app.use(bodyParser.json())

//使用
app.use(function (req, res) {
  res.setHeader('Content-Type', 'text/plain')
  res.write('you posted:\n')
  //可以通过 req.body 来获取表单 POST 请求数据
  res.end(JSON.stringify(req.body, null, 2))
})
```

#### `fs.readFile()`方法补充

```javascript
//fs.readFile()方法读取到的文件为二进制数据，如果想要解析该二进制数据，可以使用toString()方法，也可以使用readFile()方法第二个参数传值为'utf-8'
```

==`SyntaxError`表示语法错误==

### 6. express 中设置状态码

* express的响应对象`response`中有一个方法`status()`可以通过传递参数来设置响应码，该方法还可以链式编程，

```javascript
res.status(500).send('hello')
```

==数据库中的性别`gender`一般使用0或1代替==

### 7. express注意

* express没有提供读取文件的api，主要封装了http操作的api

* fs模块是异步的，因此在写读取文件的时候，最好在当前的http操作函数中读取文件。相当于setTimeout()方法

  例如：![fs模块](E:\xuexi\DW\web lx\study img\fs模块.PNG)



### 8. Express-crud 路由设计

* 路由设计就是请求不同的url，响应不同的结果
* 路由的设计：考虑请求方式、请求路径、请求参数。

| 请求方式 |     请求路径     | get请求 |            post请求            |     备注     |
| :------: | :--------------: | :-----: | :----------------------------: | :----------: |
|   GET    |    /students     |         |                                |   渲染首页   |
|   GET    |  /students/new   |         |                                | 渲染新增页面 |
|   POST   |  /students/new   |         |   name、age、gender、hobbies   | 处理新增请求 |
|   GET    |  /students/edit  |   id    |                                | 渲染编辑页面 |
|   POST   |  /students/edit  |         | id、name、age、gender、hobbies | 处理编辑请求 |
|   GET    | /students/delete |   id    |                                | 处理删除请求 |

路由的文件采用模块化开发，需要单独写一个模块。

* 路由模块放在app入口模块的同级目录

express 提供了一种==包装路由==的方法：

```javascript
//1. 创建一个路由容器
var router = express.Router()
//2. 把路由都挂载到 router 路由容器中
router.get('/', function(){})
......
//3. 导出router容器
module.exports = router
```

在app文件中使用router容器：

```javascript
//1. 导入路由容器文件
var router = require('./router')
//2. 把路由容器挂载到app服务中
app.use(router)
```

==模块的职责要单一，不要混写。划分模块的目的就是为了增强项目代码的可维护性，提升开发效率==

注意：

==配置模板引擎和body-parser 一定要在 app.use(router) 挂载路由之前，才能正常执行==

### 9. 获取函数异步操作的结果（封装异步API）

* 获取一个函数中异步操作的结果，则必须通过==回调函数==来获取
* 回调函数的关键作用

例如：

```javascript
function fn (callback) {
    setTimeout(function () {
        var data = 'ddd'
        callback(data)
    }, 1000)
}
fn(function (data) {
    console.log(data)
})
```

只要是获取函数中的异步函数的数据（函数中还有执行的函数），都可以采用上述方法。通过引用类型可以获取到异步操作的结果。（试一下对象行不行）  实际运行的过程中是上面的fn函数中代用了回调函数。

注意：

==callback的运行一定要写在函数的最后一行，要不然会阻碍后边代码的执行==

### 10. ES6 初识

ECMAscript 6 中的一个数组方法：find

该方法需要接收一个函数作为参数

当某个遍历项符合回调函数中的遍历条件时，find会终止遍历，同时返回遍历的成员。只要对比项和数组中的某一项相同时，就会返回数组中的某一项。

```javascript
var stu = students.find(function (item) {
    return item.name === student.name
})
```

ECMAscript 6 中的一个数组方法：findIndex()，同上

### 11. 数字和字符串之间的转换

* 数字转换成字符串

  使用：toString方法，只能字符串使用

* 字符串转化为数字

  使用：parseInt()方法

* 十进制转化成其他进制，要使用toString()方法

* 任何进制（包括字符串）转化成十进制都要使用parseInt()方法

### ==浏览器端渲染和服务器端渲染==

表单请求的post不需要设置请求头。

浏览器脚本请求需要设置请求头。

post只是一种请求！不是响应

请求方式

 	1. 表单
	​	2. a 标签，href 属性
	​	3. 脚本（ajax,http）

1和2属于采用服务器端渲染的方式，3属于浏览器端渲染的方式。

* 说白了就是 服务器端加载时服务器处理完成返回一个 html 页面直接渲染就可以呈现。浏览器端加载时先返回一个 HTML 页面，然后在加载的过程中渲染需要使用的功能。

### 服务器端使用 template 和浏览器端使用 template 的区别

* 浏览器端是先写好 HTML ，引入一个 template 区域来加载。加载的过程中从 js 中提取数据。
* 服务器端是 直接写好模板页面的字符串，传递给处理程序，然后再返回数据，浏览器加载。
* 两种加载都是渲染字符串的形式。处理完成返回字符串，渲染到页面上。

## Node.js 第五天

JavaScript的异步操作是因为其单线程、事件循环的底层机制。

### 1. 异步编程

注意：凡是需要得到一个函数内部异步操作的结果

* setTimeout
* readFile
* writeFile
* ajax

这种情况必须通过：回调函数来获取数据

* JavaScript中的事件一般都是等代码执行完成之后才会执行的，因此函数中有绑定事件的，获取事件中的数据一般要使用callback 回调函数的形式

### 2. 浏览器中的模块化

浏览器中可以使用第三方库实现模块化开发，js天生不支持模块化

* require.js 第三方库 AMD
* sea.js 第三方库 CMD

PHP直接支持`require`、`include`，因为PHP当初设计时加入了这个功能

* 模块作用域
* 可以使用API 来进行文件与文件之间的依赖加载

node.js中的模块化 CommonJS

* CommonJS、AMD、CMD 都是民间搞出来的
* EXMAScript 是官方规范定义。官方解决在不同的环境使用不同的 JavaScript 模块化解决方案，所有ECMAscript 在2015年发布了 ECMAscript 2016 官方标准，支持了模块化。虽然标准已经发布，但是有的 js 运行环境还不支持

学vue 的时候会去学习，例如：

* less 编译器 > css
* es6 编译器 > es5

目前前端的情况就是使用很多新技术，然后利用编译器工具打包可以在低版本浏览器中运行。

使用新技术的目的就是为了提高效率，增加可维护性

###3. app.use()

* app.use() 涉及到中间件的问题
* 不仅可以用来处理静态资源，还可以配置中间件等等，很多工作
* 中间件有一套规则 

### 4. 新版本的npm特性（npm5 以上）

1. 新版本的的npm不需要加 --save 命令
2. uninstall 不加--save 会删除依赖
3. 当安装包的时候，会自动创建或者是更新`package-lock.json`这个文件
4. `package-lock.json`文件中保存着所有依赖树（所有包）的信息，下载地址，删除node_modules文件之后能够根据该文件快速下载依赖。
   * 这样重新`npm install`的速度就会提升
5. 从文件来看有`lock`，是用来锁定版本的
   * 如果项目依赖了某第三方库的 1.1.1 版本，如果重新安装有可能升级这个库，而`package-lock.json`会锁住依赖的版本，不会出现升级造成项目不可用的情况

### 5.条件函数的使用（封装数组 find 方法）

* 不论条件函数还是回调函数，函数作为参数的情况，都接收的是整个函数，参数写成 callback 或者 conditionFunc ，不能写成callback(a,b)这样的形式

例：

````javascript
//条件函数的使用
//封装数组的 find 和 findIndex 方法
Array.prototype.myFind = function (conditionFunc) {
    for (var i = 0; i < this.length; i++) {
        if (conditionFunc(this[i], i)) {
            return this[i]
        }
    }
}

var ret = [1,2,3].myFind(function (item, index) {
    return item === 2
})
````

数组中的遍历方法，都是对函数作为参数的一种应用。数组中遍历的方法都有：

* every
* some
* includes
* map
* reduce

### 6. sublime 中设置 js 代码高亮

* ctrl + shift + p , 快捷键 set syntax javascript 

### 7. Express 定制 404 页面

* express 定制404页面需要通过中间件来配置

* 只需要在自己的路由之后增加一个

  app.use(function (req, res) {

  ​	//所有未处理的请求路径都会跑到这里

  })

### MongoDB

#### 1. 关系型数据库和非关系型数据库

表就是关系

或者说表与表之间存在关系

* 所有的关系型数据库都需要通过`spq`语言来操作
* 所有的关系型数据库在操作之前都需要设计表结构
* 而且数据表还支持约束（保证数据的完整性）
  * 唯一的
  * 主键
  * 默认值
  * 非空
* 非关系型数据库非常灵活
* 有的非关系型数据库就是 key-value 对儿，没有表
* 在 MongoDB 是长的最像关系型数据库的非关系型数据库
  * 它也是 创建数据库 -> 数据库（MongoDB中的叫法）
  * 数据表 -> 集合（数组）
  * 表记录 -> 文档对象
* MongoDB 不需要设计表结构
* 也就是说你可以任意的往里面存数据，没有结构性这么一说
* 查看MongoDB版本  在命令行工具中输入 mongod --version
* 需要配置==环境变量==，复制安装包的 bin 文件目录，加 ; 粘贴到Windows的环境变量中


### 2. 启动和关闭数据库

启动：

```shell
# mongodb 默认使用执行 mongod 命令所处盘符根目录下的 /data/db 作为自己的数据存储目录 
# 所以在第一次执行命令之前先自己手动新建一个 /data/db (在需要执行的盘符根目录下)
mongod
```

如果想要修改默认的数据存储目录，可以：

```shell
mongod --dbpath=数据存储目录路径
```

停止：

```shell
#在开启服务的控制台，直接 Ctrl + c 即可停止
# 或者直接关闭开启服务的控制台也可以
```

### 3. 连接数据库

连接：

```shell
# 默认连接本机的 MongoDB 服务
mongo
```

退出：

```shell
# 在连接状态输入 exit 退出连接
exit
```

### 4. 基本命令

* `show dbs`
  * 查看数据库列表，查看显示所有数据库（查看有数据的数据库）
* `db`
  * 查看当前操作的数据库
* `use 数据库名称`
  * 切换到指定的数据库（如果没有回创建）
  * 例如新建数据库： use zbb
* 插入数据（不设计表结构，默认每个数据自带ID）
  * `db.students.insertOne({"name":"jack"})`相当于新建了一个students数据往里面加元素
* show collections
  * 可以查看当前表里的集合
* db.集合名.find()
  * 查看当前集合中的数据

### 5. 在Node中如何操作 MongoDB 数据库 

#### 5.1 使用官方的`mongodb`包来操作

* 可以查看官方文档 github 搜索 mongodb node

#### 5.2 使用第三方 mongoose 来操作 MongoDB 数据库

第三方包：`mongoose`基于MongoDB 官方的 `mongodb`包再做的一次封装.

安装： npm i mongoose

开启mongodb 数据库，然后执行例程代码

```javascript
var mongoose = require('mongoose');

//连接MongoDB数据库，引号中的第一个为本机，第二个为本机的test数据库。
//指定连接的数据库不需要存在，当你插入第一条数据之后就会自动被创建出来
mongoose.connect('mongodb://localhost/test', { useMongoClient: true });

mongoose.Promise = global.Promise;

// 创建一个模型
// 就是在设计数据库
// MongoDB 是动态的，非常灵活，只需要在代码中设计你的数据库就可以了
// mongoose 这个包就可以让你的设计编写过程变得非常的简单
var Cat = mongoose.model('Cat', { name: String });
// Cat 为表名（最终生成名称为 cats），第二个参数为数据结构

// 实例化一个Cat 
var kitty = new Cat({ name: '喵喵'});

// 持久化保存 kitty 实例
kitty.save(function (err) {
  if (err) {
    console.log(err);
  } else {
    console.log('meow');
  }
});
```

#### 5.3 MongoDB 数据库的基本概念

按开发顺序：

* 可以有多个数据库
* 一个数据库中可以有多个集合（表）
* 一个集合中可以有多个文档（表记录）
* 文档结构很灵活，没有任何限制
* MongoDB 非常灵活，不需要像 MySQL 一样先创建数据库、表、设计表结构
  * 在这里只需要：当你需要插入数据的时候，只需要指定往那个数据库的哪个集合操作就可以
  * 一切都有 MongoDB 来帮助自动完成建库建表这件事
* MongoDB 默认开启端口 27017

理解：

```javascript
{
    qq : {//数据库
        users : [//集合
            {name:"zzz"}//文档对象 
        ]
    },
    taobao : {
            
    }
}
```

### mongoose 官方指南

#### 1. 设计Schema 发布 model

* 可以在文档中的 `model.js` 中查看增删改查的方法

```js
// 此模块是为了新建一个数据库
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

// 连接数据库
mongoose.connect('mongodb://localhost/zbb')

// 相当于设计集合结构（表结构）
// 字段名称就是表结构中的属性名称
// 每个属性的值做了强制要求，都为js代码
// 约束的目的是为了保证数据的完整性，不要有脏数据
// var blogSchema = new Schema({
//   title:  String,
//   author: String,
//   body:   String,
//   comments: [{ body: String, date: Date }],
//   date: { type: Date, default: Date.now },
//   hidden: Boolean,
//   meta: {
//     votes: Number,
//     favs:  Number
//   }
// });


// 设计约束案例：
var userSchema = new Schema({
  username: {
    type: String,
    required: true //必须要有不能为空
  },
  password: {
    type: String,
    required: true
  },
  // email: {
  //   type: String
  // }
  email: String
})

// 3. 将文档结构发布为模型
// mongoose.model 方法就是用来将一个架构发布为 model
// 第一个参数：传入一个大写名词单数字符串来表示你的数据库名称
//              mongoose 会自动将大写名词的字符串生成小写复数的集合名称
//              User在数据库中会变成users 集合名称
//  第二个参数：架构 Schema    
//  返回值 ：模型构造函数       
// var Blog = mongoose.model('Blog', blogSchema)
var User = mongoose.model('User', userSchema)

// 4. 当我们游了模型构造函数之后就可以使用构造函数对users 集合中的数据操作了（增删改查）
```

####2. 增加数据

```javascript
// 4. 当我们有了模型构造函数之后就可以使用构造函数对users 集合中的数据操作了（增删改查）
// new 模型构造函数 参数为对象
var admin = new User({
  username: 'admin',
  password: 'aaa1'
})

// 5. 数据持久化
// 使用 .save() 方法
admin.save(function (err, ret) {
  if (err) {
    console.log('保存失败');
  } else {
    console.log('保存成功');
    console.log(ret);  //就是刚刚保存的数据，id自动生成
  }
})
```

#### 3. 查询

查询所有：

```javascript
User.find(function (err, ret) {
  if(err) {
    console.log('查询失败')
  } else {
    console.log('查询成功');
    console.log(ret);
  }
})
```

按条件查询所有：

```javascript
User.find({
  username: 'zs'
}, function (err, ret) {
  if(err) {
    console.log('查询失败')
  } else {
    console.log(ret);
  }
})
```

按条件查询单个：

```javascript
User.findOne({
  username: 'zs'，
  password: '123'
}, function (err, ret) {
  if(err) {
    console.log('查询失败')
  } else {
    console.log(ret);
  }
})
```

#### 4. 删除数据

按条件删除：

```javascript
User.remove({
  username: 'zs'
}, function (err) {
  if (err) {
    console.log('删除失败')
  } else {
    console.log('删除成功')
    console.log(ret);
  }
})
```

根据条件删除一个：

```javascript
Model.findOneAndRemove(conditions, [options], [callback])
```

根据id删除一个：

```javascript
Model.findByIdAndRemove(id, [options], [callback])
```

#### 5. 更新数据

根据id更新一个：

```javascript
User.findByIdAndUpdate('数据的id', {
  更新的数据
  password: '1234'
}, function (err, ret) {
  if(err) {
    console.log('更新失败');
  } else {
    console.log('更新成功');
  }
})
```

剩余的更新方法查看文档。

### Node 操作 MySQL数据库

安装：

```shell
npm install --save mysql
```

未完待续。。。

### 异步编程

#### 回调函数

#### Promise

callback hell:

![回调地狱](E:\xuexi\DW\web lx\study img\回调地狱.PNG)

无法保证顺序的代码：

```javascript
var fs = require('fs')

fs.readFile('a.text', 'utf8', function(err, data) {
	if (err) {
		throw err
	}
	console.log(data)
	fs.readFile('b.text', 'utf8', function(err, data) {
		if (err) {
			throw err
		}
		console.log(data)
		fs.readFile('c.text', 'utf8', function(err, data) {
			if (err) {
				throw err
			}
			console.log(data)
		})
	})
})
```



##### 注意：

之前的读取文件 return 结束函数的方式，在开发中常常使用 `throw err` 的形式抛出异常

多个异步编程的情况，无法保证回调函数的执行顺序（多线程调度机制），只能通过异步嵌套异步的形式保证顺序。代码太繁琐，不好修改。

为了解决以上编码方式带来的问题（回调地狱嵌套），所以在ECMAscript 6 中新增了一个 API：`Promise`

* Promise : 保证承诺

Promise 容器：

* 容器中存放了一个==异步任务==

* 任务默认有三种状态
  * 默认状态：pending 正在进行（之前网站响应有过这个状态）
  * pending 状态发生变化，变为两种状态其中的一种
    * Resolved 解决 成功
    * Rejected 失败
    * 状态改变之后不能再改变成另一种状态

  在ECMAscript 6 中新增了一个 API Promise

  Promise 是一个构造函数

  使用：

  创建一个 Promise 容器

  1. 给别人一个承诺

     Promise 容器一旦创建，就开始执行里面的代码

     ```javascript
     new Promise(function () {
         //一个异步任务
     })
     ```

     注意：承诺本身不是异步的，只是它存放的异步任务是异步的。Promise 本身不是异步的，但是内部往往都是封装一个异步任务


  Promise 的具体使用：

  ![Promise解决方案](E:\xuexi\DW\web lx\study img\Promise解决方案.PNG)

  ```javascript
  var p1 = new Promise(function (resolve, reject) {
      fs.readFile('a.txt', function (err, data) {  //异步任务
          if (err) {
              //容器中的任务失败
              //把容器中的 Pending 状态变为 Rejected
              reject(err)
          } else {
              //承诺容器中的任务成功了
              //把容器中的 Pending 状态改为成功 Resolved
              //也就是说这里调用的resolve 方法实际上就是then 方法传递的那个 function
              resolve(data)
          }
      })
  })
  //p1 就是那个承诺
  //当p1 成功了 然后(then) 做指定的操作
  //then 方法接收的第一个 function 就是容器中的 resolve 函数中的数据
  //调用了reject 就相当于调用了 then 方法的第二个参数函数
  p1
     .then(function (data) {
      console.log(data)
      //return 123
      // 当前成功函数 return 的结果就可以在后面的 then 中function 接收到
      //当你 return 123 后面就会接收到 123
      //return hello 后面就会接收到 'hello'
      // 没有 return 后面收到的就是哦undefined
      // 上面这些事原理
      // 真正使用的是：我们可以 return 一个 Promise 对象
      //当 return 一个 Promise 对象的时候，后续的 then 中的方法的第一个参数会作为 p2 的 resolve函数的结果
      return p2
  }, function (err) {
      console.log('读取文件失败', err)
  })
  ```

  以上情况称为异步调用链式编程。

  * Promise 应用场景

    在开发的过程中，通过多个接口获取的数据或者多个ajax请求获取的数据，可以使用Promise 来写

    jquery 中的ajax 方法已经支持 Promise ，可以使用 ==then（Promise对象使用then 方法）== 方法。

    在需要==不同的请求==获取结果的时候，需要使用Promise解决方案，可以链式编程获取异步处理结果。

* ==callback 注意事项（开发中的小事）==：

  ![callback注意](E:\xuexi\DW\web lx\study img\callback注意.PNG)

    图中的fn() 函数调用没有传递 callback 参数（会出错），因此需要使用图中到的解决方案

注意：

==1. 要使用 .then() 方法，必须要支持 Promise==

==2. mongoose 的查询操作都是异步操作，都可以使用 .then 方法==

==3. .then() 方法的执行，必须要等到 Promise 的状态改变==

4. 我们在new 出来的 Promise 实例上，调用 .then() 方法，【预先】为这个 Promise 异步操作，指定 成功 和失败的 回调函数；.then() 中的参数为实参，Promise实例中的。
5.  .then() 方法是先于 Promise 中的异步方法执行的，只是 .then() 中的函数参数会在状态改变之后执行。
6.  如果在then 中写错误处理的回调函数 ，后面的 .then() 方法还会执行。如果 .catch() 这个错误信息，则后面的  .then() 方法不会再执行。

### 中间件概念

* middlewares

* 例如开发一个模块解决一个功能

  一般采取如下的形式

  ```javascript
  module.exports = function (req, res) {
      req.body = {}
  }

  //在调用的模块中使用下列函数
  query(req) //把req 对象传递到执行函数中
  ```

  调用返回的函数，传递req 对象就可以让该对象拥有body 属性。例如在express中使用 bodyParse 的配置和 template 的配置，都需要先配置之后才会给 req 对象添加一个方法

  #### Express 中间件（app.use的用法）

  中间件： 处理请求的，本质就是函数。

  ==中间件主要执行规则就是前面的中间件会对后面的中间件进行封堵。next() 方法也要求匹配，如果路径不匹配也不行。next 是执行后面的，不是执行后一个==   

  在 Express 中，对中间件有几种分类。

  同一个请求所经历的中间件都是同一个请求对象和响应对象。传递相同的req 对象和 res 对象。

  #####1. 应用程序级别中间件

  1. 不关心请求路径的请求方法的中间件

     也就是说任何请求（不关心请求路径和请求方法）都会进入这个中间件

     使用 app.use()  方法来调用，参数一般为函数

     ```javascript
     app.use(function (req, res, next) {
         
     })
     ```

     

     * 中间件本身就是一个方法，该方法接收三个参数

       * Request 请求对象

       * Response 响应对象

       * next 下一个中间件，next 本身为函数，默认中间件不会执行下一个中间件，调用  next() 方法之后，会执行下一个中间件。

         ```javascript
         app.use(function (req, res, next) {
             console.log(1)
             next()
         })
         app.use(function (req, res, next) {
             console.log(2)
         })
         ```


     当一个请求进入一个中间件之后，如果不调用 next 则会停留在当前中间件。所以next 是一个方法，用来调用下一个中间件的。

  2. 关心请求路径的中间件（以 /xxx 开头的路径中间件）。

     ```javascript
     app.use('/a', function (req, res, next) {
         console.log(req.url) //此处打印的 url 是出去 /a 后面的url
     })
     ```

     当请求路径是以 `a`开头的情况，执行function 里面的代码

  当请求进来，会从第一个中间件开始进行匹配。（代码顺序）

  ​	如果匹配，则进来。

  ​		如果请求进入中间件之后，没有调用next() 方法，则代码会停留在这个中间件中。

  ​		如果调用了next 则向后查找匹配的中间件

  ​	如果不匹配，则继续判断匹配下一个中间件。

* 如果没有能匹配的中间件，express 会默认输出：Cannot GET 路径(POST 请求则换成POST)

#####2. 路由级别的中间件

3. 除了以上中间件之外，还有一种最常用的

   严格匹配请求方法和请求路径的中间件

   app.get 和 app.post

   ```javascript
   app.get('/a', function (req, res, next) {
       console.log('aaa')
       next()
   })
   app.get('/a', function (req, res, next) {
       console.log('bbb')
   })
   ```

   上述例子中下面的不会覆盖上面的中间件

### 总结

* express 框架下编程也可以使用原生的node编写
* 原生的node 使用 res.end() 方法来结束请求，express 框架使用 res.send() 方法来结束请求
* 原生的node使用自己判断的方式来开发静态资源；express 框架使用 app.use() （中间件形式）方法来开放静态资源