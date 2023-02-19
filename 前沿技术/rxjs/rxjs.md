# 前言

## 1. 入门参考

* https://zhuanlan.zhihu.com/p/104024245



## 极简入门

### 状态

三种状态：

1. 它跟 Promise 很类似，区别在于它可以提供多值传递，因此共有 `next`、`error`、`complete` 三种状态；
2. 它通过 `subscribe` 方法关联生产者与消费者，==`subscribe` 像一条生产线的开关==，只有它启动了生产者的生产才会开始；桥梁

```js
const ob$ = Observable.create(observer => { // 生产者
  observer.next(1); // 触发权柄控制在数据生产者这里
  setTimeout(() => {
    observer.next(2);
    observer.complete();
  }, 1000);
});

const observer = {  // 消费者
  next: result => console.log('result: ', result),
  error: err => console.error('something wrong occurred: ' + err),
  complete: () => console.log('done'),
};

ob$.subscribe(observer); // 生产者调用消费者

// 输出结果：
// result: 1
// result: 2
// done
```

### 执行

```js
import { Observable } from "rxjs";

// 流的创建
const stream$ = new Observable(subscriber => {
  setTimeout(() => {
    subscriber.next([1, 2, 3]);
  }, 500);
});

// 如何消费流产生的数据，observer
const observer = {
  complete: () => console.log("done"),
  next: v => console.log(v),
  error: () => console.log("error")
};
const subscription = stream$.subscribe(observer);
// 该方法触发，启动整个流的执行
```

### 停止

```js
subscription.unsubscribe();
```

* 停止之后不会再执行之后的流程

* 字段详解：最简Rxjs入门教程--别再被Rxjs的概念淹没了 - 芋仔的文章 - 知乎 https://zhuanlan.zhihu.com/p/274469124

### observer

`observer`，代码中是`stream$.subscribe(observer)`，对应到Rxjs中，也是称之为observer，从英文翻译到中文的含义来看，也很难理解。从行为上来看，无非就是定义了如何处理上述流产生的数据，称之为流的处理方法，更容易理解

#### 总结

* 通过 subscribe 来触发这个流水线，如果没有触发只是单纯的定义，不会执行。

> Subscription = Observable.subscribe(observer) 
> observable: 随着时间产生的数据集合，可以理解为流，其subscribe方法可以启动该流 
> observer: 决定如何处理数据 
> subscription: 存储已经启动过的流，其unsubscribe方法可以停止该流

### 异步代码统一的范式——创建类操作符

![img](https://pic3.zhimg.com/80/v2-1bc126d4f7032e6b6ec5c57e1d1242ca_720w.jpg)

```js
/* 使用创建类操作符对异步事件输入的统一 */
import { from, fromEvent, of, interval, merge } from 'rxjs'; 
const ajax = () => fetch(url).then(v => v.json())

merge(
  from([1 ,2, 3]), // 从数组多值创建
  interval(3000),  // 定时器轮训创建
  fromEvent(document.getElementById('btn'), 'click'), // 按钮点击事件
  of({ hello: 'world' }), // 单值创建
  from(ajax()),    // 从接口请求创建
).subscribe()
```

* 同步、异步 都能加入流中，进行统一管理

### 操作符

* 除了上述的类操作符，RxJS 还提供了种类繁多的大量操作符，专门解决竞态危害：

```js
const tabSwitch$ = fromEvent(tab, 'click');

tabSwitch$.pipe(
    debounceTime(300), // 300ms 没有变动触发一次
    switchMap((curTab) => from(fetch(`/api/${curTab}`))), // 转换为新的请求流
    tap(render),
).subscribe();
```

例如上面提到的 `switchMap`、`debounceTime` 操作符，就是专门处理高阶 Observable 的顺序问题。

![img](https://pic1.zhimg.com/80/v2-e93be46f0198acdac8d91b125ca91684_720w.jpg)

### 总结

> 1. RxJS 通过了Observable 和操作符，解决了==异步编程的模式统一和时序问题==，让你的异步代码规范而简洁；
> 2. 什么场景下应该使用 RxJS？根据上文所述，RxJS 是用于解决竞态危害问题的。如果你的业务场景中有大量的异步行为，而且它们的执行顺序错乱会导致输出的不正确，这时候就应该考虑引入 RxJS 来规范你的代码了

* 可以把 RxJS 当做是用来处理事件的 [Lodash](https://lodash.com/)

#### 理解

* 先书写数据流程，组装好流水线。一旦控制中心工作，流水线就开始执行了。



### 基本概念

在 RxJS 中用来解决异步事件管理的的基本概念是：

- **Observable (可观察对象):** 表示一个概念，这个概念是一个可调用的未来值或事件的集合。
- **Observer (观察者):** 一个回调函数的集合，它知道如何去监听由 Observable 提供的值。
- **Subscription (订阅):** 表示 Observable 的执行，主要用于取消 Observable 的执行。
- **Operators (操作符):** 采用函数式编程风格的纯函数 (pure function)，使用像 `map`、`filter`、`concat`、`flatMap` 等这样的操作符来处理集合。
- **Subject (主体):** 相当于 EventEmitter，并且是将值或事件多路推送给多个 Observer 的唯一方式。
- **Schedulers (调度器):** 用来控制并发并且是中央集权的调度员，允许我们在发生计算时进行协调，例如 `setTimeout` 或 `requestAnimationFrame` 或其他。



## Subject

**什么是 Subject？** - RxJS Subject 是一种特殊类型的 Observable，它允许将值多播给多个观察者，所以 Subject 是多播的，而普通的 Observables 是单播的(每个已订阅的观察者都拥有 Observable 的独立执行)。

```js
import { Subject } from 'rxjs'

const subject = new Subject()
// subject.subscribe(v => console.log('0---', v))

subject.next(1)
subject.next(2)
setTimeout(() => {
    subject.next(3)
}, 1000);

subject.subscribe(v => console.log('0---', v)) // 只执行了 subject.next(3)
```

* 订阅触发 subscribe 必须在 next 之前执行

> 可以看到，Subject的行为和发布订阅模式非常接近，subscribe去订阅，next触发。事件的订阅通过subscribe，事件的触发使用next，从而实现一个发布订阅的模式。可以说，笔者本人是看到这个Subject，终于和已有的知识体系打通，之后才重新阅读官方文档，才算是弄懂了点皮毛。当然，subject还有别的用法，此处不再详细介绍。

* 使用上面的方法，我们基本上只是通过 Subject 将单播的 Observable 执行转换为多播的。这也说明了 Subjects 是将任意 Observable 执行共享给多个观察者的唯一方式。

