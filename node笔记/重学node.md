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



## fs 模块

* fs 模块一切行为都是基于fd文件描述符的，如果文件描述符失效则不能够进行操作



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

Todo: 重点复习



![node_system](./img/node_system.png)

## 事件循环

* 是实现非阻塞 I/O 的基础。libuv c++ 库

1. 面试问题，抛出的异常不能捕获
2. 手动实现事件循环

### 事件循环

* 事件循环是node.js 最重要的知识点。他是node.js 的核心，也就是为什么node能脱颖而出，真正有区别于别的语言

> 为什么这么重要？ 因为它阐明了 Node.js 如何做到异步且具有非阻塞的 I/O，所以它基本上阐明了 Node.js 的“杀手级应用”，正是这一点使它成功了。

单线程：

> Node.js JavaScript 代码运行在单个线程上。 每次只处理一件事。
>
> 这个限制实际上非常有用，因为它大大简化了编程方式，而不必担心并发问题。
>
> 只需要注意如何编写代码，并避免任何可能阻塞线程的事情，例如同步的网络调用或无限的循环。

理解事件循环：

> 事件循环会赋予调用堆栈优先级，它首先处理在调用堆栈中找到的所有东西，一旦其中没有任何东西，便开始处理消息队列中的东西。

* js的开始，比如说一个 script 的引入，就是一个函数调用的开始。
* 先处理函数调用栈，为空再处理消息队列



### node.js 中的事件循环

#### 什么是事件循环

* 事件循环允许nodejs 尽可能的调用系统内核处理多个操作。

> 事件循环允许 Node.js 通过尽可能将操作卸载到系统内核来执行非阻塞 I/O 操作——尽管 JavaScript 是单线程的。
>
> 由于大多数现代内核都是多线程的，它们可以处理在后台执行的多个操作。当这些操作之一完成时，内核会通知 Node.js，以便可以将适当的回调添加到**轮询**队列中以最终执行。我们将在本主题后面更详细地解释这一点。



在 nodejs 启动的时候，它会初始化事件循环，处理输入脚本。当有异步 I/O 操作的时候，开始处理事件循环。

下图显示了事件循环操作顺序的简化概览。

```sql
   ┌───────────────────────────┐
┌─>│           timers          │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │
│  └─────────────┬─────────────┘      ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │
│  │           poll            │<─────┤  connections, │
│  └─────────────┬─────────────┘      │   data, etc.  │
│  ┌─────────────┴─────────────┐      └───────────────┘
│  │           check           │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │
   └───────────────────────────┘
```

> 每个盒子将被称为事件循环的一个“阶段”。-phase
>
> Each box will be referred to as a "phase" of the event loop.

#### phase

* 每个阶段都有一个 fifo 队列，保存着要执行的回调。虽然每个阶段都有其特殊性，当事件循环进入这个阶段，就会执行该阶段队列中的回调。直到队列耗尽或者达到最大回调次数已被执行，事件循环进入下一个阶段。——==进入下一个阶段条件==

#### node 中的 IO

