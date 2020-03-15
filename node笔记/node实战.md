# 1.认识node.js

![node实现设计](..\pic\node实现设计.png)

## 1.2 nodejs 和 JavaScript 区别

* ECMAscript（语法和词法）

  定义了语法，写JavaScript和node都得遵守

  * 不能操作DOM，不能绑定事件，不能发送ajax请求

  * 不能处理http 请求，不能操作文件
  * 只是定义了语法

* javascript

  * 使用ECMA 的语法+ webapi
  * DOM、BOM操作、事件绑定、ajax等

* nodejs

  ecmaScript + node api

  * 处理 http 、操作文件

## 1.3 commonjs(看node的笔记)

导出：

```js
function add () {
    
}

module.exports = {
    add
}
```



## 1.4 debug

* 在node.js 文件中直接打断点，然后点开右侧的debug，点击 start

# 2. 准备

## 2.1 server 开发和前端开发的区别

* 服务的稳定性
* 考虑内存和 cpu（优化、扩展）
* 日志记录
* 安全
* 集群和服务拆分

### 2.1.1 服务的稳定性

* server 端可能遭到各种恶意攻击和误操作
* 单个客户端可以挂掉，服务端不能挂
* 用 PM2 做进程守护

### 2.1.2 考虑CPU和内存（优化和扩展）

* 客户端独占一个浏览器，内存和CPU 不是问题
* server 端承载很多请求，CPU和内存都是紧缺资源
* 使用 stream 写日志（优化），使用 radis 存 session（扩展）

### 2.1.3 日志记录

* 前端发送日志
* server 端要记录日志，存储日志，分析日志

### 2.1.4 安全

* server端要随时准备接受各种恶意攻击

### 2.1.5 集群和服务拆分

* 产品发展速度快，流量可能会迅速增加
* 通过扩展机器和服务拆分来承接？支持服务拆分？？？？

## 2.2 项目介绍

从三个方面开始，产品分析

* 目标
* 需求
* 技术方案

### 2.2.1 目标

* 开发一个博客系统，具有博客的基本功能
* 只做 server 端，不管前端

### 2.2.3 技术方案

* 数据如何存储
* 与前端如何对接，即接口设计

存储：

* 博客
* 用户

api 设计

![api-design](..\picture\node-project\api-design.png)



# 3. 前置知识

## 3.1 http 相关

* http 默认端口 80，https 默认端口 443

## 3.2 nodejs 处理http请求

* get 请求和 queryString
* post 请求和 postdata
* 路由

Get 请求处理

```js
let http = require('http')
const querystring = require('querystring')

const server = http.createServer((req, res) => {
    // res.setHeader('Set-Cookie', 'testServer=11111111')
    // res.setHeader('location', 'http://127.0.0.1:3001')
    console.log('url:', req.method)
    console.log('url:', req.url)
    let query = querystring.parse(req.url.split('?')[1])
    console.log('query:', query)
    res.writeHead(200, {
        'content-type': 'text/plain; charset=UTF-8'
    })
    res.end(JSON.stringify(query))
})

server.listen(3000, () => {
    console.log('server is running')
})
```

* 用到 http 和 querystring 模块，`req.url` req 对象中有一切信息。
* req.headers 里面有一些基本的信息

req 的事件：

* data 事件

  该事件能监听接受到的数据

  ```js
  req.on('data', (chunk) => {
      console.log(chunk.toString)
  })
  ```

  

* end 事件

  能够监听到数据请求完成

  ```js
  req.on('end', () => {
      console.log('请求结束')
  })
  ```

处理 http 请求(需要 tostring 方法可以将接受到的二进制转化成字符串)

```js
const http = require('http')

const server = http.createServer((req, res) => {
    console.log('content-type: ', req.headers['content-type'])
    req.on('data', (chunk) => {
        console.log(chunk.toString(), '--------接受的数据')
    })
    req.on('end', () => {
        console.log('接受数据结束')
    })
})

server.listen(3000, () => {
    console.log('server is running')
})
```