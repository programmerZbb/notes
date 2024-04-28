# 前置知识

## 观察者

* 观察者模式的两个缺点，rxjs 怎么解决
  * 内存：多个观察者可能存在效率降低
  * 流向：怎么知道是谁改的，谁监听的。

# 概念

## **Observable**

observable 用来产生数据的数据源，

```typescript
import Rx, { Observable } from 'rxjs';

// 普通的 observable 只能单播
const observable = new Observable(observer => {
    observer.next(111)
    setTimeout(() => {
        observer.next(222)
    }, 2000);
    // observer.complete()
})
```

* 使用 new Observable 创建，接受一个参数 productor

> 这个方法接受一个函数作为参数，这个函数叫做 `producer` 函数， 用来生成 `Observable` 的值。这个函数的入参是 `observer`，在函数内部通过调用 `observer.next()` 便可生成有一系列值的一个 `Observable`。

### subscribe 方法

* 能够单独接受 next，error, complete 回调
* 也可以直接接受一个 observer 对象

```typescript
const observer: Observer<any> = {
    next(value) {
        console.log(value)
    },
    error(err) {
        console.log(err)
    },
    complete() {
        console.log('complete')
    }
};
const subscription2 = observable.subscribe(observer)
```

* 调用 next 方法，就会触发 observer 的next方法；调用 complete 就会触发 complete 方法

## observer

一个回调函数的集合，它知道如何去监听由`Observable`提供的值。`Observer`在信号流中是一个观察者（哨兵）的角色，它负责观察任务执行的状态并向流中发射信号。