"I/O" 主要指由[libuv](https://libuv.org/)支持的，与系统磁盘和网络之间的交互。

在 Node.js 标准库中的所有 I/O 方法都提供异步版本，**非阻塞**，并且接受回调函数。某些方法也有对应的 **阻塞** 版本，名字以 `Sync` 结尾。

#### 阶段概览

https://theanarkh.github.io/understand-nodejs/chapter03-%E4%BA%8B%E4%BB%B6%E5%BE%AA%E7%8E%AF/

"I/O" 主要指由[libuv](https://libuv.org/)支持的，与系统磁盘和网络之间的交互。

* timer 这个阶段主要执行 settimeout、setinterval 的回调

* pending callbacks: 执行推迟到下一个循环迭代的 I/O 回调。？todo: 什么意思，就是说执行 I/O 回调吗？

  官网对pending阶段的解释是在上一轮的Poll IO阶段没有执行的IO回调，也就是来不及执行？还是说太多了推迟执行呢？

  > 此阶段为某些系统操作（例如 TCP 错误类型）执行回调。例如，如果一个 TCP 套接字`ECONNREFUSED`在尝试连接时收到，一些 *nix 系统希望等待报告错误。这将在**挂起的回调**阶段排队执行。

  > 官网对pending阶段的解释是在上一轮的Poll IO阶段没有执行的IO回调，会在下一轮循环的pending阶段被执行。从源码来看，Poll IO阶段处理任务时，在某些情况下，如果当前执行的操作失败需要执行回调通知调用方一些信息，该回调函数不会立刻执行，而是在下一轮事件循环的pending阶段执行（比如写入数据成功，或者TCP连接失败时回调C++层），我们先看pending阶段的处理

* idle, prepare 仅在系统内部使用

* **poll**  检索新的 I/O 事件；执行与 I/O 相关的回调。（除了 关闭回调、定时器回调、和setimmediate回调等几乎所有的回调）

  > 非常重要的阶段，比如一个http 请求回调执行，就在这个阶段执行！==决定node并发的阶段！==
  >
  > Poll IO是Libuv非常重要的一个阶段，文件IO、网络IO、信号处理等都在这个阶段处理，这也是最复杂的一个阶段

  ==**轮询** 阶段有两个重要的功能：==

  1. 计算应该阻塞和轮询 I/O 的时间。
  2. 然后，处理 **轮询** 队列里的事件。

  这个阶段主要描述的是函数调用堆栈，一旦为空则执行 setimmediate 回调

  > 当事件循环进入**轮询**阶段*并且没有调度计时器*时，将发生以下两种情况之一：
  >
  > - *如果**轮询**队列**不为空***，则事件循环将遍历其回调队列，同步执行它们，直到队列耗尽或达到与系统相关的硬限制。(==比如最大函数调用栈！==)
  > - *如果**轮询**队列**为空***，则会发生另外两种情况之一：
  >   - 如果脚本已被 调度`setImmediate()`，则事件循环将结束**轮询**阶段并继续**检查**阶段以执行那些已调度的脚本。
  >   - 如果脚本**没有**被 调度`setImmediate()`，事件循环将等待回调被添加到队列中，然后立即执行它们。
  >
  > 一旦 **轮询** 队列为空，事件循环将检查 _已达到时间阈值的计时器_。如果一个或多个计时器已准备就绪，则事件循环将绕回计时器阶段以执行这些计时器的回调。

  * 注意：微任务也需要在这个阶段执行完成，知道这个阶段的宏任务耗尽。（那么微任务肯定执行完成了！）

* check 阶段，执行 setimmediate 回调

  > 通常，随着代码的执行，事件循环最终会到达**轮询**阶段，在该阶段它将等待传入的连接、请求等。但是，如果已调度回调`setImmediate()` 并且**轮询**阶段变为空闲状态，它将结束并且继续 **检查**阶段而不是等待**轮询**事件。

  * 理论是哪个最终会进入 poll phase，执行同步代码（函数调用栈）。

* close callback 执行关闭相关回调。e.g. `socket.on('close', ...)`

  > 如果 socket 或者 处理程序突然关闭（例如`socket.destroy()`），那么该 close 事件将在这个阶段触发。
  >
  > 回调的关闭(socket.on(‘close’, ()=>{})) 都在这里处理的，更像一个清理阶段

>  为了防止轮询阶段 poll 使事件循环饿死，libuv 在停止轮询更多事件前有一个强硬的最大值（取决于系统）



#### 啥时候结束

==直到队列耗尽或达到与系统相关的硬限制。==

* 有硬件限制，只存在理论上的永不阻塞

### node是怎么进行并发的

* ~~如果一个请求进来，还没有处理完成，node是怎么实现并发的呢？~~

  得益于js 的静态作用域和上下文

  ~~函数没有执行完成上下文不会释放~~

### job queue

* 工作队列，也就是 promise 等。与之对应的是 task queue，任务队列

> ECMAScript 2015 引入了作业队列的概念，Promise 使用了该队列（也在 ES6/ES2015 中引入）。 这种方式会尽快地执行异步函数的结果，而不是放在调用堆栈的末尾。(不像settimeout，尽快地执行该回调)
>
> 在当前函数结束之前 resolve 的 Promise 会在当前函数之后被立即执行。
>
> 有个游乐园中过山车的比喻很好：消息队列将你排在队列的后面（在所有其他人的后面），你不得不等待你的回合，而工作队列则是快速通道票，这样你就可以在完成上一次乘车后立即乘坐另一趟车。

## process.nextTick()

每当事件循环进行一次完整的行程时，我们都将其称为一个滴答(tick)。

当将一个函数传给 `process.nextTick()` 时，则指示引擎在当前操作结束（在下一个事件循环滴答开始之前）时调用此函数：

* 也就是一个主任务或者消息队列中的任务(宏任务微任务都算)都算开启了一个事件循环 ❎，微任务不会开启事件循环
* 当该操作结束时，JS 引擎会运行在该操作期间传给 `nextTick` 调用的所有函数。

> 您可能已经注意到它`process.nextTick()`没有显示在图表中，即使它是异步 API 的一部分。这是因为 `process.nextTick()`从技术上讲它不是事件循环的一部分。相反，`nextTickQueue`将在当前操作完成后处理，而不管事件循环的当前阶段。在这里，*操作*被定义为来自底层 C/C++ 处理程序的转换，并处理需要执行的 JavaScript。

* 并不是事件循环的一部分，是异步api的一部分
* 存在一个 nextTickQueue 在当前处理之后执行
* ==在 promise 之前执行==

### 存在问题

* 有一种可能性，递归调用 process.nextTick() 从而阻止事件循环的执行，出现饥饿问题，事件循环中的回调函数不能执行！

### 为什么被允许这个api的设计

* 主要是为了调整时序，有一些回调函数的执行，需要一些同步代码执行完成之后才需要

### 为什么需要process.nextTick()

* process.nextTick 提供了一种可能，在同步的任务最后执行。==感觉和浏览器的 queueMicrotask 使用方式有点相似！==

* 场景：

  1. 如果已经触发了一个事件，然后才绑定。那么这个绑定是没法监听到该事件触发的，可以使用 process.nextTick 触发事件

     ```typescript
     const EventEmitter = require('events');
     
     class MyEmitter extends EventEmitter {
       constructor() {
         super();
     
         // use nextTick to emit the event once a handler is assigned
         process.nextTick(() => {
           this.emit('event');
         });
       }
     }
     
     const myEmitter = new MyEmitter();
     myEmitter.on('event', () => {
       console.log('an event occurred!');
     });
     ```

  2. 调整时序，把函数调用栈展开，也就是最后一个调用的函数放到 process.nextTick() 中

> 一开始设计的时候，setImmediate充当了微队列的作用（虽然他不是）。设计者希望执行完poll后立即执行setImmediate（当然现在也确实是这么表现的）。所以起的名字叫`Immediate`，表示`立即`的意思。 但是后来问题是，poll里可能有N个任务连续执行，在执行期间想要执行setImmediate是不可能的。因为poll队列不停，流程不向下执行。
>
> 于是出现nextTick，真正的微队列概念。但此时，immediate的名字被占用了，所以名字叫nextTick（下一瞬间）。事件循环期间，执行任何一个队列之前，都要检查他是否被清空。其次是Promise。
>
> 作者：前端小石头
> 链接：https://juejin.cn/post/7010308647792148511
> 来源：稀土掘金
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

* 如果多个并行的任务，想要立即触发一个微任务是不可能的，执行不完成不能进入下一个阶段。因此引入了 nextTick 这个方法。

### nextick、setimmediate、settimeout

传给 `process.nextTick()` 的函数会在事件循环的当前迭代中（当前操作结束之后）被执行。 这意味着它会始终在 `setTimeout` 和 `setImmediate` 之前执行。

> - `process.nextTick()`在同一阶段立即触发
> - `setImmediate()`在事件循环的下一次迭代或“滴答”时触发
>
> 本质上，名称应该互换。`process.nextTick()`比 触发得更快`setImmediate()`，但这是过去的产物，不太可能改变。进行此切换会破坏 npm 上的大部分软件包。每天都有更多的新模块被添加，这意味着我们等待的每一天，都会发生更多潜在的损坏。虽然它们令人困惑，但名称本身不会改变。
>
> > 我们建议开发人员`setImmediate()`在所有情况下都使用它，因为它更容易推理
>
> * ==其实nextTick 和 setImmediate 名称是互换的==

延迟 0 毫秒的 `setTimeout()` 回调与 `setImmediate()` 非常相似。 执行顺序取决于各种因素(不能稳定复现哪个先执行！！)，但是它们都会在事件循环的下一个迭代中运行。

```typescript
// !注意：为什么setimmediate 不一定在settimeout 0之前执行呢？因为程序本身执行需要耗时。
// ! 比如本程序的执行时长就是2ms左右，settimeout 超过2ms才能在 setimmediate 后执行。
// 所以 和机器性能等因素有关系
setTimeout(() => {
    console.log('timeout exec 0');
}, 3);

setImmediate(() => {
    console.log('setimmediate exec');
})

console.log(Date.now() - time1);
```



* todo 事件循环 和 Node.js 还提供 `setImmediate()`（相当于使用 `setTimeout(() => {}, 0)`），通常用于与 Node.js 事件循环配合使用。
* 事件循环详细！

### setimmediate、settimeout 执行顺序

* 在一个 I/O 周期之外（也就是主线程），执行顺序收到主线程执行时长的影响

* 在一个 I/O 周期之内，执行顺序是固定的，setimmediate一定领先于settimeout

  ```typescript
  setTimeout(() => {
      setImmediate(() => {
          console.log('immediate exec222');
      })
  
      setTimeout(() => {
          console.log('settimeout exec222');
      }, 0);
  }, 100);
  ```

  



## JavaScript 异步编程与回调

### 编程语言中的异步性

> ==计算机在设计上是异步的。==
>
> 异步意味着事情可以独立于主程序流而发生。——异步的目的，主要用于并发
>
> 在当前的用户计算机中，每个程序都运行于特定的时间段，然后停止执行，以让另一个程序继续执行。 这件事运行得如此之快，以至于无法察觉。 我们以为计算机可以同时运行许多程序，但这是一种错觉（在多处理器计算机上除外）。
>
> 程序在内部会使用中断，一种被发送到处理器以获取系统关注的信号。
>
> 这里不会深入探讨这个问题，只要记住，程序是异步的且会暂停执行直到需要关注，这使得计算机可以同时执行其他操作。 当程序正在等待来自网络的响应时，则它无法在请求完成之前停止处理器。
>
> 通常，==编程语言是同步的==，有些会在语言或库中提供管理异步性的方法。 默认情况下，C、Java、C＃、PHP、Go、Ruby、Swift 和 Python 都是同步的。 其中一些语言通过使用线程（衍生新的进程）来处理异步操作。

### JavaScript

* JavaScript 默认情况下是同步的，并且是单线程的。 这意味着代码无法创建新的线程并且不能并行运行。

js 如何做到并发的呢？

答案就在于它的环境。 浏览器通过提供一组可以处理这种功能的 API 来提供了一种实现方式。

更近点，Node.js 引入了非阻塞的 I/O 环境，以将该概念扩展到文件访问、网络调用等。

### 回调中的错误

 一种非常常见的策略是使用 Node.js 所采用的方式：任何回调函数中的第一个参数为错误对象（==即错误优先的回调todo:为什么==）。

如果没有错误，则该对象为 `null`。 如果有错误，则它会包含对该错误的描述以及其他信息。

#### node中的回调

https://juejin.cn/post/6957676506252312583

* 约定第一个参数是 error，为什么这样约定参考如上。

### promise

* Promise 错误处理 todo

### 现代 async/await

* 调试promise很难？？？todo

# node api

## node 官网阅读

### 1. 文档分类

一个模块分为：属性、类、方法

* 一般情况下，都是从这三个方面去看文档



## commonjs

### 模块作用域

#### __dirname

当前模块的目录名。 这与 [`__filename`](http://nodejs.cn/api/modules.html#__filename) 的 [`path.dirname()`](http://nodejs.cn/api/path.html#pathdirnamepath) 相同。



## node REPL

https://juejin.cn/post/6865953350211338253#heading-12

* Node.js的交互式运行环境（REPL）

### 什么是 REPL

在Node.js中，为了方便开发者测试JavaScript代码，提供了一个名为REPL（Read Eval Print Loop:交互式解释器）的可交互运行环境，开发者可在该运行环境中输入任何表达式，然后按下回车键，REPL运行环境将显示该表达式的运行结果。

### 为什么需要 REPL

* 像浏览器的控制台一样，提供一个可执行环境，进行代码调试测试等

### 进入REPL

执行 node 即可进入

### 技巧

_ 上一个结果

.help 查看基础的命令



## timer

### unref 和 ref

> 调用时，活动的 `Timeout` 对象不需要 `Node.js` 事件循环保持活动状态。 如果没有其他活动保持事件循环运行，则进程可以在调用 `Timeout` 对象的回调之前退出。 多次调用 `timeout.unref()` 将无效。 调用 `timeout.unref()` 会创建一个内部定时器，它将唤醒 `Node.js` [事件循环](https://www.zhihu.com/search?q=事件循环&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"117732823"})。 创建太多这些定时器可能会对 `Node.js` 应用程序的性能产生负面影响。
>
> https://zhuanlan.zhihu.com/p/117732823

timer.unref 标记句柄，当进程中再无存活的事件，timer 句柄不会阻止进程退出。也就是执行完同步代码即可退出了。

* 事件循环的callback 会阻止进程的退出

  Todo 那么 http 请求是怎么样阻止事件循环退出的呢？



* todo 解释一下

  https://zhuanlan.zhihu.com/p/38091559

```typescript
const timer = setTimeout(() => {
    console.log('timer exec');
}, 1000);

timer.unref()
// 这个是为什么？用事件循环解释一下！
setTimeout(() => {
    console.log('执行');
    timer.ref()
}, 1000);

```

> `unref` 与`clearTimeout` 的共同点是都不会 hold 住当前 process。不同点是 `unref` 并不会取消回调函数的执行，取消其执行是因为程序退出导致的。而 `clearTimeout` 本意就是取消回调函数的执行。

```typescript
import fs from 'fs'

const timer = setTimeout(() => {
    console.log('timer exec');
}, 0);

timer.unref()

fs.readFile('../event-loop.js', (err, data) => {
    console.log('file read complete');
})
```

* 这样也会 hold 住进程，timer exec 还是会执行

```typescript
const timer = setTimeout(() => {
    console.log('timer exec');
}, 1000);

timer.unref()

setTimeout(() => {
    console.log('执行');
}, 400);
```

* 这样就不会执行

总结：

hold 进程的程序如果在hold期间内执行被 unref 的回调，则会执行；如果超过 hold 的时间范围，则不会执行。

* 这也就是说为什么从事件循环中移除了

#### 怎么样理解 unref 呢？

* 引用计数更好理解！

  不会阻止 callback 的执行，因此可能会执行，除非程序退出

#### 引用计数

https://zhuanlan.zhihu.com/p/117732823

> 上面的例子 `timeout` [回调函数](https://www.zhihu.com/search?q=回调函数&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"117732823"})其实是已经推进了事件循环当中，不过为什么不会等待事件循环执行结束才退出进程呢？这时候引入了一个 `引用计数` 的概念，可以简单理解为当一个新的事件加入事件循环的队列中时，`引用计数` 就会 `+1` ，当执行完事件后，`引用计数` 就会 `-1`，直到没有新的事件进来，`引用计数` 等于 `0` 的时候进程退出。`unref` 调用后，`引用计数` 会 `-1`。

![img](https://pic1.zhimg.com/v2-c8fc1086849bc6c7b9ebdd5bd9a8addc_b.jpg)



## path 路径

`path` 模块提供了许多非常实用的函数来访问文件系统并与文件系统进行交互。

* 一般用来处理各种路径，不是直接获取的。用来处理的！
* 可以使用 Linux 的通配符进行匹配

### 注意

* 所有的路径操作都要记住识别mac 和 windows
  * 包括sep 和 delimiter

### path.dirname(path)

`path.dirname()` 方法返回 `path` 的目录名，类似于 Unix `dirname` 命令。 尾随的目录分隔符被忽略，见 [`path.sep`](http://nodejs.cn/api/path.html#pathsep)。

### path.sep

* 返回特定于平台的路径片段分离器
  
  该模块提供了 `path.sep`（作为路径段分隔符，在 Windows 上是 `\`，在 Linux/macOS 上是 `/`）
  
  - Windows 上是 `\`
  - POSIX 上是 `/`

### path.delimiter

* 返回特定平台的分隔符

  `path.delimiter`（作为路径定界符，在 Windows 上是 `;`，在 Linux/macOS 上是 `:`）。

### path.resolve

一共有两种形式，肯定返回一个绝对路径

* 第一个参数是相对路径，那就拼接当前目录，然后计算后面的路径（同join方法，也会进行路径计算（相对路径））
* 第一个参数是绝对路径，怎以第一个路径为开头

### path.join

把多个路径组合起来

* *一般通过加 dirname 获取一个绝对路径*



```typescript
// node 路径模块，http://nodejs.cn/learn/the-nodejs-path-module
// path 模块提供了许多非常实用的函数来访问文件系统并与文件系统进行交互。
import path from 'path'

/**
 * 该模块提供了 path.sep（作为路径段分隔符，在 Windows 上是 \，在 Linux/macOS 上是 /）和 path.delimiter（作为路径定界符，在 Windows 上是 ;，在 Linux/macOS 上是 :）。
 */
// 当前系统分离器separator；当前系统分隔符delimiter；
// console.log(path.sep, path.delimiter);

// path.basename(); 返回路径的最后一部分。 第二个参数可以过滤掉文件的扩展名; p1: 路径; p2: 后缀
// console.log(path.basename('/test/test2.txt', '.txt'));

// 返回路径的目录部分
// console.log(path.dirname('test/test2'), path.dirname('test/test2/test3'));

// 返回扩展名
// console.log(path.extname('test/test1'), '--', path.extname('test/test2/txt.txt'));

// 是否是绝对路径。从根目录出来的就是绝对路径
// console.log(path.isAbsolute('./test/test2'));

// ! 注意：当前路径不会返回 ./ 形式，而是直接路径开始
// 连接路径的两个部分。纯粹是一个拼接！不会计算就是简单拼接
// console.log(path.join('./test/test2', 'test1/test3'), path.join('./test.txt'));

// 把路径标准化。当包含类似 .、.. 或双斜杠等相对的说明符时，则尝试计算实际的路径：
// 把 . 、 .. 、 // 等路径处理掉
// console.log(path.normalize('./test/test1/..//test2'));

/**
 * root: 根路径。
    dir: 从根路径开始的文件夹路径。
    base: 文件名 + 扩展名
    name: 文件名
    ext: 文件扩展名
 */
// console.log(path.parse('../test/test1/test2/txt.txt'));

// 返回两个值之间的相对路径。接受 2 个路径作为参数。 基于当前工作目录，返回从第一个路径到第二个路径的相对路径。
// console.log(path.relative('../path', './test.txt'));

// 返回相对路径的绝对路径计算 // ! 绝对路径，总结就是如果存在第一个值，就以第一个值为基准
// 1. 一个参数
// console.log(path.resolve('./test.txt'));
// 2. 两个参数，第一个参数作为基准
// console.log(path.resolve('../../test', './basic.ts'));
// 3. 两个参数，第一个参数是绝对路径
console.log(path.resolve('/test', '../test2/test3'));

```

### 总结

想要得到一个路径方法的结果，一定要主观的从某个路径出发，结果符合主观意识



## fs 模块

Api 如下

```typescript
import fs from 'fs'
import os from 'os';
import path from 'path';

// 检查文件是否存在，node 是否有权限访问。当前用户的权限
// 文件访问常量参考：http://nodejs.cn/api-v12/fs.html#fs_file_access_constants
// fs.access('./test.txt', fs.constants.W_OK, err => {
//     console.log(err);
// })
// 同步检查文件是否存在，不存在抛出错误
// fs.accessSync('./test.txt')

// 追加数据到文件，如果不存在文件，则创建文件。和 write 不一样，write存在则会直接覆盖
// fs.appendFile('./test.txt', '\n new2 line', 'utf-8', (err) => {
//     console.log(err);

// })

// ? 权限相关
// 修改文件权限
// fs.chmod('./test.txt', '755', (err) => {
//     console.log(err, globalThis);
// })
/**
 * chown函数是在指定的文件上进行操作；

    fchown函数是在已打开的文件进行操作；

    lchown是更改符号链接本身的所有者，而不是符号链接所指向的实际文件。
 */
// fs.lchown
// fs.fchown

// 修改文件所有者和群组
// fs.chown

// fs.open 拿到文件描述符
// fs.open('./test.txt', (err, fd) => {
//     console.log(err, fd);
//     fs.readFile(fd, (err, data) => {
//         console.log(data.toString('utf-8'));
//     })
// })
// 第二个参数：文件系统标志，参考node官网，主要是用于对文件的操作类型，只读、只写等。不同的操作类型拿到不同的值
// fs.closeSync(22)
// const fd = fs.openSync('./test.txt', 'r')
// console.log(fd);
// fs.readFile(22, (err, data) => {
//     if (err) {
//         console.log(err);
//         return
//     }
//     console.log(data.toString('utf-8'));
// })

// 关闭文件描述符，用ls查看会是红色的标记。// !且不能再通过文件系统对其进行操作！
// fs.closeSync(22);
// const buffer = fs.readFileSync('./test.txt', 'utf-8')
// console.log(buffer.toString());

// fs.close(22, err => {
//     console.log(err);
// })
// fs.open('./new2.txt', (err, fd) => {
//     console.log(err, fd);
// })

// 拷贝文件
// fs.copyFile('./test.txt', 'new.txt', (err) => {
//     console.log(err);
// })

// 新建硬链接
// fs.link
// 软连接和硬链接。https://zhuanlan.zhihu.com/p/382931448
// readlink 可以查看软连接的指向

// 创建文件夹
// fs.mkdir('./test', err => {
//     console.log(err);
// })

// 创建临时目录，会自动拼接6个随机字符串
// fs.mkdtemp("temp", (err, folder) => {
//     if (err)
//         console.log(err);
//     else {
//         console.log("The temporary folder path is:", folder);
//     }
// });

// 在系统临时目录下创建一个临时目录
// import { sep } from 'path'; 
  
// // Get the temporary directory of the system 
// const tmpDir = os.tmpdir();  

// console.log(tmpDir);
// fs.mkdtemp(`${tmpDir}${sep}`, (err, folder) => { 
//   if (err) 
//     console.log(err); 
//   else { 
//     console.log("The temporary folder path is:", folder); 
//   } 
// });

// 读取目录内容，返回目录列表
// console.log(fs.readdir('./', (err, files) => {
//   console.log(err, files);
// }));

// 读取文件内容
// console.log(fs.openSync('./test.txt', 'r'));
// 文件描述符读取内容
// fs.read(22, (err, number, buffer) => {
//   console.log(err, buffer);
// })
// 直接读取
// fs.readFile('./test.txt', (err, data) => {
//   console.log(data.toString());
// })
// 读取链接的值。也能通过 readFile 读取文件的内容
// fs.readFile('./new.txt', (err, data) => {
//   console.log(data.toString());
// })
// fs.readlink('./new2.txt', (err, link) => {
//   console.log(err, link);
// })

// 怎么会出现解析路径呢
// fs.realpath('./new.txt', (err, path) => {
//   console.log(err, path);
// })
// console.log(path.resolve('./new.txt'));

// 重命名文件和文件夹
// fs.rename('./test.txt', './test-new.txt', (err) => {
//   console.log(err);
// });

// 删除目录
// fs.rmdir('./test', (err) => {
//   console.log(err);
// })
// 删除文件
// fs.rm('./new.txt', err => {
//   console.log(err);
// })

// 返回文件在文件系统中的状态
// fs.stat('./test-new.txt', (err, stats) => {
//   console.log(err, stats);
// })
// lstat 是link stat
// fs.lstat('./new2.txt', (err, stats) => {
//   console.log(err, stats);
// })
// 文件系统的stat，需使用 fd 进行操作
// fs.fstat()

// 新建符号连接
// fs.symlink('./test-new.txt', 'file.txt', (err) => {
//   console.log(err);
// })

// 将文件内容截断为指定的长度
// fs.truncate('./test-new.txt', 1, (err) => {
//   console.log(err);
// })

// 删除文件或link
// fs.unlink('./test.txt', err => {
//   console.log(err);
// })

// 监视文件的改动，第二个参数处理类型和文件名称
// fs.watch('./test-new.txt', (eventType, filename) => {
//   console.log(eventType, filename);
// })
// 监听文件，监听器接受当前文件和上一次文件状态
// fs.watchFile('./test-new.txt', (cur, prev) => {
//   console.log(cur, prev);
// })
// 取消监控
// fs.unwatchFile('./test-new.txt')

// fs.utimes('./test-new.txt', new Date(), new Date(), err => {
//   console.log(err);
// })

// 可以写文件，不能创建文件夹
// fs.mkdir
// fs.writeFileSync('./test.txt', 'testtest', 'utf-8')

```

* 关于 `fs` 模块的特殊之处是，所有的方法默认情况下都是异步的，但是通过在前面加上 `Sync` 也可以同步地工作。

### 重点方法

* access 检测文件是否存在
* fs.stat 获取文件在系统中的状态！包括很多文件的基础属性



### 使用promise，而不是回调

* 官方提供了 promises 方法，看起来像是实验性的
* 官方推荐使用 fs-extra 库使用 promise 的方式

```typescript
// fs 提供了promise方案
// import fs from 'fs';

// const fsp = fs.promises

// fsp.readFile('./test-new.txt').then(res => {
//     console.log(res.toString());
// })

// 或者使用fs-extra 代替
import fs2 from 'fs-extra'

fs2.readFile('./test-new.txt').then(res => {
    console.log(res.toString());
})

```

* 所有的fs方法都能在这个库中使用，主要的区别就是支持promise。除此之外还加了一些方法



## process 进程

https://juejin.cn/post/6913498911973834759#heading-16

无需为此导入 `process` 核心模块，因为它是自动注入的。

主要进行两方面的事情：

* 获取进程信息（资源使用、运行环境、运行状态）—— 想象一下进程相关的东西都能获取到，和window功能有些相近。
* 执行进程操作（监听事件、调度任务、发出警告）

分为三类吧：系统环境信息、运行信息（包括传入参数等）、事件等

process 模块简介

```typescript
import os from 'os'

const { cpuUsage } = process

// 获取当前目录
console.log(process.cwd());

// process.chdir 会改变当前目录。
process.chdir('./testdir')

console.log(process.cwd());

console.log(__dirname);

// process 基础
// 第一部分：获取进程信息
// 1.1 资源使用
// 进程内存使用情况
console.log(process.memoryUsage());
/**
 * {
        rss: 206532608, // 常驻内存，计算参考：https://www.jianshu.com/p/9bf36aa82f90
        heapTotal: 150007808,
        heapUsed: 127605536,
        external: 4598984,
        arrayBuffers: 3374650
    }
 */

// cpu使用情况。返回当前进程用户和系统使用CPU时长。这些值分别衡量用户和系统代码所花费的时间，如果多个 CPU 内核正在为此进程执行工作，最终可能会大于实际经过的时间。
// 单位是微秒（百万分之一秒）
console.log(process.cpuUsage(), '---cpu');
// 先前调用的结果process.cpuUsage()可以作为参数传递给函数，以获取差异读数。
// const startUsage = cpuUsage();
// // { user: 38579, system: 6986 }

// // spin the CPU for 500 milliseconds
// const now = Date.now();
// while (Date.now() - now < 500);

// console.log(cpuUsage(startUsage));

// 1.2 运行环境: 运行环境指此进程运行的宿主环境包括运行目录、node环境、CPU架构、用户环境、系统平台
// 运行目录
console.log(process.cwd());
// node环境
console.log(process.version);
// 平台信息完整，主要是各种依赖版本信息
console.log(process.versions);
// cpu 架构。注意操作系统 os 模块同样能拿到该信息
console.log(process.arch, os.arch());
// 用户环境。需要再node之前注入，写到node执行之前。例子： NODE_ENV=test3 npx ts-node process.ts
console.log(process.env.NODE_ENV);
// 系统平台
console.log(process.platform);

// 1.3 运行状态：运行状态指当前进程的运行相关的信息包括启动参数、执行目录、主文件、PID信息、运行时间
// 获取启动参数。位置在文件名之后，参数一般放到最后边，和函数是一样的
console.log(process.argv);
// console.log(process.argv0, process.argv[0]);
// console.log(process.execArgv); // execArgv获取Node.js的命令行选项

// 执行目录 process.execPath 返回的是执行这个文件的环境变量目录
console.log(process.execPath, process.cwd());
// 运行时间 // 是不是时间循环记录的那个时间
console.log(process.uptime());
// 获取主文件相关信息
console.log(process.mainModule, '---main---', require.main);
// 获取进程id
console.log(process.pid);
```

* 注意环境是写到命令的最前面！参数是写到最后面！



### 事件

进程监听的事件主要是三类：本身的事件（本身进程退出）、异常事件、进程间通信的事件（系统信号等）

https://juejin.cn/post/6844903614784225287

process是EventEmiiter的实例对象，因此可以使用process.on('eventName', () => {})来监听事件。 常用的事件类型分两种：

- 进程状态 比如：beforeExit、exit、uncaughtException、message
- 信号事件 比如：SIGTERM、SIGKILL、SIGUSR1

#### beforeExit 和 exit

beforeExit与exit的区别有两方面：

- beforeExit里面可以执行异步代码、exit只能是同步代码(因为立马要执行)
- 手动调用process.exit()或者触发uncaptException导致进程退出不会触发beforeExit事件、exit事件会触发。

```typescript
// beforeExit，正常退出才会执行
process.on('beforeExit', () => {
    console.log('beforeExit exec');
    // 不能执行异步代码，否则时间循环不能回收！死循环！
    setImmediate(() => {
        console.log('beforeExit 异步执行');
    })
})

// 异常退出也可以执行
process.on('exit', () => {
    console.log('exit exec');
    // 异步代码不执行
    setImmediate(() => {
        console.log('exit 异步执行');
    })
})
```

#### uncaughtException 重要！

* uncaughtException 捕获之后不会再控制台进行错误输出

> `'uncaughtException'`是一种粗略的异常处理机制，仅用作最后的手段。该事件*不应*用作`On Error Resume Next`. 未处理的异常本质上意味着应用程序处于未定义状态。尝试在未从异常中正确恢复的情况下恢复应用程序代码可能会导致其他不可预见和不可预知的问题。
>
> ==从事件处理程序中抛出的异常将不会被捕获==。==相反，该进程将以非零退出代码退出，并且将打印堆栈跟踪。这是为了避免无限递归。==
>
> 在未捕获的异常后尝试正常恢复可能类似于在升级计算机时拔掉电源线。十次有九次，什么都没有发生。但是第十次，系统被破坏了。
>
> 的正确用法`'uncaughtException'`是在关闭进程之前对分配的资源（例如文件描述符、句柄等）执行同步清理。**在`'uncaughtException'`之后进行操作时不安全的！。**
>
> 为了以更可靠的方式重新启动崩溃的应用程序，无论是否 `'uncaughtException'`发出，都应在单独的进程中使用外部监视器来检测应用程序故障并根据需要恢复或重新启动。
>
> ——摘自 node 官网，https://nodejs.org/dist/latest-v18.x/docs/api/process.html#event-uncaughtexception

* 在这个事件中的回调中跑出错误不会被捕获。相反，该进程将以非零退出代码退出，并且将打印堆栈跟踪。这是为了避免无限递归。
* 在未捕获的异常后尝试正常恢复可能类似于在升级计算机时拔掉电源线。十次有九次，什么都没有发生。但是第十次，系统被破坏了。
* 在`'uncaughtException'`之后进行操作时不安全的！
* 更好的处理是不论这个事件是否出发都应该在外部进行检测，并在适当的时候进行恢复或者重新启动

**注意：node的9.3版本增加了process.setUncaughtExceptionCaptureCallback方法**

当process.setUncaughtExceptionCaptureCallback(fn)指定了监听函数的时候，uncaughtException事件将会不再被触发。



#### unHandledExcetion

* 和上面那个事件不重复，只有在 promise 错误未捕获时触发。

#### 两个异常事件的对比

* uncaughtException 作为一个兜底事件，任何未处理的异常（包括promise错误）都会被这个事件捕获到。

* unHandledException 只会捕获 promise 异常，并且不再继续传递下去。

### 信号

http://www.ay1.cc/article/8356.html

信号是操作信息提供了进程间通信机制。信号通常是一个数字，同时也可以使用一个字符串来标识。比如 SIGKILL 标识数字 9。不同的操作系统对信号的定义不同。下面表格里罗列的是基本通用的信号定义。

| 名称    | 数字 | 是否可处理 | NodeJS 默认行为 | 信号的含义                  |
| ------- | ---- | ---------- | --------------- | --------------------------- |
| SIGHUP  | 1    | Yes        | 退出            | 父命令行被关闭              |
| SIGINT  | 2    | Yes        | 退出            | 命令行尝试中断，即 Ctrl + C |
| SIGQUIT | 3    | Yes        | 退出            | 命令行尝试退出，即 Ctrl + Z |
| SIGKILL | 9    | No         | 退出            | 强制进程退出                |
| SIGUSR1 | 10   | Yes        | 启动调试器      | 用户自定义信号              |
| SIGUSR2 | 12   | Yes        | 退出            | 用户自定义信号              |
| SIGTERM | 15   | Yes        | 退出            | 进程优雅的退出              |
| SIGSTOP | 19   | No         | 退出            | 进程被强制停止              |

这表格里，是否可处理表示这个信号是否可被进程接收并被处理。NodeJS 默认行为表示进程在接收到这个信号以后默认执行的动作。

* 进程间通信：

  linux 常用两种：kill 和 管道符

#### 对正在进行中的node进程进行调试

```bash
kill -s SIGUSR1 pid
```





### 触发警告

```php
process.emitWarning('Something warning happened!', {
  code: 'MY_WARNING',
  type: 'XXXX'
});

// (node:14771) [MY_WARNING] XXXX: Something warning happened!
复制代码
```

当type为DeprecationWarning时，可以通过命令行选项施加影响

- `--throw-deprecation`  会抛出异常
- `--no-deprecation`  不输出DeprecationWarning
- `--trace-deprecation` 打印详细堆栈信息

```lua
process.emitWarning('Something warning happened!', {
  type: 'DeprecationWarning'
});
console.log(4);

node --throw-deprecation index.js
node --no-deprecation index.js
node --trace-deprecation index.js
```

* 推荐命令：

  ```bash
  node --loader ts-node/esm --trace-deprecation process-warning.ts
  ```

  

### process.cwd()

* 获取当前 node.js 的工作目录
* 注意和 __dirname 的区别

### process.chdir()

* 更改node.js 进程的当前工作目录，如果失败则抛出异常

### process.exit

* 退出程序

  根据 Unix 传统，程序执行成功返回 0，否则返回 1 。

### process.stdout

*在Node.js代码里使用的console.log其实底层依赖的就是process.stdout*

## child_process 子进程

https://juejin.cn/post/6844903615237193742

在Java语言中可以通过多线程的方式来解决上述的问题，但是Node.js在代码执行的时候是单线程的，那么Node.js应该如何解决上面的问题呢？其实Node.js可以创建一个子进程执行密集的cpu计算任务（例如上面例子中的longComputation）来解决问题，而child_process模块正是用来创建子进程的。

child_process提供了几种创建子进程的方式

- 异步方式：spawn、exec、execFile、fork
- 同步方式：spawnSync、execSync、execFileSync

### 子进程执行一个第三方命令

```typescript
import childProcess from 'child_process'

// const child = childProcess.spawn('pwd')
// 在Node.js代码里使用的console.log其实底层依赖的就是process.stdout
// 1
// child.stdout.pipe(process.stdout)
// 2. 利用可读流可写流的性质
// child.stdout.on('data', (chunk) => {
//     process.stdout.write(chunk)
// })

// 3. 共享一个 stdio
childProcess.spawn('pwd', {
    stdio: 'inherit',
})
```

```php
const { spawn } = require('child_process');
const child = spawn('pwd', {
  stdio: 'inherit'
});
复制代码
```

stdio选项用于配置父进程和子进程之间建立的管道，由于stdio管道有三个(stdin, stdout, stderr）因此stdio的三个可能的值其实是数组的一种简写

- pipe 相当于['pipe', 'pipe', 'pipe']（默认值）
- ignore 相当于['ignore', 'ignore', 'ignore']
- inherit 相当于[process.stdin, process.stdout, process.stderr]

由于inherit方式使得子进程直接使用父进程的stdio，因此可以看到输出

ignore用于忽略子进程的输出（将/dev/null指定为子进程的文件描述符了），因此当ignore时child.stdout是null。

### 第三方命令传递参数

#### spawn

spawn默认情况下并不会创建子shell来执行命令，因此下面的代码会报错



spawn 配置shell option创建子shell支持管道命令

```typescript
// spawn 配置shell option创建子shell支持管道命令
const child = spawn('ls -l', {
    shell: true,
  	cwd: '/usr', // 更改执行目录
  	env: { // 指定环境变量，并覆盖
      NODE_ENV: 'develop', 
    }
})
child.stdout.pipe(process.stdout)
```

#### exec

由于exec会创建子shell，所以可以直接执行shell管道命令。spawn采用流的方式来输出命令的执行结果，而exec也是将命令的执行结果缓存起来统一放在回调函数的参数里面，因此exec只适用于命令执行结果数据小的情况。

* 可以一股脑执行一长串shell

#### execFile

execFile与exec不同，execFile通常用于执行文件，而且并不会创建子shell环境

> 该`child_process.execFile()`功能类似于[`child_process.exec()`](https://nodejs.org/dist/latest-v18.x/docs/api/child_process.html#child_processexeccommand-options-callback) 默认情况下不生成外壳。相反，指定的可执行文件`file`直接作为新进程生成，使其比[`child_process.exec()`](https://nodejs.org/dist/latest-v18.x/docs/api/child_process.html#child_processexeccommand-options-callback).
>
> 支持的选项相同[`child_process.exec()`](https://nodejs.org/dist/latest-v18.x/docs/api/child_process.html#child_processexeccommand-options-callback)。由于未生成 shell，因此不支持 I/O 重定向和文件通配等行为。
>
> ——node官网

execFile 和 exec 的区别在于它并不会创建 shell，而是直接执行命令，所以会更高效一点，例如：

```js
const { execFile } = require("child_process")
const child = execFile("node", ["--version"], (error, stdout, stderr) => {
  if (error) throw error
  console.log(stdout)
})
```

==由于没有创建 shell，程序的参数作为数组传入，因此具有较高的安全性。==

* https://juejin.cn/post/7015860859662499854

#### fork

fork方法是spawn方法的一个特例，fork用于执行js文件创建Node.js子进程。而且fork方式创建的子进程与父进程之间建立了IPC通信管道，因此子进程和父进程之间可以通过send的方式发送消息。

> *注意：fork方式创建的子进程与父进程是完全独立的，它拥有单独的内存，单独的V8实例，因此并不推荐创建很多的Node.js子进程*

* 并不一定能节省时长，但是肯定能不阻塞！
* 抛出错误会阻塞！！

主进程

```typescript
import {fork} from 'node:child_process'

const forked = fork('./child.ts')
forked.on('message', (message) => {
    // console.log(message, '---parent receive message');
    console.log(a);
    console.timeEnd('test')
})
forked.send('parent sent message')
```

子进程

```typescript
console.log('child exec', new Date().getTime());
let b = 0
// process.stdout.write('干啥呢')
process.on('message', (message) => {
    // console.log(message, 'child receive message');
})
setTimeout(() => {
    // 必须建立 ipc 通道才能使用send方法，建议进行判断
    process.send!('child send message')
}, 500);
```

* 子进程一旦引入便开始执行了，相当于web api 帮助你处理一些事情，并在合适时机触发回调
* 主要是利用 process 的 message 事件进行通信！



总结：

* spawn：默认不开启新 shell，可以开启。采用流式的传递方案，能够处理大数据量的输出
* exec：会创建子 shell，可以一股脑执行命令，会缓存结果在回调函数中传递
* execFile: 不创建子 shell，会缓存结果
* fork: fork方式创建的子进程与父进程是完全独立的，它拥有单独的内存，单独的V8实例

### 执行一个第三方命令

```typescript
import child from 'child_process'

child.exec('npx ts-node index.ts', (err, stdout, stderr) => {
    err?.killed
    console.log(err?.code, '____', stdout, '____', stderr);
})

```

* 可以用 err 获取退出的code

### 事件

* close 事件，在exit之后执行

* exit 事件

* error事件

  - 无法创建进程
  - 无法结束进程
  - 给进程发送消息失败

  *注意当代码执行出错的时候，error事件并不会触发，exit事件会触发，code为非0的异常退出码*

* message 用户IPC通信

  message除了发送字符串、object之外还支持发送server对象和socket对象，正因为支持socket对象才可以做到多个Node.js进程监听相同的端口号。

  ```typescript
  // 测试一下是否能直接发送socket
  import net, { createConnection } from 'net'
  import childProcess, { fork } from 'node:child_process'
  
  const socket = createConnection({
      host: '127.0.0.1',
      port: 8093,
  })
  
  // message除了发送字符串、object之外还支持发送server对象和socket对象，正因为支持socket对象才可以做到多个Node.js进程监听相同的端口号。
  const forked = fork('../child.ts')
  forked.send(socket)
  ```

### 向子进程传递一个 socket 对象（或者server）

* 传递需要两个参数

  ```typescript
  child.send('server', server)
  ```

  * 子进程

  ```typescript
  process.on('message', (message, tcp: net.Server) => {
      if (message === 'server') {
          // console.log(tcp, message, '---receive data');
          // server.emit('connection', tcp)
          tcp.on('connection', socket => {
              console.log('执行');
              // 用户也可以显式发出此事件以将连接注入 HTTP 服务器 // todo 笔记
              server.emit('connection', socket)
          })
      }
  })
  ```

==一个父进程代理端口的案例==，这样就可以做到多个子进程共享一个端口的案例！

* 父进程

```typescript
import { fork } from 'child_process'
import net, { createServer } from 'net'

const child = fork('./child.ts')
const server = createServer(socket => {
})
server.listen(8096, () => {
    // 把tcp请求外包给子进程 
    // 只有socket 和 server 能这样传值
    child.send('server', server)
    // server.close()
})
```

* 子进程

```typescript
import net from 'net'
import http, { createServer } from 'http'

const server = createServer((req, res) => {
    res.end('hello')
})

// todo 笔记
process.on('message', (message, tcp: net.Server) => {
    if (message === 'server') {
      	// 需要再连接事件之后触发
        tcp.on('connection', socket => {
            console.log('执行');
            // 用户也可以显式发出此事件以将连接注入 HTTP 服务器 // todo 笔记
            server.emit('connection', socket)
        })
    }
})

```





### kill 方法

* 子进程能够通过这个方法传递信号

```typescript
import { exec } from "child_process";

// 采用回调函数的方式。并且可以直接执行管道符命令。建议输出数据较少的命令使用这个方法
// exec('ls -l | grep exec', (err, stdout, stderr) => {
//     console.log(stdout);
// })

const child = exec('node timer.js', {
    timeout: 300
})

// 信号退出的例子
child.on('exit', (code, signal) => {
    console.log('exit exec', code, signal);
})
child.kill('SIGTERM')

```



### `subprocess.unref()`

* *// 父进程不等待子进程退出*。需要设置 stdio: 'ignore'

  不会再获取到子进程的任何消息

```typescript

const child = spawn(
    // 'ls -l',
    'echo $NODE_ENV', // 输出环境变量
    {
        shell: true,
        cwd: '/usr', // 更改执行目录
        env: {
            NODE_ENV: 'new'
        },
        stdio: 'ignore',
    }
)
// child.stdout.pipe(process.stdout)

// close事件当子进程的stdio流关闭的时候才会触发，并不是子进程exit的时候close事件就一定会触发，因为多个子进程可以共用相同的stdio
// 事件
child.on('exit', (code, signal) => {
    console.log('exit exec', code, signal);
})
child.on('close', (code, signal) => {
    console.log('close exec', code, signal);
})

// console.log(child.env);
// todo
// 父进程不等待子进程退出
child.unref()
```



### 父子进程的关系及内存共享

https://www.cnblogs.com/-colin/p/7990053.html

子进程会复制父进程的几乎所有信息：子进程复制父进程用户空间所有数据；

　　　　　　　　　　　　　　　　　子进程复制父进程内核空间PCB中绝大多数数据；

## stdio 流

stdin、stdout、stderr

## http 模块

HTTP 核心模块是 Node.js 网络的关键模块。

分为三块：属性、方法、类

### 属性

```typescript
// http支持的方法
// console.log(http.METHODS);
// 状态码及其描述
// console.log(http.STATUS_CODES);
// 返回Agent对象的全局实例。用于管理 HTTP 客户端连接的持久性和复用，它是 Node.js HTTP 网络的关键组件。
// console.log(http.globalAgent);
```

### 方法

首先 Node.js 的 http 库提供三个主要的函数： http.request, http.get, http.createServer

https://www.zhihu.com/question/58996077/answer/518247970

> 前两个函数主要是为了创建 http [客户端](https://www.zhihu.com/search?q=客户端&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A518247970})，向其它服务器发送请求，http.get 只是 http.request 的发送 get 请求的便捷方式；而 http.createServer 是为了创建 Node 服务，比如 Koa 服务框架就是基于 http.createServer 封装的。

### 类

HTTP 模块提供了 5 个类：

- `http.Agent`
- `http.ClientRequest`
- `http.Server`
- `http.ServerResponse——可读流`
- `http.IncomingMessage——可写流`

#### http.Agent

https://www.zhihu.com/question/58996077/answer/518247970

* http.Agent 主要为了服务端作为http客户端发起请求时的代理。用户管理http连接的创建，销毁和复用工作。

  用于定制 agent 覆盖掉全局的 globalAgent

而 http.Agent 主要是为 http.request, http.get 提供代理服务的，用于管理 http 连接的创建，销毁及复用工作。http.request, http.get 默认使用 http.globalAgent 作为代理，每次请求都是“建立连接-数据传输-销毁连接”的过程，如果我们想让多个请求复用同一个 connection，则需要重新定义 agent 去覆盖默认的 http.globalAgent，下面我们看一下新建一个agent的需要哪些主要参数：

- keepAlive：{Boolean} 是否开启 keepAlive，多个请求公用一个 socket connection，默认是 false。

- maxSockets：{Number} 每台主机允许的[socket](https://www.zhihu.com/search?q=socket&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A518247970})的最大值，默认值为Infinity。

- maxFreeSockets：{Number} 处于连接池空闲状态的最大连接数， 仅当开启 keepAlive 才有效

  为什么有空闲状态额连接。什么时候需要回收。

  > 只有向相同的 host 和 port 发送请求才能公用同一个 keepAlive 的 socket 连接，如果开启 keepAlive ，同一时间内多个请求会被放在队列里等待；如果当前队列为空，该 socket 处于空闲状态，但是不会被销毁，等待下一次请求的到来。

```typescript
const agent = new http.Agent({
    keepAlive: true,
    maxFreeSockets: 5,
    maxSockets: 1000,
    scheduling: 'fifo', // 两种调度策略的主要区别在于'lifo' 选择最近使用的socket，而'fifo'选择最近最少使用的socket。具体参考官方文档
    timeout: 500, // 超时设置
});
http.get('http://nodejs.cn/api/path.html#pathdirnamepath', {
    agent,
}, res => {
    console.log(res);
})
```

#### http.ClientRequest

* 当 `http.request()` 或 `http.get()` 被调用时，会创建 `http.ClientRequest` 对象。

  这个对象可以监听获取（属性）、管理（主动关闭等）、监听请求连接的状态。

```typescript
const clientReq = http.get('http://localhost:8091/', {
    agent,
}, res => {
    console.log(res);
})
clientReq.on('close', () => {
    console.log('关闭了！');
});
```

#### 作为客户端发送请求怎么获取数据呢？

有两种方式：

* 可以调用 `response.read()` 方法。res 是一个 readable 流，参考readable类的 read 方法(只能在 readable事件中触发)
* 在 `response` 事件处理函数中，可以为 `data` 事件设置事件监听器，以便可以监听流入的数据。

```typescript
const clientReq = http.get('http://localhost:8091/', {
    agent,
}, res => {
    let rawData = '';
    // console.log(res);
    res.on('data', (data: Buffer) => {
        console.log(data, '---data');
        rawData += data;
    })
    res.on('readable', () => {
        console.log(res.read().toString(), '---read'); // 方法1
    })
    res.on('end', () => {
        try {
            console.log(rawData, '---rawData'); // 方法2
        } catch (err) {
            console.log(err);
        }
    })
})
```

#### http.Server

当使用 http.createServer() 创建一个新的服务器时，通常会实例化返回此类

拥有服务器对象后，就可以访问其方法：——套路都是一样的！返回一个实例，能控制流程的终止和监听流程，比如一个http.get 方法。

- `close()` 停止服务器不再接受新的连接。
- `listen()` 启动 HTTP 服务器并监听连接。

##### 一个比较重要的事件——connection

* 这个事件能够由 server主动触发，用户也可以显式发出此事件以将连接注入 HTTP 服务器
* 需要传入一个 socket 实例

用法：能够做socket传递，也就是能够由一个 tcp 代理进行socket传递给一个http服务器

1. 向子进程传递socket让子进程进行响应处理
2. 单纯的做一个为tcp代理

一个 tcp转发的例子

```typescript
// 下面是一个tcp转发的例子
import { createServer } from 'http'
import net, { Server } from 'net'

/**
 * 1. 创建一个socket链接（tcp）
 * 2. tcp监听连接事件，连接成功之后就把socket转发给http请求
 * 3. http请求主动触发socket连接，处理响应
 */

const server = createServer((req, res) => {
    console.log(req.url);
    res.end('hello2')
})
// server.emit('connection', )
const tcp = new Server((socket) => {
})
tcp.listen(8097, () => {
    console.log('server is running');
})

// 建立链接之后会给一个socket
tcp.on('connection', socket => {
    server.emit('connection', socket)
    server.close()
})

```





#### `http.ServerResponse`

由 `http.Server` 创建，并作为第二个参数传给它触发的 `request` 事件。

在事件处理函数中总是会调用的方法是 `end()`，它会关闭响应，当消息完成时则服务器可以将其发送给客户端。 必须在每个响应上调用它。

```typescript
res.end();
```

* res.setHeader 方法设置的key遵循首字母大写的方式

  ```typescript
  res.setHeader('Content-Type', 'application/json')
  ```

##### 处理header

- `getHeaderNames()` 获取已设置的 HTTP 消息头名称的列表。
- `getHeaders()` 获取已设置的 HTTP 消息头的副本。
- `setHeader('headername', value)` 设置 HTTP 消息头的值。
- `getHeader('headername')` 获取已设置的 HTTP 消息头。
- `removeHeader('headername')` 删除已设置的 HTTP 消息头。
- `hasHeader('headername')` 如果响应已设置该消息头，则返回 true。
- `headersSent()` 如果消息头已被发送给客户端，则返回 true。

```typescript
const server = http.createServer((req, res) => {
    // res.writeHead(200, { 'Content-Type': 'application/json' })

    // http消息头进行交互
    res.setHeader('Content-Type', 'application/json')
    // res.removeHeader('content-type')
    console.log(res.getHeaderNames(), '---names'); // 获取已经设置的 header key值
    // console.log(res.getHeaders()); // 获取已经设置的header对象
    // console.log(res.getHeader('content-type')); // 获取单个header
    console.log(res.hasHeader('content-type')); // 判断是否有某个header
    res.writeHead(200, {
        test: 222
    }) // 处理完消息头后使用这个方法将header发送到客户端，这个之后不能再操作header
    console.log(res.headersSent, '--00'); // 判断header是否已经被发送到了客户端

    // 在事件处理函数中总是会调用的方法是 end()，它会关闭响应，当消息完成时则服务器可以将其发送给客户端。 必须在每个响应上调用它。
    res.end(JSON.stringify({
        test: 222
    }));
})
```

1. 处理header完成之后，需要使用 writeHead 将header发送到客户端。（也可以再次定制header）

2. 也可以通过直接修改code的方式修改响应头

   ```typescript
   // 重新编辑状态和message
   res.statusCode = 500
   res.statusMessage = 'Internal Error' // code 码后边的消息
   ```

#### `http.IncomingMessage`

`http.IncomingMessage` 对象可通过以下方式创建：

- `http.Server`，当监听 `request` 事件时。
- `http.ClientRequest`，当监听 `response` 事件时。

常用的方法和属性

- 使用 `statusCode` 和 `statusMessage` 方法来访问状态。
- 使用 `headers` 方法或 `rawHeaders` 来访问消息头。
- 使用 `method` 方法来访问 HTTP 方法。
- 使用 `httpVersion` 方法来访问 HTTP 版本。
- 使用 `url` 方法来访问 URL。
- 使用 `socket` 方法来访问底层的 socket。是一个可写流

```typescript
const server = http.createServer((req, res) => {

    // http.IncomingMessage 类
    // console.log(req.statusCode, '--req--', req.statusMessage); // 响应码和响应信息（只有作为客户端请求的时候才会有）
    // console.log(req.headers, '--req--', req.rawHeaders); // 获取请求头信息。req.rawHeaders 是一个字符串数组
    console.log(req.method, '--method'); // 请求方法
    console.log(req.httpVersion, '---version'); // 请求版本
    console.log(req.url); // 请求路由
    console.log(req.socket); // 获取底层的 socket。是一个可写流
    req.socket.on('data', chunk => {
        console.log(chunk.toString());
    })
}
```

* 因为 `http.IncomingMessage` 实现了可读流接口，因此数据可以使用流访问。

  比如说用 data 事件去拿数据

#### 处理get请求

* 官方推荐使用：URLSearchParams

```typescript
// 获取 get 请求参数
// 方法1：使用 URLSearchParams，官方推荐！
const search = req.url?.split('?')[1]
const searchArr = new URLSearchParams(search)
console.log(searchArr.get('test1'), searchArr.keys());

// 方法2：使用querystring
// const searchRe = querystring.parse(search!)
// console.log(searchRe.test1);
```



### 类总结

* http中的类一般都继承了流接口，流接口一般都继承了eventEmitter接口

### 关于 get 和 post 请求的讨论请查看http相关笔记

https://www.zhihu.com/question/549907469/answer/2654938556



### 关于http请求的思考

* 如果一个全局变量每次请求都修改它，它会进行累积

## cluster

* 与 child_process 区别

  cluster 是创建多个工作进程；child_process 是创建一个子进程。其实也差不多都是父子进程的关系

### 属性

* cluster.isPrimary 是否是主进程
* cluster.isWorker 是否是工作进程

### 方法

* cluster.fork()

  https://juejin.cn/post/6844903795252527118

  > 学过操作系统的同学，应该对 fork() 这个系统调用不陌生，调用它的进程为父进程，fork 出来的都是子进程。子进程和父进程具有相同的==代码段、数据段、堆栈==，但是它们的内存空间不共享。父进程（即 master 进程）负责监听端口，接收到新的请求后将其分发给下面的 worker 进程。这里涉及三个问题：父子进程通信、负载均衡策略以及多进程的端口监听。
  >
  > 备注：Linux 上 fork() 支持写时复制，只有进程空间的各段的内容要发生变化时，才会将父进程的内容复制一份给子进程。因此子进程和父进程一开始是共享相同的内存空间。

​	正因为它们共享堆栈，所以子进程崩溃，父进程也会退出

一个 primary 对应多个 work 进程



### 负载均衡策略

https://juejin.cn/post/6844903795252527118

前面提到，所有请求是通过 master 进程分配的，要保证服务器负载比较均衡的分配到各个 worker 进程上，这就涉及到负载均衡策略了。Node.js 默认采用的策略是 **round-robin** 时间片轮转法。

round-robin 是一种很常见的负载均衡算法，Nginx 上也采用了它作为负载均衡策略之一。它的原理很简单，每一次把来自用户的请求轮流分配给各个进程，从 1 开始，直到 N(worker 进程个数)，然后重新开始循环。这个算法的问题在于，它是假定各个进程或者说各个服务器的处理性能是一样的，但是如果请求处理间隔较长，就容易导致出现负载不均衡。因此我们通常在 Nginx 上采用另一种算法：**WRR**，加权轮转法。通过给各个服务器分配一定的权重，每次选出权重最大的，给其权重减 1，直到权重全部为 0 后，按照此时生成的序列轮询。

可以通过设置 NODE_CLUSTER_SCHED_POLICY 环境变量，或者通过 cluster.setupMaster(options) 来修改负载均衡策略。==读到这里大家可以发现，我们可以 Nginx 做多机器集群上的负载均衡，然后用 Node.js Cluster 来实现单机多进程上的负载均衡。==

node cluster 负载参考：https://nodejs.org/dist/latest-v18.x/docs/api/cluster.html#how-it-works

* 建议使用 nginx 做多机器集群上的负载均衡，用 node cluster 做单机多进程上的负载均衡。



## node IPC 总结

https://juejin.cn/post/6844903795252527118

* 共享内存 不同进程共享同一段内存空间。通常还需要引入信号量机制，来实现同步与互斥。

* 消息传递 这种模式下，进程间通过发送、接收消息来实现信息的同步。

* 信号量 信号量简单说就是系统赋予进程的一个状态值，未得到控制权的进程会在特定地方被强迫停下来，等待可以继续进行的信号到来。如果信号量只有 0 或者 1 两个值的话，又被称作“互斥锁”。这个机制也被广泛用于各种编程模式中。

* 管道 管道本身也是一个进程，它用于连接两个进程，将一个进程的输出作为另一个进程的输入。可以用 pipe 系统调用来创建管道。我们经常用的“ | ”命令行就是利用了管道机制。

Node.js 为父子进程的通信提供了事件机制来传递消息。下面的例子实现了父进程将 TCP server 对象句柄传给子进程。

### 没有父子关系怎么进程通信





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



# RPC 调用

• Remote Procedure Call(远程过程调用)

* 和 Ajax 有什么相同点?

  * 都是两个计算机之间的网络通信 

  * 需要双方约定一个数据格式
*  和 Ajax 有什么不同点?
  * 不一定使用 DNS 作为寻址服务，rpc一般是在内网进行通信请求
  * 应用层协议一般不使用 HTTP。一般使用二进制协议，http超文本传输协议
  * 基于TCP或UDP协议。Ajax： http + tcp协议

* 寻址方式、应用层协议、传输层协议

## 寻址过程

* 拿一个ip去寻址服务器（内网）找到一个内网IP

## 理解 RPC

RPC（Remote Procedure Call）中文名『远程过程调用』，又是一个很蹩脚的翻译。我们拆开理解下，==『过程』也叫方法或函数==，==『远程』就是说方法不在当前进程里，而是在其他进程或机器上面==，合起来 RPC 就是==调用其他进程或机器上面的函数==。

> 在没有网络的时代，程序都是单机版的，所有逻辑都必须在同一个进程里。进程之间就像高楼大厦里面陌生的邻居，大家无法共享，遇到同样的功能只能重复实现一次。显然进程的障碍是逆天的，不符合先进生产力的发展方向，这个时候『进程间通信』的需求出现了，大家要求进程之间能够相互交流，相互共享和调用。这样再写程序，就可以利用进程间通信机制来调用和共享已经存在的功能了。随着网络的出现，进程的隔阂进一步消除，不光同一栋楼里的邻居可以共享资源，其他小区、甚至其他城市的居民都可以通过互联网互相调用，这就是 RPC。概念很容易理解，==但是远程和本地的实现原理有很大区别，架构设计者的职责就是设计一个机制让远程调用服务就像调本地服务一样简单，这就是 RPC 框架。==



## 为什么需要 RPC

* 服务端拆分，异构语言，约定一个通用的调用方式解决该问题。语言隔阂
* 进程隔阂。有的功能已经被其他服务实现了，能不能直接复用呢。



## 基本原理

RPC 首要解决的是通讯的问题，==主流的 RPC 框架分为基于 HTTP 和基于 TCP 的两种==。基于 HTTP 的 RPC 调用很简单，就和我们访问网页一样，只是它的返回结果更单一（JSON 或 XML）。它的优点在于实现简单，标准化和跨语言（基本所有语言都有http相关库），比较适合对外提供 OpenAPI 的场景，而它的缺点是 HTTP 协议传输效率较低、短连接开销较大（HTTP 2.0 后有很大改进）。而基于 TCP 的 RPC 调用，由于 TCP 协议处于协议栈的下层，能够更加灵活地对协议字段进行定制，减少网络开销，提高性能，实现更大的吞吐量和并发数。但是需要更多地关注底层复杂的细节（需要语言提供 socket 编程），跨语言和跨平台难度大，实现的代价更高，它比较适合==内部系统之间追求极致性能的场景。（内部系统，因为需要定制协议，做不到通用）==

* 目前大厂使用的都是基于 TCP 实现的 RPC 方案，高速服务框架*HSF*（High-speed Service Framework）。

### RPC 基本调用过程

![rpc-base-process](./img/rpc-base-process.png)

1. 调用方（Client）通过本地的 RPC 代理（Proxy）调用相应的接口
2. 本地代理将 RPC 的服务名，方法名和参数等等信息转换成一个标准的 RPC Request 对象交给 RPC 框架
3. RPC 框架采用 RPC 协议（RPC Protocol）将 RPC Request 对象序列化成二进制形式，然后通过 TCP 通道传递给服务提供方 （Server）
4. 服务端（Server）收到二进制数据后，将它反序列化成 RPC Request 对象
5. 服务端（Server）根据 RPC Request 中的信息找到本地对应的方法(可以按照文件目录的方式直接找到该方法)，传入参数执行，得到结果，并将结果封装成 RPC Response 交给 RPC 框架
6. RPC 框架通过 RPC 协议（RPC Protocol）将 RPC Response 对象序列化成二进制形式，然后通过 TCP 通道传递给服务调用方（Client）
7. 调用方（Client）收到二进制数据后，将它反序列化成 RPC Response 对象，并且将结果通过本地代理（Proxy）返回给业务代码（这一块的实现还不是很清楚???）

### 通讯层的设计

因为==在 TCP 通道（就是socket）里传输的数据只能是二进制形式的==，所以我们必须将数据结构或对象转换成二进制串传递给对方，这个过程就叫『序列化』。而相反，我们收到对方的二进制串后把它转换成数据结构或对象的过程叫『反序列化』。而序列化和反序列化的规则就叫『协议』。



## RPC 请求过程

### 寻址/负载均衡

* RPC：使用特有服务进行寻址

![rpc](./img/rpc-process.jpg)

* al 使用 vip 方式去寻址 -> 寻址服务器

### 通信方式

* tcp 通信方式：

  * 单工通信
    * 只能一方往另一方发包，如果需要反过来，需要重新建立 tcp 链接

  * 半双工通信
    * 同一时间内只能一端向另一端发送数据
    * 轮番单工通信
  * 全双工通信
    * 交叉的方式进行通信

* 区别：实现难度和成本

### 使用协议

* 二进制协议
  * 更小的数据包体积
  * 更快的编解码速率

### 实现二进制协议

序列化： 将 [数据结构](https://www.zhihu.com/search?q=数据结构&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"459586615"})或对象转换成二进制串的过程

反序列化：将在序列化过程中所生成的二进制串转换成数据结构或者对象的过程

结构化数据

* 重复调用 buffer.write 方法，不同的位数存储不同的信息。（offset）

  手动实现太麻烦

* 谷歌实现的 protocol buffer，二进制协议

  推荐使用 protocol buffers 这个工具，专门为node实现

### 总结

* 寻址 + 负载均衡 有运维去消化
* 多路复用 + 二进制协议 bff 需要理解和实现



## node buffer

* 参考：http://nodejs.cn/api/buffer.html#bufferfrom-bufferalloc-and-bufferallocunsafe
* 拓展阅读：https://juejin.cn/post/6844903607238656008#heading-18

它表示在 ==V8 JavaScript 引擎外部==分配的固定大小的内存块（无法调整大小）。

可以将 buffer 视为整数数组，每个整数代表一个数据字节。

* 引入 buffer 用来处理二进制数据

### Buffer

* Buffer是内存区域，就是一块内存区域。Buffer所开辟的是堆外内存。==不占据js内存==
* 它表示在V8 js引擎==外部分配==的==固定大小的==的内存块（无法调整大小）
  * 外部分配：开发者直接调用
  * 固定大小：不能重新调整结构
* 由node Buffer类实现
* buffer中二进制数据是用16进制表示的



### 为什么需要buffer

==Buffer引入用于帮助开发者处理二进制数据==（之前js是没有直接处理二进制数据的能力的），在此之前，js生态系统中只处理字符串而不是二进制数据。

==buffer与流紧密相连（一般情况下只有流会直接产生二进制数据）==，当流处理接收数据的速度快于其消化的速度时，则会将数据放入buffer中。因为有些数据是不能直接用字符串保存的（需要转义），还不如直接保存二进制。

> 一个简单的场景是：当观看 YouTube 视频时，红线超过了观看点：即下载数据的速度比查看数据的速度快，且浏览器会对数据进行缓冲。

### 创建buffer——Buffer.from 、 Buffer.alloc()、Buffer.allocUnsafe()

一版就用这两种情况创建新的 buffer 实例。和数组有点相似，创建填充满，或者创建一个空的。

buffer 每一项都是一个 8位二进制数据，直接打印为 16 进制显示

* `Buffer.from`：从现有的一个字符串或者数组创建出一个 buffer 实例。长度和字符一一对应

```js
const buffer1 = Buffer.from('test')
// 产生 4 位 buffer 每一位代表一个字符
const buffer2 = Buffer.from([1,2,3])
// 产生三个 buffer 每一位是数组的一个元素
```

* Buffer.alloc(): 按规定长度创建出一个 buffer 实例，长度是字节为单元。也就是buffer数组的长度

  ```js
  const buffer3 = Buffer.alloc(20)
  // 长度位 20 的一个为空的 buffer （每一位都是 00）
  ```

* Buffer.allocUnsafe()

  > 虽然 `alloc` 和 `allocUnsafe` 均分配指定大小的 `Buffer`（以字节为单位），但是 `alloc` 创建的 `Buffer` 会被使用零进行初始化，而 `allocUnsafe` 创建的 `Buffer` 不会被初始化。 这意味着，尽管 ==`allocUnsafe` 比 `alloc` 要快得多==，但是分配的内存片段可能包含可能敏感的旧数据。(==不安全，可能包含旧数据==)

  读取buffer内存时，使用allocUnsafe是不安全的，可能访问到旧数据。

### 读取 buffer

#### 索引访问

* 像数组一样

#### tostring

* 提供了tostring方法，默认采用utf-8编码

#### 迭代

可以使用数组的方式去迭代获取



### 更改buffer内容

* write方法

  直接会替换掉buffer的内容

* 用索引替换

  可以使用索引进行替换某个字节

* buffer提供了一个copy方法，能够拷贝并直接修改参数的buffer

  ```typescript
  buf.copy(bufferCopy, 2, 0, 2) // 参数：拷贝者，拷贝者start，被拷贝者start，被拷贝者end
  ```

  

### 重写方法

* 主要是 read**BE & read\*\*LE，BE 和 LE 区别就是大小。大端和小端。https://developer.mozilla.org/zh-CN/docs/Glossary/Endianness

  使用这些 方法能够对 buffer 对象进行读写操作。

* ==只有超过两个字节的才能写大端和小端，比如说16位==

* Todo ： utf-8 ?

```typescript
let writeBuffer = Buffer.alloc(8)

/**
 * 32位代表占据8个字节中的前4个；
 * LE 代表小位数排到前边，大位数排到后面；BE则相反；
 * 如果使用 write32，那么这个buffer得长度就必须大于等于4，否则报错
 * Int 代表整形。写的数不能超过32位表示最大整数，[2 ^ 31, 2 ^ 31 -1]
 * UInt 代表只有正整数，U代表无符号。如果32位，范围：[0, 2 ^ 32 - 1]
 * BigInt 只能接受大整数
 * Double 双精度
 * float 单精度
 */
// writeBuffer.writeUInt32BE(4294967295)
// writeBuffer.writeBigInt64BE(9n);
writeBuffer.writeDoubleBE(11.11);
writeBuffer.writeFloatBE(11.222);
// writeBuffer.writeInt16BE(126)

// [-128, 127] 范围，超出报错
// writeBuffer.writeInt8(127)

// writeBuffer.writeBigInt64BE(97n)

console.log(writeBuffer, '---', writeBuffer.toString())
// writeBuffer.writeFloatBE
```



```js
const buffer1 = Buffer.from([1,2,3,4])

// buffer1.writeInt16BE(256, 1)
buffer1.writeInt16LE(256, 1) // 16 进制需要占两位

console.log(buffer1)
```

```js
buf.writeInt8(value[, offset]) // value范围是[-128,127]
buf.writeInt16BE(value[, offset])
// 这些方法的区别就是添加的 value 的大小是有限制的
```

```js
buf.writeBigInt64BE() // 需要一个bigint类型的数据
```



### 读取buffer

* 和上面一样，读取的时候就是按写入的二进制顺序进行读取（翻译成指定的格式）
* 返回值是一个十进制数据



* write 方法

  所有的write方法都需要源对象有一个符合长度的空间，比如writeInt32BE，那么源buffer至少有4个字节的长度，小于4个字节会报错。



### 是否包含特定值

```typescript
// 是否包含特定值
console.log(buf.includes('tes'), buf.indexOf('tes'))
```



### buffer 合并

```typescript
// buffer 合并，生成一个新的buffer
console.log(Buffer.concat([buf, bufferCopy]).toString())
// 直接字符串相加也可以，在网络请求中常见
let new = '' + buffer1 + buffer2
```



### buffer 的操作（计算）

buffer 很多操作都挂载到了 Buffer 这个对象上。比如: concat



### buffer 模块与 Buffer 的关系

* Buffer 就是 buffer 模块导出的

* buffer 其他属性

  * kMaxLength：buffer 最大长度

  ```typescript
  // buffer 模块
  console.log(buffer.kMaxLength, buffer.constants)
  // buffer.kMaxLength 新建buffer时内存大小的最大值，默认 4G
  ```

  



### Protocol Buffer

* 使用 Protocol Buffer 工具能够快速的对二进制协议进行编解码。node 环境
* 参考：https://github.com/mafintosh/protocol-buffers



### 切片

如果要创建 buffer 的局部视图，则可以创建切片。 切片不是副本：原始 buffer 仍然是真正的来源。 如果那改变了，则切片也会改变。

* 切片是会跟着buffer的修改而修改的，因此做切片上传提供了方案。





### toString 方法

* 能够解码，默认 `utf-8`

  如果超出了 unicode 码的范围，则解析不出任何数据

* http://nodejs.cn/api/buffer.html#buftostringencoding-start-end



总结：

* toString 和 readInt** 等方法能够 decode buffer，但是 toString 有局限性。



## node 中的 IO

* http 请求
* process.stdin 控制台输入/输出
* fs 文件读取



## node stream

流是为node.js应用程序提供动力的基本概念之一。在unix 操作系统中是 |

==它们是一种以高效的方式处理读/写文件、网络通信、或任何类型的端到端的信息交换。==

* 推荐阅读

  https://juejin.cn/post/6844903891083984910#heading-9

### 应用场景

stream 的应用场景主要是处理 IO 操作

* 输出控制台

  ```typescript
  process.stdin
  ```

* http 请求

  Req、res 都是stream对象

* 读取文件

  ```typescript
  const stream = fs.createReadStream('./file.txt', {
    encoding: 'utf-8',
  })
  ```

> 文件`IO`流，`Unix`系统标准输入输出流，标准错误流(`stdin`, `stdout`, `stderr`)，还有一开始提到的 `TCP` 流，还有一些 `Web` 后台技术（如`Nodejs`）对`HTTP`请求/响应流的抽象，都可以见到流的概念。
>
> ——https://juejin.cn/post/6910943445569765384#heading-11

流不是 Node.js 特有的概念。 它们是几十年前在 Unix 操作系统中引入的，程序可以通过管道运算符（`|`）对流进行相互交互。

例如，在传统的方式中，当告诉程序读取文件时，这会将文件从头到尾读入内存，然后进行处理。

使用流，则可以逐个片段地读取并处理（而无需全部保存在内存中）。

Node.js 的 [`stream` 模块](http://nodejs.cn/api/stream.html) 提供了构建所有流 API 的基础。 所有的流都是 [EventEmitter](http://nodejs.cn/api/events.html#events_class_eventemitter) 的实例。

* 增量的读取文件，不用整个文件读入内存。
* 一小段一小段的 chunk

### 为什么使用stream

相对于使用其他的数据处理方法，流基本上提供了两个主要优点：

* 内存效率：无需加载大量的数据到内存中即可进行处理
* 时间效率：当获取数据之后立即开始处理数据，这样所需的时间更少，而不必等到整个数据有效负载可用才开始。



### 与 buffer 联系

https://juejin.cn/post/6844903607238656008#heading-0

> 怎么理解流呢？流是数据的集合（与数据、字符串类似），但是流的数据不能一次性获取到，数据也不会全部load到内存中，因此流非常适合大数据处理以及断断续续返回chunk的外部源。==流的生产者与消费者之间的速度通常是不一致的，因此需要buffer来暂存一些数据==。buffer大小通过highWaterMark参数指定，默认情况下是16Kb。



### 继承了 eventEmitter

* 所以能够监听各种事件和触发各种事件



### 流速控制

推荐参考：https://zhuanlan.zhihu.com/p/398967492

* 有一个问题，读取速度大于写入速度就会造成buffer堆积问题——背压问题

> 编程中的 Backpressure ，**通常意义上是指「上游生产速度大于下游消费速度，导致下游的Buffer溢出」。**
>
> 也就是常说的 ==背压问题==

1. 暂停读入，没有暂停写入

   readStream.pause()

2. 继续读入

   readStream.resume()

3. 手动写入，返回Boolean，

   > - true：堆积的数据小于highWaterMark，可以继续写操作。
   > - false: 暂停写操作，数据堆积在缓冲区等待写入，等drain事件触发后，再继续执行写操作。（可以开始消费数据时，drain事件会被触发）

   writeStream.write(chunk)

```typescript
// 控制流速的案例
import fs from 'fs'
import { Writable } from 'stream'

const readStream = fs.createReadStream('./fs-stream.ts')
readStream.pause()
readStream.on('data', chunk => {
    console.log(chunk)
})

const writeStream = new Writable()
writeStream._write = (chunk, encoding, next) => {
    console.log(chunk)
    next()
}

readStream.on('data', chunk => {
    if (writeStream.write(chunk) === false) { // 写不下了
        readStream.pause(); // 暂停读取
    }
})

// 写入流可以继续写入
writeStream.on('drain', () => {
    readStream.resume(); // 开始继续读取
})

```



* 官方提供的 pipe 方法，已经做了背压问题优化。建议直接使用pipe去做流的传递。

### 编码方式

*可用编码：'ascii' | 'utf8' | 'utf-8' | 'utf16le' | 'ucs2' | 'ucs-2' | 'base64' | 'base64url' | 'latin1' | 'binary' | 'hex'*

* todo 区分

### pipe

它获取来源流，并将其通过管道传输到目标流。

```typescript
import http from 'http'
import fs from 'fs'

const server = http.createServer((req, res) => {

    res.setHeader('Access-Control-Allow-Origin', '*')
    res.setHeader('access-control-allow-methods', '*')

    const stream = fs.createReadStream('./file.txt')
    stream.pipe(res)
    // res.end('test')
})

server.listen(3000, () => {
    console.log('server running')
})
```



* 注意：该方法会返回目标流，非常方便对目标流进行操作

  ```typescript
  src.pipe(dest1).pipe(dest2)
  
  // 相当于
  src.pipe(dest1)
  dest1.pipe(dest2)
  ```



#### 其他流驱动的api



### hightWaterMark ——控制流速

https://zhuanlan.zhihu.com/p/398967492

* highWaterMark 用来控制每次读取的 buffer 长度

  **默认值:** `16384` （16 KiB），或者 `16` 用于 `objectMode` 流

  *// 设置了 highWaterMark，则每次读取到指定的长度都会触发 `data` 事件*

```typescript
import { Readable, Writable } from 'stream'
import fs from 'fs'

// 和 readable scream 创建参数相同
const readable = fs.createReadStream('./stream.ts', {
    // end: 100, // 控制读取文件大小
    highWaterMark: 100, // 控制每次 chunk 的大小，控制每次读取的长度，也就是流速
})
// console.log(readable.readableHighWaterMark)

readable.on('data', () => { // 设置了 highWaterMark，则每次读取到指定的长度都会触发该事件
    console.log('data')
})

const writable = new Writable({
    highWaterMark: 65536
})
writable._write = (chunk, encoding, next) => {
    console.log(chunk.toString())
    next()
}
```



### 与eventEmitter 关系

 所有的流都是 [EventEmitter](http://nodejs.cn/api/events.html#events_class_eventemitter) 的实例。因此stream 能够调用 eventEmitter 的方法



### stream 事件

`stream`对象可以监听`"data"`,`"end"`,`"opne"`,`"close"`,`"error"`等事件。`node.js`中监听自定义事件使用`.on`方法，例如`process.stdin.on(‘data’,…)`, `req.on(‘data’,…)`,通过这种方式，能很直观的监听到`stream`数据的传入和结束

* stream 能够通过 on 进行事件监听

```typescript
// 使用 pipe 传入时触发
writable.on('pipe', (src) => {
    // console.log(src, '---888')
})
// 写入完成时触发
writable.on('finish', () => {
    console.log('finish')
})
// 关闭时触发
writable.on('close', () => {
    console.log('close')
})
```





### 分类

流分为四类：

* readable: 可以通过管道读取、但是不能通过管道写入的流（可以接收数据，但是不能向其发送数据）

  当推送数据到可读流中时，会对其进行缓冲，直到使用者开始读取数据为止。例：fs.createReadStream

* `Writable`: 可以通过管道写入、但不能通过管道读取的流（可以发送数据，但不能从中接收数据）。

  fs.createWriteStream

* `Duplex`: 可以通过管道写入和读取的流，基本上相对于是可读流和可写流的组合。

* `Transform`: 类似于双工流、但其输出是其输入的转换的转换流。就是输入对应一个输出。

### duplex stream

`Duplex Stream` 双向的，既可读，又可写。 `Duplex streams`同时实现了 `Readable`和`Writable` 接口。 `Duplex streams`的例子包括

- `tcp sockets`
- `zlib streams`
- `crypto streams` 我在项目中还未使用过双工流，一些Duplex Stream的内容可以参考这篇文章[NodeJS Stream 双工流](https://link.juejin.cn/?target=https%3A%2F%2Fwww.cnblogs.com%2FdolphinX%2Fp%2F6376615.html)



### 可读流——readableStream

#### **readable.setEncoding(encoding)**

* 该方法设置从流中`readable.setEncoding()`读取的数据的字符编码。`Readable`

  也就是读出的数据是什么类型。默认值为null，读出的数据也是 buffer 类型，如果设置为 uft-8，则会直接读出字符串。不用再调用 tostring 方法

  ```typescript
  readable.setEncoding('utf-8');
  readable.on('readable', () => {
      // readable.read() 能够获取readable中的buffer
      console.log(readable.read(), 'readable')
  })
  ```

### 可写流-writeableStream

* 除了一些监听的事件——主要是因为实现了 EventEmitter(甚至可以自己触发)，一个重要的方法就是 write

#### writeable.write() 方法

* 能够动态往可写流中添加数据

==可读流重要的方法就是read，可写流重要的方法就是write==

#### read 方法

该`readable.read()`方法从内部缓冲区中读取数据并将其返回。如果没有数据可供读取，`null`则返回。默认情况下，`Buffer`除非使用该`readable.setEncoding()`方法指定了编码或流在对象模式下运行，否则数据将作为对象返回。

* 该`readable.read()`方法只能在`Readable`以暂停模式运行的流上调用。在流动模式下，`readable.read()`会自动调用，直到内部缓冲区完全耗尽。

#### 读取可读流中的数据

1. 使用 readable.read 方法从内部缓冲区中读取数据并将其返回。只能在 readable事件中调用

2. 使用 data 事件，读取

   ```typescript
   res.on('data', (data: Buffer) => {
     console.log(data, '---data');
     rawData += data;
   })
   res.on('readable', () => {
     console.log(res.read().toString(), '---read');
   })
   res.on('end', () => {
   	// 处理数据  
   })
   ```



#### 可读流可写流传递数据

1. 使用 pipe

2. 边读边写

   ```typescript
   readable.on('data', (chunk) => {
       console.log(chunk);
       // 可以边读边写
       writable.write(chunk)
   })
   ```

   

### stream 库

* Event-stream: https://github.com/dominictarr/event-stream



## node net

https://juejin.cn/post/6844903453714546702

* 可以简单的把服务端看成 Server, 客户端看作 Socket。都是从 net 模块中引入

### server 和 socket

* 创建两端的方法都有两种

  Server: Server, createServer

  Socket: Socket, createConnection

Client

```typescript
const socket = createConnection({
    port: 8093,
    host: '127.0.0.1',
    localAddress: '127.0.0.1',
    localPort: 8094,
})
```

### 属性

* 客户端

  远端地址和本机地址

  ```typescript
  socket.on('connect', () => {
      console.log('----地址相关-----');
      console.log(socket.address());
      // 地址（对象），ip版本，端口
      console.log(socket.remoteAddress, socket.remoteFamily, socket.remotePort);
      // 本身的地址和端口。连接本身是需要ip和端口的，所以客户端也有一个地址
      console.log(socket.localAddress, socket.localPort);
  })
  ```

### 方法

* close 主动关闭连接

* *destroy* 销毁，发生错误的时候销毁这个socket

  服务端不能主动销毁，只能关闭

### server 事件

- listening：调用 server.listen()，正式开始监听请求的时候触发。
- connection：当有新的请求进来时触发，参数为请求相关的 socket。
- close：服务端关闭的时候触发。
- error：服务出错的时候触发，比如监听了已经被占用的端口。

```typescript
// 事件相关
server.on('listening', () => {
    console.log('server listening exec');
})
// 建立连接执行
server.on('connection', () => {
    console.log('server connection exec');
})
server.on('close', () => {
    console.log('server close exec');
})
server.on('error', () => {
    console.log('server error exec');
})
```



### client 事件

data：当收到另一侧传来的数据时触发。

connect：当连接建立时触发。

close：连接断开时触发。如果是因为传输错误导致的连接断开，则参数为error。

end：当连接另一侧发送了 FIN 包的时候触发（读者可以回顾下HTTP如何断开连接的）。默认情况下（allowHalfOpen == false），socket会完成自我销毁操作。但你也可以把 allowHalfOpen 设置为 true，这样就可以继续往socket里写数据。当然，最后你需要手动调用 socket.end()

error：当有错误发生时，就会触发，参数为error。（官方文档基本一句话带过，不过考虑到出错的可能太多，也可以理解）

timeout：提示用户，socket 已经超时，需要手动关闭连接。

drain：当写缓存空了的时候触发。（不是很好描述，具体可以看下stream的介绍）

lookup：域名解析完成时触发。

### unref 方法

* 从事件循环中删除



### Node.js net 搭建多路复用的 RPC 通道

### node net 模块

* http://nodejs.cn/api/net.html

```js
const net = require('net');
const server = net.createServer((socket) => {
 	// socket 网络通道代理对象，能够读写
  socket.on('data', (buffer) => {
    
  })
});
server.on('error', (err) => {
  throw err;
});
server.listen(8124, () => {
  console.log('server bound');
});
```

* Socket 相当于通道，能够读取和写入数据
* socket 传递的数据都是 buffer，需要使用 buffer 的方法进行处理
* 双方（客户端和服务端）都是通过 socket.write 的方式去往通道中写数据；通过监听 data 事件的方式从管道中拿数据。



### 全双工通信实现

* 全双工通信需要在半双工通信的基础上，把数据包 的 id 带上（包序号）
* 数据的头部携带 id（seqid （Sequence number）序列号）

### http

* 没有发现 http2 就是 tcp 的全双工通信，http1 就是半双工通信

#### 两个 api

* Buffer.slice 
* Buffer.concat
  * 都是 Buffer 直接调用，和数组的区别

### 粘包问题

* tcp 优化 粘包问题（准确的说 TCP不存在粘包问题，只是认为的给数据流分了包）



### tcp 问题汇总

* 三次握手和四次挥手
* lookup

### 从 tcp 看http协议

* 从tcp协议看http是怎么样封装的

```typescript
const tcp = new Server((socket) => {
    socket.on('data', chunk => {
        console.log(chunk.toString(), '---tcp数据');
        /**
         * 从这里就可以看到http是怎么样封装tcp数据的
         * 比如一个post请求:
         * 1. header之间是换行的
         * 2. body是空一行出现的
         * POST /?test1=544 HTTP/1.1
         *   Content-Type: application/json
         *   User-Agent: PostmanRuntime/7.29.2
            Accept: *
            Postman-Token: c1c68e6b-56fc-4cb1-b1ce-2f0a80e035d2
            Host: localhost:8097
            Accept-Encoding: gzip, deflate, br
            Connection: keep-alive
            Content-Length: 21

            {
                "test": "222"
            }
         */
    })
})
```





## node:dgram

https://nodejs.org/api/dgram.html

* node创建UDP连接



## RPC 关键词

* 二进制 编码解码 数据包



## Stream

* 思考一下 node 程序的IO。

  本质还是按需加载，没必要一次性加载成功

  https://juejin.cn/post/6844903891083984910

  * 输入：
    1. 控制台
    2. 读取文件
    3. http 

```js
stream.pipe(res)
// 将 res 作为 stream 的 dest。res 理论上是一个 writeAble 的 stream
```



## events 模块

`events` 模块为提供了 EventEmitter 类，这是在 Node.js 中处理事件的关键。

* todo 和 tapable 的区别
* Eventemitter 是一个纯发布订阅系统；tapable 除了是一个发布订阅系统，还需要承担插件的功能，所以需要提供回执机制，也就是插件触发之后处理完成需要反向响应（tapable 主事件需要收到插件完成的信号）
* http://nodejs.cn/learn/the-nodejs-events-module

```typescript
// node event 模块
import EventEmitter from "events";
import colors from 'colors'

const customEvent = new EventEmitter()

// 订阅事件
// customEvent.addListener('test', (arg) => {
//     console.log('test run', colors.red(arg))
// })
// 返回的事件就是 customEvent
const newListener = customEvent.on('test', (arg) => {
    console.log('test run', colors.red(arg))
})
// customEvent.on('test2', (arg) => {
//     console.log('test2 run', colors.red(arg))
// })
// customEvent.once('test2', (arg) => {
//     console.log('test2 run', colors.red(arg))
// })
const fn = (arg: any) => {
    console.log('test3 run', colors.red(arg))
}
customEvent.once('test3', fn)

customEvent.once('test2', (arg) => {
    console.log('test2 run', colors.red(arg))
})

// console.log(newListener === customEvent)

// 解除绑定
// customEvent.off('test', customEvent.listeners('test')[0] as any)

// 放到指定的事件前面调用。也就是调整事件队列的顺序。
// customEvent.prependListener('test', () => {
//     console.log('new test')
// })

customEvent.prependOnceListener('test3', () => {
    console.log('test3 new')
})

// 解除所有的监听
customEvent.removeAllListeners('test3')

// customEvent.emit('test', 'arg2')
// 也可以用绑定返回的事件进行触发
// newListener.emit('test', 'arg2')
newListener.emit('test', 'arg2')
newListener.emit('test2', 'arg2')
newListener.emit('test3', 'arg2')

// customEvent.eventNames() 返回所有注册的事件名称
// console.log(colors.green(customEvent.eventNames() as any), customEvent.getMaxListeners())

// 获取指定名称事件的个数
// console.log(customEvent.listenerCount('test'))

// 获取事件绑定函数
// console.log(customEvent.listeners('test')[0](222))


```

* node中很多模块都继承了该对象，能够实现事件的订阅。和浏览器中的addEvnetListenr有点像，给任何东西都能绑定对象。



## os 操作系统模块

* 如果要获取win和mac系统的一些区别可以使用这个模块。

  比如： mac 换行为 \n，win 换行为 \r\n

```typescript
import os from 'os'

// os.EOL 可给出行定界符序列。 在 Linux 和 macOS 上为 \n，在 Windows 上为 \r\n。
// console.log(JSON.stringify(os.EOL), `\\${os.EOL}`)

// os.constants.signals 可告知所有与处理过程信号相关的常量，例如 SIGHUP、SIGKILL 等。
// os.constants.errno 可设置用于错误报告的常量，例如 EADDRINUSE、EOVERFLOW 等。
// console.log(os.constants.signals)
// 返回标识底层架构的字符串，例如 arm、x64、arm64。
console.log(os.arch());

// 返回关于系统上可用的 CPU 的信息。
// console.log(os.cpus()[0].model);

// 存储序
// console.log(os.endianness())

// 系统可用内存字节数
// console.log(os.freemem());

// 返回家路径
// console.log(os.homedir());

// 返回主机名
// console.log(os.hostname());

// 平均负载
// console.log(os.loadavg());

// 网络接口
// console.log(os.networkInterfaces());

// node 编译平台
// console.log(os.platform());

// 操作系统发行版本
// console.log(os.release());

// 临时文件夹路径
// console.log(os.tmpdir());

// 总内存
// console.log(os.totalmem());

// console.log(os.type());

// 上次启动到现在秒数
// console.log(os.uptime());

console.log(os.userInfo());


```



## node 多进程

* 为啥 node 能开启多进程，而浏览器是多线程呢？

  todo利用 进程 id 进行测试！



## node 错误处理

* 没有浏览器中的 unhandledrejection 错误处理方式

* throw new Error 或者 Promise.reject 都会导致程序退出

* todo 对比浏览器中的错误处理

  caught 捕获，exception 例外，异常 crash 崩溃

```typescript
// node 中不推荐直接跑出一个值的方式。这个不会抛出错误
// throw '111'
try {
    throw '222'
} catch (err) {
    console.log(err, '--111');
}

// process.exit(0)

// 捕获未捕获的异常，包括promise错误也能捕获到
process.on('uncaughtException', err => {
    console.error('一个未捕获的错误', err)
    process.exit(1) // 必须执行这个语句
})
// 捕获未处理的 promise 错误
process.on('unhandledRejection', err => {
    console.error('未捕获的promise错误', err)
})


// throw new Error('出错了')
Promise.reject(22)
```



### 重要！

https://juejin.cn/post/6844903614784225287

当异常一直没有被捕获处理的话，最后就会触发'uncaughtException'事件。默认情况下，Node.js会打印堆栈信息到stderr然后退出进程。不要试图阻止uncaughtException退出进程，因此此时程序的状态可能已经不稳定了，建议的方式是及时捕获处理代码中的错误，uncaughtException里面只做一些清理工作。

### node 程序退出

手动调用process.exit()或者触发uncaptException会导致进程退

### node 异常处理好文

https://www.infoq.cn/article/quit-scheme-of-node-uncaughtexception-emergence

> 一个友好的错误处理机制应该满足三个条件:
>
> 1. 对于引发异常的用户，返回 500 页面
> 2. 其他用户不受影响，可以正常访问
> 3. 不影响整个进程的正常运行
>
> 很遗憾的是，保证 `uncaughtException` 不影响整个进程的健康运转是不可能的。当 Node 抛出 `uncaughtException` 异常时就会丢失当前环境的堆栈，导致 Node 不能正常进行内存回收。也就是说，每一次 `uncaughtException` 都有可能导致内存泄露。





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





# express

## 简介

基于 [Node.js](https://nodejs.org/en/) 平台，快速、开放、极简的 Web 开发框架

* Web 应用程序

​	Express 是一个保持最小规模的灵活的 Node.js Web 应用程序开发框架，为 Web 和移动应用程序提供一组强大的功能。

* api 使用您所选择的各种 HTTP 实用工具和中间件，快速方便地创建强大的 API。 主要是http相关api

Express 框架核心特性：

- 可以设置中间件来响应 HTTP 请求。
- 定义了路由表用于执行不同的 HTTP 请求动作。
- 可以通过向模板传递参数来动态渲染 HTML 页面。

## 特性-官方

- 稳健的路由
- 专注于高性能
- 超高测试覆盖率
- HTTP 助手（重定向、缓存等）
- 查看系统支持 14+ 模板引擎
- 内容协商
- 可执行以快速生成应用程序

## 基础

* 安装

  npm i express

* 建议安装

  - **body-parser** - node.js 中间件，用于处理 JSON, Raw, Text 和 URL 编码的数据。
  - **cookie-parser** - 这就是一个解析Cookie的工具。通过req.cookies可以取到传过来的cookie，并把它们转成对象。
  - **multer** - node.js 中间件，用于处理 enctype="multipart/form-data"（设置表单的MIME编码）的表单数据。

## api

### app.get

使用指定的回调函数将 HTTP GET 请求路由到指定路径。

第二个参数：

- 一个中间件功能。
- 一系列中间件功能（以逗号分隔）。
- 一组中间件函数。
- 以上所有的组合。

也就是可以可以无限传递中间件

比如：

```typescript
app.post('/test-post', parseJson(), (req, res) => {
    console.log(req.body, '---body');

    res.send('hello')
})
```



### app.post

使用指定的回调函数将 HTTP GET 请求路由到指定路径。



### app.set

* 设置 http 响应头。可以传入对象

### app.type

* 可以快速设置 http content-type header。如果包含 `/` 符号，则采用传入的字符串，如果不包含则采用相应的 type

```typescript
res.type('.html')
// => 'text/html'
res.type('html')
// => 'text/html'
res.type('json')
// => 'application/json'
res.type('application/json')
// => 'application/json'
res.type('png')
// => 'image/png'
```



### http header 设置

* content-type: 第一个是文件类型，第二个是编码。用 分号 分割，末尾不能再加分号！！！



## 结束请求



| Method                                                       | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [res.download()](https://www.expressjs.com.cn/en/4x/api.html#res.download) | Prompt a file to be downloaded.                              |
| [res.end()](https://www.expressjs.com.cn/en/4x/api.html#res.end) | End the response process.                                    |
| [res.json()](https://www.expressjs.com.cn/en/4x/api.html#res.json) | Send a JSON response.                                        |
| [res.jsonp()](https://www.expressjs.com.cn/en/4x/api.html#res.jsonp) | Send a JSON response with JSONP support.                     |
| [res.redirect()](https://www.expressjs.com.cn/en/4x/api.html#res.redirect) | Redirect a request.                                          |
| [res.render()](https://www.expressjs.com.cn/en/4x/api.html#res.render) | Render a view template.                                      |
| [res.send()](https://www.expressjs.com.cn/en/4x/api.html#res.send) | Send a response of various types.                            |
| [res.sendFile()](https://www.expressjs.com.cn/en/4x/api.html#res.sendFile) | Send a file as an octet stream.                              |
| [res.sendStatus()](https://www.expressjs.com.cn/en/4x/api.html#res.sendStatus) | Set the response status code and send its string representation as the response body. |



## 快速生成 express 项目

* 主要参考目录结构和划分

  https://www.expressjs.com.cn/starter/generator.html

## req 对象

* 能够获取 ip hostname 等客户端信息。具体查看官网api

https://www.expressjs.com.cn/en/4x/api.html#req.fresh

## 中间件

https://www.expressjs.com.cn/guide/writing-middleware.html

定义：中间件是一个函数，在应用的请求响应周期内能够访问 req、res对象和next函数。

​	该`next`函数是 Express 路由器中的一个函数，当被调用时，它会在当前中间件之后执行中间件。==next 相当于下一个中间件的引用==

中间件函数可以执行以下任务：

- 执行任何代码。
- 更改请求和响应对象。
- 结束请求-响应周期。提前结束响应
- 调用堆栈中的下一个中间件。

* 如果当前中间件函数没有结束请求-响应循环，它必须调用`next()`以将控制权传递给下一个中间件函数。否则，请求将被挂起。

> 从 Express 5 开始，如果返回 promise reject 或者跑出错误就会调用 next，值为 rejected value 或者 throw Error



### 分类

Express 应用程序可以使用以下类型的中间件：

- [应用级中间件](https://www.expressjs.com.cn/guide/using-middleware.html#middleware.application)
- [路由器级中间件](https://www.expressjs.com.cn/guide/using-middleware.html#middleware.router)
- [错误处理中间件](https://www.expressjs.com.cn/guide/using-middleware.html#middleware.error-handling)
- [内置中间件](https://www.expressjs.com.cn/guide/using-middleware.html#middleware.built-in)
- [第三方中间件](https://www.expressjs.com.cn/guide/using-middleware.html#middleware.third-party)

todo 啥意思？

您可以使用可选的挂载路径加载应用程序级和路由器级中间件。您还可以将一系列中间件函数一起加载，从而在挂载点创建中间件系统的子堆栈。

### 使用中间件

Express 是一个路由和中间件 Web 框架，其自身功能最少：Express 应用程序本质上是一系列中间件函数调用。

* 本质上是中间件的集合！

三种使用方式
 * 1. 直接添加，全局使用。所有的路由都会应用此中间件。app.use

 * 2. 在指定路由中添加。只作用到该路由

   ```typescript
   app.get('/test1/', test1Mw, (req, res) => {
       console.log(333);
       res.send('hello')
   })
   ```

 *      3. 指定路由添加。只有指定的路由生效
        
        ```typescript
        app.use('/test1', mw)
    ```

​	==注意：第三种方式use加路由的情况，如果路由是根目录，则可以匹配任何路由==

> 路由将匹配紧随其路径的任何路径，并带有“ `/`”。例如：`app.use('/apple', ...)`将匹配“/apple”、“/apple/images”、“/apple/images/news”等

#### 多个相同路径中间件使用

* 如果方法、路径相同的中间件怎么样让后一个中间件执行呢

  可以使用 next('route') 执行下一个中间件

  ```typescript
  app.get('/test1', (req, res, next) => {
      console.log('第一个test1');
      next('route')
  })
  
  app.get('/test1/', test1Mw, (req, res) => {
      console.log(333);
      res.send('hello')
  })
  ```

### 内置的中间件

查询列表：https://github.com/senchalabs/connect#middleware



### 第三方中间件

查询列表：https://www.expressjs.com.cn/resources/middleware.html

### 中间件应用——第三方中间件

#### http-proxy-middleware

* 一个本地代理的工具，参考：https://juejin.cn/post/6844903698632540173

代码参考：http-proxy-middleware -> basic

#### express-session

https://juejin.cn/post/6844903813703286797

* 创建session的中间件。低版本需要配合 cookie-parse 一起工作

注意：

* 从15版本开始不再依赖 cookie-parse。如果cookie-parse 和改module配置的 secret 不相同可能导致错误
* 默认情况适用 内存存储，这个不能在生产环境使用，可能导致内存泄露，而且无法在每个进程中共享（一般使用redis）
* 会自动设置cookie，无需开发人员再操作。==保存的是sessionid!!!==

配置项

* cookie session id 保存的cookie设置



## 路由

> `router`对象是中间件和路由的隔离实例。您可以将其视为“迷你应用程序”，仅能够执行中间件和路由功能。每个 Express 应用程序都有一个内置的应用程序路由器。

app 应用应该是实现了 router，router的方法都能使用

```typescript
var express = require('express')
var app = express()
var router = express.Router()

```



### 使用

* 默认完全匹配

* 支持正则匹配
* 可以使用中间件指定
* static

> Express 使用[path-to-regexp](https://www.npmjs.com/package/path-to-regexp)来匹配路由路径；有关定义路由路径的所有可能性，请参阅 path-to-regexp 文档。[Express Route Tester](http://forbeslindesay.github.io/express-route-tester/)是一个用于测试基本 Express 路由的便捷工具，尽管它不支持模式匹配。

* 查询字符串（query strings） 不是路由的一部分

#### 匹配

https://www.expressjs.com.cn/guide/routing.html

1. 完全路径

2. 字符串模型

   主要是 ? * + 在字符串中的使用，建议参考官网

3. 正则

### http method 对应路由

https://www.expressjs.com.cn/en/4x/api.html#app.METHOD

* method 类型即是请求路由方法名称

* 也可以不限制method，让中间件去处理

  ```typescript
  // 所有http method请求
  app.all('/all', (req, res) => {
      console.log(req.method, 'http method');
      res.send('hello method')
  })
  ```

  



### 定制 404 页面

```typescript
// 404 页面定制
// 只要把这个放到最后即可。并且可以不指定路由
app.use((req, res) => {
    res.status(404).send('not found')
})
```

* 所有不指定路由的方式，只需要放到所有的中间件最后即可。不用指定路由
* 不知道http method 的中间件，直接 app.use 指定路由即可



### res.sendStatus 快速返回http响应码

* 可以快速返回http响应码，不需要额外定制响应内容。express 已经封装好了。



### app.route

> 您可以使用`app.route()`. 因为路径是在单个位置指定的，所以创建模块化路由是有帮助的，因为这有助于减少冗余和拼写错误。

* 也就是可以指定一个路径下不同 method 的处理方式。
* 对 restful api 是有用的

==注意和app.all的区别。是属于app.all的进一步实现==



## 覆盖 express api

* 参考：https://www.expressjs.com.cn/guide/overriding-express-api.html

  感觉没啥必要



## 缓存配置

* todo!



### 洋葱模型

https://juejin.cn/post/6957258059022499854

* express 中间件也保持洋葱模型，但是不严格
* **在异步执行中间件时，Express 并非严格按照洋葱模型执行中间件，而 KOA 则是严格遵循的（体现再两者在中间件为异步函数的时候处理会有不同）。**



### static

**express.static**是Express中唯一的内建中间件

https://juejin.cn/post/7029681403595849765

* express.static()

  用法：可以快速的托管静态文件。不需要添加路由，直接能访问。

```typescript
app.use(express.static(path.join(__dirname, 'dist')));
// 直接访问:  http://localhost:3000/index.html

// 也可以添加路由
app.use('/static',express.static('public'))
// 访问： http://localhost:3000/static/index.html，加了 static 路由

// 还可以添加配置，参考官网
app.use('/static', express.static('static', {
    dotfiles: 'ignore', // 忽略以 . 开头的文件
}))
```



### 路由参数

1. querystring 类型。在req.query 中能够拿到
2. :id 的方式需要在 req.params 中拿到

```typescript
// 路由参数
app.get('/user:id', (req, res) => {
    console.log(req.params, req.query, '---req.params');
    res.send('params')
})
```

* 常用的路由参数做路径

```typescript
// 路由参数
app.get('/user/:id/test', (req, res) => {
    console.log(req.params, req.query, '---req.params');
    res.send('params')
})
```



### router.param —— 处理路由参数

需要注意，在express中路由所有的方法都在 app 中实现了

* router.param 方法能够处理路由参数，只要匹配到该参数就会执行回调

```typescript
app.get('/user/:id/test', (req, res) => {
    console.log(req.params, req.query, '---req.params');
    // express.static.mime.lookup()
    res.type('json')
    res.vary('User-Agent')
    res.send('params')
})
app.param('id', (req, res, next, id) => {
    console.log(id, '---param');
    next()
})
```



## express 错误处理

https://www.expressjs.com.cn/guide/error-handling.html

express 推荐自己处理错误，把错误信息发送给错误处理中间件。如果不想这样做，也可以在路由中自己捕获，然后处理

> 从 Express 5 开始，返回 Promise 的路由处理程序和中间件将`next(value)`在拒绝或抛出错误时自动调用。例如：

* 错误处理中间件必须是四个参数

  ```typescript
  const fn: ErrorRequestHandler = (err, req, res, next) => {
      console.log(err, '---err');
      res.status(500).send('Something broke!')
  }
  
  app.use(fn)
  ```

==express 没有一个很好的处理错误的方式，必须依靠开发人员进行维护，获取主动传给错误处理中间件！==



## express 存在问题

### 1. 不知道怎么处理 PromiseRejection

需要手动调用 next(err) 将它传递给错误处理中间件

* express 对于async 函数不做处理

  如果你在路由处理器或中间件里用到了 `async/await` 代码，就容易碰到 `UnhandledPromiseRejectionWarning` 异常。Express 不知道它们，所以不会为你处理这些异常。

```typescript
app.get("/health", async (req, res) => {
  throw new Error("fake error");
});
```

* 例子：https://juejin.cn/post/6935701501008412708

怎么做：

在写服务端接口的时候一定要处理异常！接口层面。

* 拓展：node 15 unHandlePromiseRejection 处理，https://zhuanlan.zhihu.com/p/267720330

## express 反向代理

反向代理位于 Web 应用程序前面，除了将请求定向到应用程序之外，还对请求执行支持操作。它可以处理错误页面、压缩、缓存、服务文件和负载平衡等。

* 建议使用 Nginx

### http-proxy-middleware

配置功能说明



### X-Forwarded- 请求头

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Forwarded

客户端在请求反向代理服务器的时候，有可能被反向代理服务器修改原始的请求头，X-Forwarded- 说明了原始请求客户端的请求头

常见：

* X-Forwarded-Host
* X-Forwarded-Proto
* X-Forwarded-For

### trust proxy

https://expressjs.com/zh-cn/guide/behind-proxies.html

设置非 `false` `trust proxy` 值会导致三个重大变化：

- [req.hostname](https://expressjs.com/zh-cn/api.html#req.hostname) 的值派生自 `X-Forwarded-Host` 头中设置的值（可以由客户机或代理设置此值）。
- `X-Forwarded-Proto` 可以由逆向代理设置，以告知应用程序：它是 `https` 还是 `http`，或者甚至是无效名称。该值由 [req.protocol](https://expressjs.com/zh-cn/api.html#req.protocol) 反映。
- [req.ip](https://expressjs.com/zh-cn/api.html#req.ip) 和 [req.ips](https://expressjs.com/zh-cn/api.html#req.ips) 值由 `X-Forwarded-For` 的地址列表填充。



# koa

## 与 express 区别

https://juejin.cn/post/6957258059022499854

Express 封装、内置了很多中间件，比如 connect 和 router ，而 KOA 则比较轻量，开发者可以根据自身需求定制框架；

Express 是基于 callback 来处理中间件的，而 KOA 则是基于 await/async；

**在异步执行中间件时，Express 并非严格按照洋葱模型执行中间件，而 KOA 则是严格遵循的（体现再两者在中间件为异步函数的时候处理会有不同）。**

* express 的异步中间件会延后执行

* 轻量、异步方式、中间件机制

# 其他框架

2021年，如果选型一个Node.js的web server框架，你会选择什么？ - i5ting的回答 - 知乎 https://www.zhihu.com/question/446613186/answer/1755055118



# 后端开发相关

## 后端框架喜欢使用装饰器做 IOC

todo

https://www.zhihu.com/question/323525252/answer/937101214

> 为什么很多[后端框架](https://www.zhihu.com/search?q=后端框架&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"937101214"})喜欢使用装饰器来搞 IOC，我个人觉得这样搞确实好处多多。采用集中声明式的书写依赖明显比手动编写 controller 逻辑代码更不容易产生错误。另外，由于装饰器是框架的一部分，所以框架本身管理项目的依赖就变得更容易和全面。再则装饰器设计的好的确实可以让代码变得非常精简，提高编码的愉悦性。



# 部署node服务相关

## pm2

Todo: 掘金

## docker compose

todo

# 数组 reduce 

* 如果没有初始值，则会从第一项 第二项开始执行



# mjs 和 commonjs

## node.js 如何处理es6模块

* js 两种模式：

  一种是 ES6 模块，简称 ESM；另一种是 Node.js 专用的 CommonJS 模块，简称 CJS。这两种模块不兼容。

语法上面，CommonJS 模块使用`require()`加载和`module.exports`输出，ES6 模块使用`import`和`export`。

用法上面，`require()`是同步加载，后面的代码必须等待这个命令执行完，才会执行。`import`命令则是异步加载，或者更准确地说，ES6 模块有一个独立的静态解析阶段，依赖关系的分析是在那个阶段完成的，最底层的模块第一个执行。

### ==es module 模块特点==

* 可以在顶层使用 await 

  Commonjs 必须在 async 函数中使用 await

总是说 esm 在静态语法分析的时候有优势？到底是怎么一回事呢？

* commonjs 的模块引入，不要求顶层，可以放在执行条件中；引入的模块名称也可以是表达式；

  这样在做 ast 分析的时候就不能准确分析出哪些接口或者模块被引入了，哪些没有被引入

* Esm 引入的时候，只能在顶层；不能再判断条件中；引入模块必须是字符串；

说白了就是，能在编译时做的事情，最好别侵入到运行时。在做 ast 分析时，并没有运行环境的上下文。

> * 只能作为模块顶层的语句出现
>
> * import 的模块名只能是字符串常量。应用的是一个引用
>
>   commonjs 模块名称可以是表达式。。
>
> * 不管 `import` 的语句出现的位置在哪里，在模块初始化的时候所有的 `import` 都必须已经导入完成。
>
> * import binding 是 immutable 的
>
>   就是导入的模块名不能再赋值给其他值，防止 tree shaking 出错，保证这个值的唯一性。commonjs 就可以使用 let a = require('a') 这样修改这个值。

### node中的两种模块

* node 中 .mjs 后缀名表示使用 es6 模块

  > Node.js 遇到`.mjs`文件，就认为它是 ES6 模块，默认启用严格模式，不必在每个模块文件顶部指定`"use strict"`。

  如果不希望改变后缀可以设置 package.json 中的type为 module

1. 后缀名

   mjs 和 cjs

2. package.json 中的 type 字段

   默认是 commonjs

   设置为 module 则使用 es module 解析

### 在 commonjs 中加载 es6 模块

CommonJS 的`require()`命令不能加载 ES6 模块，会报错，只能使用`import()`这个方法加载。

> ```javascript
> (async () => {
>   await import('./my-app.mjs');
> })();
> ```

上面代码可以在 CommonJS 模块中运行。

`require()`不支持 ES6 模块的一个原因是，它是同步加载，而 ES6 模块内部可以使用顶层`await`命令，导致无法被同步加载。

* 使用 async 函数，执行一个动态 import 方法

### esm 中加载 commonjs 模块

* es module 中可以直接在家 commonjs，但是只能整体加载，不能只加载单一的一项

```javascript
// 正确
import packageMain from 'commonjs-package';

// 报错
import { method } from 'commonjs-package';
```

* commonjs 是挂载到一个对象上的。

  > 这是因为 ES6 模块需要支持静态代码分析，而 CommonJS 模块的输出接口是`module.exports`，是一个对象，无法被静态分析，所以只能整体加载。

加载单一的输出项，可以写成下面这样。

> ```javascript
> import packageMain from 'commonjs-package';
> const { method } = packageMain;
> ```



### 同时支持两种模式

* esm 支持 commonjs

  一般需要写一个转换层，根据具体的需要输出具名接口

  ```js
  import common from './common.js'
  
  export const fn = common.fn
  ```

  注意：还需要把后缀改成 .mjs 或者改type 才能顺利执行

* Commonjs 支持 esm

  需要使用动态 import 进行加载，esm最好导出一个对象的方式。比如 export default obj

还有一种方式是修改 package.json 文件中的exports字段，指明两种格式模块各自的加载入口

```js
{
  "exports"：{ 
    "require": "./index.js"，
    "import": "./esm/wrapper.js" 
	}
}
```





### 严格模式



## 命令行直接运行 esmodule 的代码

* 把文件名改成 mjs 后缀，即可直接运行。



## 命令行引入json文件设置

* 如果node不能引入 json 文件可以加执行参数

  ```bash
  node --experimental-json-modules generate-file.mjs
  ```



# node 程序调试

vscode 官方：https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_additional-configuration

参考：https://juejin.cn/post/6942344638892081166

https://juejin.cn/post/6992976045213220878#heading-7

有空可以看看大佬的教学：

https://medium.com/@paul_irish/debugging-node-js-nightlies-with-chrome-devtools-7c4a1b95ae27

常用有四种方式

## 1. vscode-javascript debug terminal

* 打开vscode的 javascript debug terminal（debug专用命令行）

  用 vscode 打断点，左侧的断点即可

  然后切到该目录node运行这个程序

* 新建 javascript debug terminal 方式很多，新增终端那个下拉也能创建



## 2. Vscode-auto attach

* 需要再 vscode 中使用 command + shift + p，搜索 auto attach 进行设置

  - `smart`（默认）- 如果您在`node_modules`文件夹之外执行脚本或使用常见的“运行器”脚本（如 mocha 或 ts-node），则会调试该过程。**您可以使用自动附加智能模式**设置 ( )配置“运行器”脚本允许列表`debug.javascript.autoAttachSmartPattern`。
  - `always`- 将调试集成终端中启动的所有 Node.js 进程。
  - `onlyWithFlag`- 只有使用`--inspect`or`--inspect-brk`标志启动的进程才会被调试。

  一般使用 smart 方式，这样打了断点重新启动程序，就能进行调试了。
  
  一定要重启（或者右侧的图标）终端！

## 3. vscode-launch config，其他程序调试

* 配置完成可以直接在 vscode 的调试界面运行
* 调试配置存储在`launch.json`工作区`.vscode`文件夹中的一个文件中（就是其中的一个临时文件，不用提交！）。调试配置文件的创建和使用介绍在通用[调试](https://code.visualstudio.com/docs/editor/debugging#_launch-configurations)文章中。



## 4. ndb

* 建议全局安装：

  ```bash
  npm i ndb -g
  ```

* debug 命令，前面加一个ndb即可

  ```bash
  ndb npx ts-node ./node-inspect.ts
  ```

  

## 5. 使用 node inspect

* 推荐阅读：http://www.ruanyifeng.com/blog/2018/03/node-debugger.html

```bash
node --inspect ./index.js
```

* 不能调试 ts 文件

那么，`--inspect` 和 `--inspect-brk` 有什么不同？

> `--inspect-brk` 会在代码执行前停住。

- `--inspect` 启动调试服务，跑完程序就退出。除非是有异步任务在，不然完全不给调试器对接的机会。
- `--inspect-brk` 启动完调试服务就停在开头，等着调试器接入，接入后断在第一行代码等待下一步操作。

推荐使用 inspect-brk

* 注意：这种方式并不能很好的调试 ts 代码！

## 调试进行中的node进程

* 查看 process  的信号！
* 注意：这种方式并不能很好的调试 ts 代码！

## 工程代码调试

vscode中 command hover 到package.json中的script上，就能看到run script和debug script两个快捷路径！





# node 命令行程序开发——shell脚本

* 参考：https://www.ruanyifeng.com/blog/2015/05/command-line-with-node.html
* https://segmentfault.com/a/1190000016555129

## 1. 可执行脚本

* 首先需要设置文件可执行

  ```bash
  chmod u+x ./shell
  ```

* 除了添加环境变量，另一种把文件变成命令的方式

  * 在当前目录下新建 package.json

    ```js
    {
      "name": "test",
      "bin": {
        "test": "./test.js"
      }
    }
    ```

### bin字段

* npm 会把配置加入环境变量

1. 本地启动命令

   package.json 设置

   ```json
   {
       "name": "shell-with-js",
       "bin": {
           "shell_js": "./index.mjs"
       }
   }
   ```

   index.mjs 

   ```js
   #!/usr/bin/env node
   import inquirer from "inquirer";
   
   inquirer
       .prompt([
           {
               type: 'confirm',
               name: 'test',
               default: 'hhh',
               message: '测试测试',
           }
       ])
   
   ```

* 注意：主要 npm link 注册一下

  不推荐使用ts直接运行脚本，建议用 ts 写，然后用 tsc 编译。

## 2.命令行参数的原始写法

```js
#!/usr/bin/env node
// process.argv 返回环境变量数组 + 参数数组
// console.log('test', process.argv)
```

### Shebang

* 开头的注释

  看看：https://www.zhihu.com/question/333282017?sort=created

* 相当于 /usr/bin/env node index.js

## 3. 新建进程

```javascript
#!/usr/bin/env node
var name = process.argv[2];
var exec = require('child_process').exec;

var child = exec('echo hello ' + name, function(err, stdout, stderr) {
  if (err) throw err;
  console.log(stdout);
});
```



## 4. 使用 shelljs 写 shell 脚本

* 本地模式

  ```js
  #!/usr/bin/env node
  var name = process.argv[2];
  var shell = require("shelljs");
  
  shell.exec("echo hello " + name);
  ```

* 全局模式，直接写 shell 命令

  ```js
  
  require('shelljs/global');
  
  if (!which('git')) {
    echo('Sorry, this script requires git');
    exit(1);
  }
  
  mkdir('-p', 'out/Release');
  cp('-R', 'stuff/*', 'out/Release');
  
  cd('lib');
  ls('*.js').forEach(function(file) {
    sed('-i', 'BUILD_VERSION', 'v0.1.2', file);
    sed('-i', /.*REMOVE_THIS_LINE.*\n/, '', file);
    sed('-i', /.*REPLACE_LINE_WITH_MACRO.*\n/, cat('macro.js'), file);
  });
  cd('..');
  
  if (exec('git commit -am "Auto-commit"').code !== 0) {
    echo('Error: Git commit failed');
    exit(1);
  }
  ```

  

## 5. 处理参数——yargs模块

* 安装：

  ```bash
  npm install --save yargs
  ```

* 传递参数两种方式

  1. --name=arg
  2. --name arg

```js
#!/usr/bin/env node
/**
 * 添加帮助信息：
 *  usage：用法格式
    example：提供例子
    help：显示帮助信息
    epilog：出现在帮助信息的结尾
 */
const argv = require('yargs')
    .option('n', {
        /**
         * demand：是否必选
            default：默认值
            describe：提示
         */
        alias: 'name',
        demand: true,
        default: 'tom',
        describe: 'your name',
        type: 'string'
    })
    .usage('Usage: hhh [options]')
    .help('h')
    .alias('h', 'help')
    .example('hhh -n jj', 'test eg')
    .epilog('copyright 2015')
    .argv

console.log(argv)

```

* yargs 能识别 -n 或者 --n 的参数

### 参数和环境变量

==注意环境是写到命令的最前面！参数是写到最后面！==

## 添加子命令——一个命令的子命令

```typescript
#!/usr/bin/env node

import yargs from 'yargs'

require('shelljs/global')
const argv = yargs
    .command('test1', 'help info', (yargs) => {
        // console.log(yargs)
        // echo(yargs)
        console.log(yargs)
        // 子任务添加参数
        const innerArgv = yargs
            .option('m', {
                alias: 'message',
                description: 'message info',
            })
            .parseSync()
        // echo(innerArgv.message)
        console.log(innerArgv.message);
    })
    .parseSync()

console.log(argv.name)

```

* 要用 npx ts-node 执行，使用ts编写的

## 其他约束

### 1、返回值

根据 Unix 传统，程序执行成功返回 0，否则返回 1 。

> ```javascript
> if (err) {
>   process.exit(1);
> } else {
>   process.exit(0);
> }
> ```

### 2. **重定向**

Unix 允许程序之间使用管道重定向数据。

> ```bash
> $ ps aux | grep 'node'
> ```

脚本可以通过监听标准输入的data 事件，获取重定向的数据。

> ```javascript
> process.stdin.resume();
> process.stdin.setEncoding('utf8');
> process.stdin.on('data', function(data) {
>   process.stdout.write(data);
> });
> ```

下面是用法。

> ```javascript
> $ echo 'foo' | ./hello
> hello foo
> ```

### 3. 系统信号

操作系统可以向执行中的进程发送信号，process 对象能够监听信号事件。

> ```javascript
> process.on('SIGINT', function () {
>     console.log('Got a SIGINT');
>     process.exit(0);
> });
> ```

发送信号的方法如下。

> ```bash
> $ kill -s SIGINT [process_id]
> ```



## 美化一下

* 美化参考：https://segmentfault.com/a/1190000016555129

### 命令行交互式工具——inquirer.js

使用参考：https://juejin.cn/post/7085145681693769759#heading-14

* https://juejin.cn/post/7085145681693769759

- 可以向用户提出问题
- 解析用户输入的答案
- 对用户的输入进行验证
- 提供错误回调

总结

* 主要使用 prompt 方法
* type 区分交互类型，常用 单选、多选、确认、输入等
* list、checkbox 需要 choices 列表设置
* 每个类型都需要一个name属性，方便收集值
* 成功之后有一个回调，失败有一个回调
* 能够对当前验证、过滤，对上一个命令进行过滤（when）

```js
#!/usr/bin/env node
import inquirer from "inquirer";

// 启动 inquirer
inquirer
  // 交互内容
  .prompt([
    // {
    //     type: "list",
    //     message: "确认吗?",
    //     name: 'sure',
    // },
    {
        type: 'list',
        message: '多选',
        name: 'checkbox',
        default: 2,
        choices: [
          {
            name: '选项1',
            value: 1,
          },
          {
            name: '选项2',
            value: 2,
          },
        ],
    },
    {
      type: 'editor',
      name: 'editor',
      message: '请编辑',
      default: 'test',
      // 校验，不通过的需要重新输入
      validate: (input) => {
        // if (input.includes('test')) {
        //   return '不能包含test'
        // }
        return true
      },
      // 对输入进行校验
      filter: (input) => {
        // console.log(input, '---666');
        // return 'new value'
        return input
      },
      // 对输入进行转换
      transformer: input => {
        console.log(input, 'transformer');
        return input + '??'
      },
    },
    {
      type: 'checkbox',
      name: 'checkbox',
      choices: [
        {
          name: 'hh',
          value: 'hh',
        },
      ],
      // 一般写在第一个之后，前一个符合要求才能展示第二个
      when: (input) => {
        console.log(input, '----when');
        if (input && input.editor.includes('test2')) {
          return true
        }
        return false
      },
    },
  ])
  // 收集用户答案后的回调，会以键值对的方式存储在这里。需要通过之前的问题校验
  .then((answers) => {
    console.log(answers, '--99')
  })
  // 捕获错误的回调
  .catch((error) => {});
```

### 命令行图表

* 这个好像不需要学习

  https://github.com/yaronn/blessed-contrib



### 命令行图标

https://github.com/sindresorhus/ora

```js
#!/usr/bin/env node
import ora from 'ora';

// 主要两个变量：图标和颜色
// 主要两个动作：开始和结束

// ora('message')
const spinner = ora('Loading unicorns').start();
spinner.color = 'red'
spinner.spinner = { // 自定义图标
	interval: 90, // 周期时长
	frames: [
		'✅',
		'❎'
	], // 图标定制，必须是 unicode 码能支持的
}

setTimeout(() => {
	spinner.color = 'yellow';
	spinner.text = 'Loading rainbows';
}, 1000);

setTimeout(() => {
	spinner.stop();
	spinner.start();
}, 2000);

```





### 命令行进度条

https://github.com/visionmedia/node-progress



# node 服务监控

## pm2

对于线上项目，如果直接通过 node app 来启动，如果报错了可能直接停止导致整个服务崩溃

https://juejin.cn/post/6844903710037016584

pm2 特点

* 内建负载均衡（使用 Node cluster 集群模块）

* 后台运行

* 0 秒停机重载

* 具有 Ubuntu 和 CentOS 的启动脚本

* 停止不稳定的进程（避免无限循环）

* 控制台检测

* 提供 HTTP API

* 远程控制和实时的接口 API ( Nodejs 模块,允许和 PM2 进程管理器交互 )

### 环境

* 安装：

  npm i pm2

* 安装typescript

  ```bash
  npx pm2 install typescript
  ```

### 基本操作

* 查看列表 

  ```bash
  npx pm2 ls
  ```

* 查看状态

  ```bash
  npx pm2 show (name|id)
  ```

* 查看内存使用情况

  ```bash
  npx pm2 monit
  ```

* 停止进程

* ```bash
  npx pm2 stop (id|all)
  ```

* 重启进程

  ```bash
  npx pm2 restart (id|all)
  ```

* 删除并杀死进程

  ```bash
  npx pm2 delete (id|all)
  ```

### 多配置项起步

* pm2 支持配置文件启动

  快速生成配置文件

  ```bash
  npx pm2 init simple
  ```

  https://pm2.keymetrics.io/docs/usage/application-declaration/

* 启动

  ```bash
  pm2 start ecosystem.config.js
  ```

  * 入口文件在配置文件中

配置文件实战

```js
module.exports = {
  apps: [
    {
      name: "app1",
      script: "./basic.ts",
      watch: true,
      ignore_watch: ["node_modules", "public"],
      instances: 'max', // 负载均衡配置
      "error_file": "./logs/app-err.log",
      "out_file": "./logs/app-out.log",
      "merge_logs": true,
      "log_date_format": "YYYY-MM-DD HH:mm:ss",
      "max_memory_restart": "1000M", // 程序超过该内存将会重启
      "min_uptime": "60s", // 应用程序的最小正常运行时间被视为已启动
      "max_restarts": 40, // 最大重启次数
      "autorestart": true,
      "restart_delay": 60, // 重启延迟
      "env": {
        NODE_ENV: "development",
      }, // 一般是线上使用pm2，所以环境变量一般设置为 production
      "env_production": {
        NODE_ENV: "production", // 只需要在执行 pm2 的时候加上 npx pm2 start ecosystem.config.js --env production 即可进入该配置
      },
    },
  ],
};

```



### 负载均衡

1. 命令行的方式

   ```bash
   npx pm2 start index.ts -i (2|max)
   ```

   * 开启2个或根据cpu核数个进程

2. 配置文件

   ```js
   {
     instances: 2,
   }
   ```

### 日志

* pm2 会收集 stdout 和 error 的相关信息
* 一般情况下node会使用 log4js 进行日志收集

> 我们正常意义上的日志，以node为例，应该都是使用的`log4js`来进行按日期写入的，那么pm2可不可以按日期写入呢？答案肯定是：可以。

pm2为我们提供了插件系统，而日期分割功能就正好用到了插件系统：[pm2-logrotate](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fkeymetrics%2Fpm2-logrotate)

* 安装

  ```bash
  pm2 install pm2-logrotate
  ```

* 配置

  https://juejin.cn/post/6844904024848728078



刷新日志：（把之前的舍弃掉）

pm2 flush

设置日志前缀，日志前边带日期

pm2 start app.js --time

pm2 restart app --time

https://mdnice.com/writing/4ff85ce4742b4029a3d412c86b51f380



### pm2 状态查看平台

* 可以安装 pm2-web，能够直接访问web进行查看



# npm 命令行

## npm --unsafe-perm 参数

https://segmentfault.com/q/1010000019365121

# 日志相关

* log4js 

  todo 了解一下



# 与 java 或者 go 相比

## esbuild 作者说

https://esbuild.github.io/faq/#why-is-esbuild-fast

此外，Go 的核心是为并行设计而设计的，而 JavaScript 则不是。Go 在线程之间共享内存，而 JavaScript 必须在线程之间序列化数据。Go 和 JavaScript 都有并行的垃圾收集器，但是 Go 的堆在所有线程之间共享，而 JavaScript 的每个 JavaScript 线程都有一个单独的堆。[根据我的测试](https://github.com/evanw/esbuild/issues/111#issuecomment-719910381)，这似乎将 JavaScript 工作线程可能的并行量减少了一半，大概是因为一半的 CPU 内核忙于为另一半收集垃圾。

* Go 的堆在所有线程之间共享，js每个线程都有一个单独的堆（单一线程的劣势，不能够共享数据）
* the world stop 策略。每一个进程都有一套垃圾回收在执行，全套的运行时环境。

个人理解：

* go的并发设计，能够共享堆栈固然好，但是同时操作数据时加入了锁的概念，增加了程序员的心智负担。相比之下js的单线程更符合程序员的直觉。
* js 单线程的模式限制了它并不适合服务端大数据量的开发，浪费内存，性能低下（一个线程一个 v8 gc在执行）
