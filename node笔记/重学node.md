# 1. 目标

* 做一个 BFF 层（backend for frontend），组装后端数据给前端做服务
* 目标
  * 对用户侧提供 HTTP 服务
  * 使用后端 RPC 服务

# 2. 起飞

## 2.1 node 专有

* 失去了 requestAnimationFrame()，新增 setImmediate

* 环境变量

  __filename

  __dirname

* 进程对象

  process，记载了运行node进程信息，包含 性能分析，时间，内存管理，杀进程

  .env 当前所运行node的环境变量，argv 启动node 程序使用的命令行

## 2.2 一些思考

* 在前端工程化项目中，使用 `env.NODE_ENV`的方式，相当于给 process 的 env 对象中添加了一个属性。process.argv 则能打印出在运行该程序时的参数，例如 `node index.js test`



# 3. 复习一下规范

## 3.1 初代

* 浏览器使用 `script`标签去加载js，没有直接执行。

问题：

1. 脚本变多时，需要手动管理加载顺序
2. 不通的脚本之间逻辑调用，需要通过全局变量的方式。jQuery 就是挂载到全局变量上的
3. 没有 html 怎么办？ Node 需要一个模块规范来管理

## 3.2 commonjs 规范

* 导出的 exports 能够在外边改变改引用的属性

# 4. 内置模块

## 4.1 events 模块

### events 模块中的 EventEmitter

* 观察者模式(前端)
  * addEventListener
  * removeEventListener（待补上）

## 4.2 querystring 模块

* 能够解析 http 请求中的query

```js
const querystring = require('querystring')

const query = querystring.parse('name=111')
```



# 5. 非阻塞 I/O

## node js 的非阻塞 IO

* I / O 即 input/output ，一个系统的输入输出
* 阻塞 I/O 和 非阻塞 I/O 的区别就在于系统接受输入再到输出期间，能不能再接受其他输入。说白了就是利用了异步回调。

理解非阻塞 I/O 的要点在于

* 确定一个进行 input/output 的系统
* 思考在I/O 过程中，能不能进行其他的I/O。
* 非阻塞 I/O 是通过回调函数来实现的，回调函数的实现方式成为一部编程

## 异步编程

callback

* 回调函数格式规范
  * Error-first callback
  * Node-style callback
  
* 第一个参数是 error，后面的参数才是结果

  回调栈 -> 浏览器 sources 中的 call stack 

  node 中为了避免不知道第一个参数的类型，约定第一个参数是 error，第一个参数不为空，就代表调用失败

### 异步流程控制

1. 回调地狱
2. 异步并发

## node系统



![node_system](./img/node_system.png)

## 事件循环

* 是实现非阻塞 I/O 的基础。libuv c++ 库

1. 面试问题，抛出的异常不能捕获
2. 手动实现事件循环

# Http 库

## express

* 特点，强大的路由能力
* 中间件 洋葱模型

### feature

- Robust routing

  强壮的路由系统

- Focus on high performance

  专注高性能，功能稍弱

- Super-high test coverage

  

- HTTP helpers (redirection, caching, etc)

- View system supporting 14+ template engines

- Content negotiation

  negotiation 协商

- Executable for generating applications quickly

### 核心功能

* 路由

* request/response 简化

  request：pathname、query

  response：send()、json()、jsonp() 等

### response methods

| Method                                                       | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [res.download()](http://expressjs.com/en/4x/api.html#res.download) | Prompt a file to be downloaded.                              |
| [res.end()](http://expressjs.com/en/4x/api.html#res.end)     | End the response process.                                    |
| [res.json()](http://expressjs.com/en/4x/api.html#res.json)   | Send a JSON response.                                        |
| [res.jsonp()](http://expressjs.com/en/4x/api.html#res.jsonp) | Send a JSON response with JSONP support.                     |
| [res.redirect()](http://expressjs.com/en/4x/api.html#res.redirect) | Redirect a request.                                          |
| [res.render()](http://expressjs.com/en/4x/api.html#res.render) | Render a view template.                                      |
| [res.send()](http://expressjs.com/en/4x/api.html#res.send)   | Send a response of various types.                            |
| [res.sendFile()](http://expressjs.com/en/4x/api.html#res.sendFile) | Send a file as an octet stream.                              |
| [res.sendStatus()](http://expressjs.com/en/4x/api.html#res.sendStatus) | Set the response status code and send its string representation as the response body. |

## koa

* 及简设计模式，不带路由功能，由中间件自己提供
  * koa-mount 提供了路由功能
* 中间件执行可以是异步，能够中断执行
* 简单粗暴的直接赋值，而不是调用函数赋值



# koa 洋葱模型实现

* 一个 middleware 队列保存 use 的中间件

* 执行顺序的实现（洋葱模型）

  核心逻辑：

  ```js
  function fn(ctx) {
      return dispatch(0)
      function dispatch(i) {
          const mw = middleware[i]
          if (!mw) {
              return
          }
          return mw(ctx, dispatch.bind(null, i + 1))
      }
  }
  
  fn()
  ```

  https://zhuanlan.zhihu.com/p/279391637

  * 思路：
    1. 中间件队列中所有的 mw 都会得到执行
    2. 执行的时候注意 next 加入异步队列



# 数组 reduce 

* 如果没有初始值，则会从第一项 第二项开始执行



