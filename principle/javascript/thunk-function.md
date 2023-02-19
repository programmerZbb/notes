参考：https://www.ruanyifeng.com/blog/2015/05/thunk.html

# thunk 函数是什么

## 1. 参数求值策略

* 参数的实参在传入的时候是否需要计算出最终的值呢？
  * call by value: c 语言采用，传入之前已经把值计算出来了
  * call by name: 传入参数不计算，传入一个表达式，在真正用的时候再计算

## JavaScript 中的 thunk 函数

JavaScript 语言是传值调用，它的 Thunk 函数含义有所不同。**在 JavaScript 语言中，Thunk 函数替换的不是表达式，而是多参数函数，将其替换成单参数的版本，且只接受回调函数作为参数。**

* 替换的是函数，而不是参数表达式

```typescript
// 正常版本的readFile（多参数版本）
fs.readFile(fileName, callback);

// Thunk版本的readFile（单参数版本）
var readFileThunk = Thunk(fileName);
readFileThunk(callback);

var Thunk = function (fileName){
  return function (callback){
    return fs.readFile(fileName, callback); 
  };
};
```

任何函数，只要参数是回调函数的函数，就能写成 thunk 函数的形式



## 个人理解

也就是把回调函数从参数中拿了出来，成了一个单独的函数，那就就能像同步一样，把这个通过执行这个函数进行一些操作



## thunk 函数和科里化

* 科里化是一种手段
* thunk函数是一种具体的实现，要求参数必须有个callback。是科里化之后的结果。