![Observer](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/600871d0174d48c489da64dfeccdde6e~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

* 用 observer 去订阅 observable。也就是 subscribe 的参数

在`RxJS`中，`Observer`是可选的。在`next`、`error` 和 `complete`处理逻辑部分缺失的情况下，`Observable`仍然能正常运行，未包含的特定通知类型的处理逻辑会被自动忽略。



## Subscription

* observable 调用 subscribe 返回的对象。`Subscription`就是表示`Observable`的执行
* 常用方法：*unsubscribe* 取消订阅，就不再监听流的输出

### add 方法



## subject

![单播与多播](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a859253ee8fb449aac5d0e94c2ded433~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### 单播——unicast

* 一对一。普通的 observable 就是一个单播，每次订阅都会从头开始把值发给订阅者

问题：

某些情况我们希望新订阅者不从头开始接受数据，而是直接从现在开始

### 多播（muticase）

* 一对多。每个订阅者都会接受当前之后新的数据

### 实现一个多播

* 用一个中间体，接受到单播之后，进行广播

### 广播——broadcast



### subject

> 它是一个代理对象，既是一个 `Observable` 又是一个 `Observer`，它可以同时接受 `Observable` 发射出的数据，也可以向订阅了它的 `observer` 发射数据，同时，`Subject` 会对内部的 `observers` 清单进行多播(`multicast`)
>
> ——https://juejin.cn/post/6910943445569765384

* 是一个代理对象，即是一个 observable 又是一个 observer ，意味着有两者的特性
* Subject 会对内部的 observers 进行多播

> `Subjects` 是将任意 `Observable` 执行共享给多个观察者的唯一方式

* 重点：唯一的方式将一个 observable 共享给多个观察者的唯一方式。保证了数据的实时性。

### subject 的双面性

```typescript
// subject 的双面性
const subject2 = new Subject()

subject2.subscribe((value) => {
    console.log(value, '---aaa');
})
subject2.subscribe((value) => {
    console.log(value, '---bbb');
})

subject2.next(111)
subject2.next(222)
```

* 既能够订阅，又能够发射数据

## subject 的变体

### `BehaviorSubject`

* 新的订阅能够拿到一个初始值，就是订阅之前的最后一个状态

#### 初始值

* 实例化时必须有一个初始值，因为任何一个订阅一开始就会发送一个数据

### ReplaySubject

* 能够回放之前的数据，在初始化的时候定制。订阅完成之后，立马回放，然后继续订阅后面的数据

```typescript
import { ReplaySubject } from 'rxjs'

const subject = new ReplaySubject(3) // 回放3个数据

subject.next(111)
subject.next(222)
subject.next(333)
subject.next(444)

subject.subscribe((value) => {
    console.log(value, '----aaa')
})

setTimeout(() => {
    subject.next(555)
}, 300);

setTimeout(() => {
    subject.next(666)
}, 500);

setTimeout(() => {
    subject.subscribe((value) => {
        console.log(value, '----bbb')
    })
}, 1000);
```

#### 默认值

* 重放次数，不传就重放所有

### AsyncSubject

* 接收最后一次发射的数据
* 必须调用 observable.complete()



## ConnectableObservable

* 一个中间体，把 observable 转发给订阅者

> `ConnectableObservable` 是多播的共享 `Observable`，可以同时被多个 `observers`共享订阅，它是 `Hot Observables`。`ConnectableObservable` 是订阅者和真正的源头 `Observables`（上面例子中的 `interval`，每隔一秒发送一个值，就是源头 `Observables`）的中间人，`ConnectableObservable` 从源头 `Observables` 接收到值然后再把值转发给订阅者。

* 是一个 hot Observable。类似一个水龙头，能够控制打开和关闭

然后该对象上提供了`connect`方法让我们控制发送数据的时间。

### connect 方法

* 水龙头开关，调用这个方法之后才会发射数据。订阅者共享一个实例，接收的值取决于他们订阅的时机。==更偏向于 observable 本身去控制流的开始==

> `ConnectableObservable` 并不会主动发送值，它有个 `connect`方法，通过调用 `connect` 方法，可以启动共享 `ConnectableObservable` 发送值。当我们调用 `ConnectableObservable.prototype.connect` 方法，不管有没有被订阅，都会发送值。订阅者共享同一个实例，订阅者接收到的值取决于它们何时开始订阅。

```typescript
import Rxjs, { Subject, Observable, of, refCount } from 'rxjs'
import { publish } from 'rxjs/operators'

// const subject = new Subject()
const ob = publish()(of(1,2,3))
// const ob = of(1,2,3).pipe(publish(), refCount())

ob.subscribe(() => {
    console.log(111)
})

setTimeout(() => {
    ob.subscribe(() => {
        console.log(222)
    })
}, 500);
ob.connect();
```

* connect 执行之后给之前订阅过的发布数据

### refCount——引用计数

* 上面那种控制方式需要手动去控制，有什么方式能够自动进行控制呢？

  也就是达到有订阅才发送数据，订阅全部取消之后就不发送数据呢？

* 使用refCount 引用计数就能够实现

# Cold-Observables与Hot-Observables

## Cold-Observables

* Cold-Observables 只有 observable 被订阅的时候才会进行产生值。是单播的，每一个订阅都会产生一个订阅对象，一个订阅对象会从头开始发布数据（相当于懒发布）。每个订阅者收到的数据都是一样的。

## Hot-Observables

* Hot observables 不管有没有订阅都会生产值。每个订阅接受的值顺序不一样。

## 总结

* observable 是冷播，每次都是一个全新的订阅实例
* subject 是热播，每次都是同一个实例



# Schedulers 调度器

用来控制并发并且是中央集权的调度员，允许我们在发生计算时进行协调，例如 `setTimeout` 或 `requestAnimationFrame` 或其他。

* 调度器是一种数据结构。 它知道如何根据优先级或其他标准来存储任务和将任务进行排序。

* 调度器是执行上下文。 它表示在何时何地执行任务(举例来说，立即的，或另一种回调函数机制(比如 `setTimeout` 或 `process.nextTick`)，或动画帧)。

* 调度器有一个(虚拟的)时钟。 调度器功能通过它的 `getter` 方法 `now()` 提供了“时间”的概念。在具体调度器上安排的任务将严格遵循该时钟所表示的时间。

## 为什么说 rxjs 对并发友好呢？

这就是原因，因为它也有 scheduler 的概念，离散型的流本身就适合做可中断操作。



# 操作符

采用函数式编程风格的纯函数 (`pure function`)，使用像 `map`、`filter`、`concat`、`flatMap` 等这样的操作符来处理集合。也正因为他的纯函数定义，所以我们可以知道调用任意的操作符时都不会改变已存在的`Observable`实例，而是会在原有的基础上返回一个新的`Observable`。

* 纯函数，不是副作用函数

> 尽管 `RxJS` 的根基是 `Observable`，但最有用的还是它的操作符。操作符是允许复杂的异步代码以声明式的方式进行轻松组合的基础代码单元。

* 声明式、基础单元

## 实现一个操作符

* 操作符的本质就是接受一个 Observable 返回一个新的 observable

## 实例操作符和静态操作符

实例操作符：通常是能被实例化的对象直接调用的操作符。我们一般更多会使用实例操作符多一点，比如`filter`、`map`、`concat`等等。使用实例操作符可以更快乐的使用`this`，而省去一个参数，还能维持链式调用。

静态操作符：`Observable`是一个`class`类，我们可以直接把操作符挂载到他的静态属性上，好处在于无需实例化即可调用，缺点在于就无法再使用`this`的方式进行目标对象调用了，而是需要把目标对象传入。

## publish 方法

* 调用 publish 方法，讲一个 observable 转换成一个 ConnectableObservable 



## 创建型操作符

* ==操作的核心是看他生产出的是observable还是一个subject，也就是是一个冷播还是热播。关系到后面的订阅的执行==
* 创建操作符一般直接作为函数使用，不在pipe中使用

### from

* 从一个数组、类数组对象、`Promise`、迭代器对象或者类 `Observable` 对象创建一个 `Observable`.

```typescript
import { from } from 'rxjs'

const source = from([1,2,3])
source.subscribe(value => {
    console.log(value)
})
```

用处：
对现有项目进行改造非常有用，转换成一个 observable

### fromEvent

创建一个 `Observable`，该 `Observable` 发出来自给定事件对象的指定类型事件。可用于浏览器环境中的`Dom`事件或`Node`环境中的`EventEmitter`事件等。

```typescript
import { fromEvent } from 'rxjs'

// process.stdin.on('data', chunk => {
//     console.log(chunk, '---data')
// })
const source = fromEvent(process.stdin, 'data')
source.subscribe(value => {
    console.log(value, '---subscribe')
})

// dom
const click = Rx.Observable.fromEvent(document.getElementById('btn'), 'click');
click.subscribe(x => console.log(x));
```

* 所有的 from 开头的方法都是进行数据转换，并不是直接生产，对老项目作用较大

### FromPromise

Rxjs 6.0 开始已经全部使用 from 操作符来代替 fromPromise 了，参考：https://stackoverflow.com/questions/45784825/frompromise-does-not-exist-on-type-observable

```typescript
import { from } from 'rxjs'

const source = from(new Promise((res, rej) => {
    res(2)
}))

source.subscribe(value => {
    console.log(value, '---000')
})

```

### interval

* 生产一个定时 observable，

> 使用该操作符创建的`Observable`可以在指定时间内发出连续的数字，其实就跟我们使用`setInterval`这种模式差不多。在我们需要获取一段连续的数字时，或者需要定时做一些操作时都可以使用该操作符实现我们的需求。

```typescript
import { interval, take } from 'rxjs'

const source = interval(1000).pipe(take(3))

source.subscribe(value => {
    console.log(value)
})

```

### of

* 从传入参数生产一个 observable。

  与 Array.of 相似

### repeat

* 数据源重复次数

### range 

* 指定范围内的数字序列

```typescript
import { range } from 'rxjs'

const source = range(2, 5)

source.subscribe(value => {
    console.log(value);
})
```

### bindNodeCallback

* 把 node api 转换成一个 observable

```typescript
import * as fs from 'fs';
var readFileAsObservable = Rx.Observable.bindNodeCallback(fs.readFile);
var result = readFileAsObservable('./roadNames.txt', 'utf8');
result.subscribe(x => console.log(x), e => console.error(e));
```

## 转换操作符

* 对输入的数据进行转换、改造，变成我们需要的数据
* 转换操作符只在 pipe 中使用，一般不会单独使用的

### buffer

* 把作用的 observable 缓存起来，直到参数的 observable 把它触发

```typescript
import { interval, buffer, Subject } from 'rxjs'

const subject = new Subject
const source = interval(100).pipe(
    buffer(subject)
)
const subscription = source.subscribe(value => {
    console.log(value, '---111')
})

setTimeout(() => {
    subject.next(222)
    // subject.complete()
    subscription.unsubscribe()
}, 500);
```

* 常用场景就是按钮把它触发，一个蓄水池的效果

### concatMap

* 结合两个 observable

```typescript
import { concatMap, interval, take } from 'rxjs'

const source1 = interval(200).pipe(take(3))
const source = interval(500).pipe(take(3), concatMap(item => source1))
source.subscribe(value => {
    console.log(value)
})
```

* 如果inner observable 开始执行，上一次的 observable 还没结束，则当前的 observable 放到上一个的后面执行
* 其实和 buffer 有点像，只不过触发顺序调换了，是外面的ob触发里面的ob

### map

* 和数组的 map 一样，常用
* 也能够直接返回 observable，但是和 mergeMap 不一样，mergeMap 能够直接把数据合并进来

### mapTo

* 已经没什么意义了，官方建议使用 map

### mergeMap

* 真就是合并 ob 的操作，因为每一项的返回值都是一个 observable 对象。如果不符合要求可以返回一个空 ob——EMPTY

```typescript
import { mergeMap, interval, take, EMPTY, of } from 'rxjs'

const source = interval(100).pipe(
    take(3),
    mergeMap(x => x%2 === 0 ? of(4,5,6) : EMPTY)
)

source.subscribe(value => {
    console.log(value)
})
```

### pluck

* 挑选某个属性，已经被 map 取代了

### scan——扫描

* 和 数组的 reduce 函数一样的。但是和 reduce 不一样的是：reduce 是遍历数组然后拿到最终返回值，scan 是每一个返回都会作为流的一条数据

```typescript
import { scan, interval, take } from 'rxjs'

const source = interval(100).pipe(
    take(4),
    scan((pre, cur) => pre + cur, 0)
)

source.subscribe(value => {
    console.log(value, '---222')
})
```

## 过滤操作符

### debounceTime

* 防抖

### throttleTime

* 节流

### distinct

distinct 不同的

* 去除重复

### filter

* 和数组的一样

### first

* 找出符合条件的第一个数据

```typescript
import { first, interval, take } from 'rxjs'

const source = interval(100).pipe(
    take(4),
    first(item => item > 0)
)

source.subscribe(value => {
    console.log(value)
})
```

### take

* 只取数据源的前n个值（n是参数）

### skip

* 跳过前几个值

一个take 和 skip 结合去中间段的例子

```typescript
import { of, skip, take } from 'rxjs'

const source = of(1,2,3,4,5).pipe(
    skip(2),
    take(2)
)

source.subscribe(value => {
    console.log(value)
})
```

## 组合操作符

组合操作符都是作用于多个 observable，因此调用类型应该是`Observable<Observable<unkonw>>` 这种类型

### concatAll

* 合并 observable，只能是 `Observable<Observable<number>>` 类型才能调用（两个 ob 合并成一个）
* ==和 mergeMap 是有区别的，mergeMap 会按执行顺序合并数据源；concatAll会按源顺序合并，执行的时候也是顺序执行，执行完一个再进行下一个执行==

```typescript
import { concatAll, map, interval, of, take } from 'rxjs'

const source1 = of(1,2,3).pipe(
    map(item => interval(100).pipe(take(3)))
)

const source = source1.pipe(concatAll())

source.subscribe(value => {
    console.log(value)
})
```

### mergeAll

* 和 mergeMap 是一样的，按照时间顺序并发执行

### combineLatest

* 一个对象每个元素都拿到最新值。对于一个对象类型的复杂 observable 是有用的

```typescript
import { combineLatest, interval, take } from 'rxjs'

const observables = {
    a: interval(100).pipe(take(2)),
    b: interval(150).pipe(take(2)),
    c: interval(170).pipe(take(2)),
}

const source = combineLatest(observables)

source.subscribe(value => {
    console.log(value)
})
```

### zip

* ==有点牛逼的==。就是把几列数据对齐，超出的数据不再处理。返回的是数组数据
* 最牛逼的是结合map，能够直接组装对象，将单个的数据组装成对象

```typescript
import { zip, interval, take, map } from 'rxjs'

const age$ = interval(100).pipe(take(3))
const name$ = interval(120).pipe(take(4))
const isDev$ = interval(150).pipe(take(5))

const source = zip(age$, name$, isDev$).pipe(
    map(([age, name, isDev]) => ({ age, name, isDev }))
)
source.subscribe(value => {
    console.log(value, '---222')
})
```

### startWith

* 加个发射初始值

### switch

* 已废弃



## 一个数据流的基本操作

* 拉长：使用mergeMap，能够添加一些数据。也可以减少，也可以转换
* 原长度转换：map
* 减少：filter

mergeMap 能力非常强，能够实现 filter 和map的能力



## 奇淫技巧

### 1. 将数组数据源转换成对象

```typescript
const source1 = of([1,2,3]).pipe(
    map(([a, b, c]) => ({ a, b, c }))
)
```

## 总结

* 创建型操作符 返回一个 observable 类型的数据
* 转换型操作符 返回一个 OperatorFunction，能够在 pipe 中使用



# 什么时候用呢？

* 当你需要处理大量串行的异步数据的时候可以使用。不需要自己建队列，不需要自己处理队列，不需要设置什么定时器（定时处理的场景）
* 当你需要写内核，并且内核需要向外广播数据的场景下，可以使用 rxjs，既有发布订阅能力，又能优雅的处理异步问题。

## 为什么还没大规模推广呢？

* 说明大部分前端业务的复杂度还不够，生命周期也比较短，用了rxjs感觉有些浪费。



# 好文参考

https://juejin.cn/post/6910943445569765384#heading-11

https://juejin.cn/post/7252171043383640123?searchId=20240411145914D9888DE61FF8B80BAC37