# generator 含义

https://www.ruanyifeng.com/blog/2015/04/generator.html

* 为什么需要generator

  想着同步的方式写异步流程，去掉generator一模一样

## 基本概念

* 是一个状态机，封装了多种内部状态
* 是一个遍历器生成函数。执行 generator 函数会返回一个遍历器对象，可以依次遍历 generator 函数内部的每一个状态

### 执行

不同的是，调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是上一章介绍的遍历器对象（Iterator Object）。

* 与 Iterator 的联系

  *返回的是一个对象，代表generator函数的内部指针，但是已经部署了 Symbol.iterator*

  可以使用 [...] 直接把迭代器一下执行完成

  ==返回对象的 Symbol.iterator 方法执行之后返回对象本身==

* 执行完成的时候 value 为返回的值（没返回就是 undefined）

> 每次调用`next`方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个`yield`表达式（或`return`语句）为止。换言之，Generator 函数是分段执行的，`yield`表达式是暂停执行的标记，而`next`方法可以恢复执行。

==执行generator函数，不会立即执行该函数，只有调用next才会执行，找到下一个yield暂停==

### 使用

直接执行会返回 iterator 对象，然后调用 next 开始递归

* 只有 yield 或 return 能暂停
* next 移动指针
* 指针完成之后，再调用 next 值就是 undefined

### 实现一个可迭代对象

generator ==返回对象的 Symbol.iterator 方法执行之后返回对象本身==，用这个思路可以实现对象的可迭代。

```typescript
// 实现一个 iteratorable 对象
const MyIterator: any = {
    test: 222,
    name: 333,
};
MyIterator[Symbol.iterator] = function* () {
    yield MyIterator['test']
    yield MyIterator['name']
}
console.log([...MyIterator]);
```

### 迭代器执行规则

遍历器对象的`next`方法的运行逻辑如下。

（1）遇到`yield`表达式，就暂停执行后面的操作，并将紧跟在`yield`后面的那个表达式的值，作为返回的对象的`value`属性值。

（2）下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式。

（3）如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。

（4）如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

只有遇到 yield 才会暂停执行后面的操作



### yield 用在表达式中

yield 用在表达式中需要使用括号

```typescript
function* demo() {
  console.log('Hello' + yield); // SyntaxError
  console.log('Hello' + yield 123); // SyntaxError

  console.log('Hello' + (yield)); // OK
  console.log('Hello' + (yield 123)); // OK
}
```



### 惰性求值

* generator 本身也有惰性求值的用处，yield 后面的语句只有调用 next 才会执行

  > 需要注意的是，`yield`表达式后面的表达式，只有当调用`next`方法、内部指针指向该语句时才会执行，因此等于为 JavaScript 提供了手动的“惰性求值”（Lazy Evaluation）的语法功能。

## 返回值

* yield 后面语句的返回值会在 next 的value中提现

* yield 表达式左边的值，由执行 next 函数时传入的值决定

  ```typescript
  const x = yield 1
  ```

  * 第一次执行 next 时传入的值会被忽略

## for of 遍历

* for of 能够遍历 generator 函数返回的 iterator 对象

  每一个 item 都是 iterator 的值 

## 哪些方法需要部署 iterator 接口

除了`for...of`循环以外，扩展运算符（`...`）、解构赋值和`Array.from`方法内部调用的，都是遍历器接口。

```typescript
function* numbers () {
  yield 1
  yield 2
  return 3
  yield 4
}

// 扩展运算符
[...numbers()] // [1, 2]

// Array.from 方法
Array.from(numbers()) // [1, 2]

// 解构赋值
let [x, y] = numbers();
x // 1
y // 2

// for...of 循环
for (let n of numbers()) {
  console.log(n)
}
// 1
// 2
```



## 协程

传统的编程语言，早有异步编程的解决方案（其实是多任务的解决方案）。其中有一种叫做["协程"](https://en.wikipedia.org/wiki/Coroutine)（coroutine），意思是多个线程互相协作，完成异步任务。



协程和线程还是有区别的，通常有这几个方面：

\- 线程通常是系统实现的（也有语言模拟的，如Python的线程），协程通常是库实现的（也有语言实现的，如Go）。
\- 线程的控制权通常是系统控制的，是被动的，协程的控制权通常是调用方主动控制的（需要主动yield释放控制权，Python如此，Lua如此，此处也是），这一点很重要，导致普通的协程程序很难写。
\- 由于线程是系统控制的，通常切换需要进入内核态进行处理，协程通常是在用户态就完成了切换，这一点也很重要。
\- 协程切换是在一个线程内进行的

## generator 干了什么

能实现执行权的交换，把执行权交给其他协程，执行完成之后再拿到执行权



## 与promise

* yield 后跟 Promise 不能直接拿到结果，而是拿到这个 promise 对象，你需要使用then 执行完这个 Promise 对象，然后再把控制权交给 generator。

  因此 generator 只是一个状态机

## 总结

* 一个状态机
* 返回一个迭代器，能够遍历这个状态机
* 能够实现控制权的转移



## 用法

* 除了 yield 和同步写法一模一样