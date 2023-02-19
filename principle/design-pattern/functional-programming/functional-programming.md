# 简介

* 来源于读这本书：https://llh911001.gitbooks.io/mostly-adequate-guide-chinese/content/ch1.html#%E4%BB%8B%E7%BB%8D

  后期实践还需补充

# 起步

* 遇到问题——todo什么意思

  可变状态（mutable state）、无限制副作用（unrestricted side effects）和无原则设计（unprincipled design）

> 现在已经有一些通用的编程原则了，各种缩写词带领我们在编程的黑暗隧道里前行：DRY（不要重复自己，don't repeat yourself），高内聚低耦合（loose coupling high cohesion），YAGNI （你不会用到它的，ya ain't gonna need it），最小意外原则（Principle of least surprise），单一责任（single responsibility）等等。

# 纯函数

## 纯函数的好处

* 定义：

  > 纯函数是这样一种函数，即相同的输入，永远会得到相同的输出，而且没有任何可观察的副作用。

> 比如 `slice` 和 `splice`，这两个函数的作用并无二致——但是注意，它们各自的方式却大不同，但不管怎么说作用还是一样的。我们说 `slice` 符合*纯*函数的定义是因为对相同的输入它保证能返回相同的输出。而 `splice` 却会嚼烂调用它的那个数组，然后再吐出来；这就会产生可观察到的副作用，即这个数组永久地改变了。

* 引入系统变量的例子

```js
// 不纯的
var minimum = 21;

var checkAge = function(age) {
  return age >= minimum;
};


// 纯的
var checkAge = function(age) {
  var minimum = 21;
  return age >= minimum;
};
```

> 在不纯的版本中，`checkAge` 的结果将取决于 `minimum` 这个可变变量的值。换句话说，它取决于==系统状态（system state）==；这一点令人沮丧，因为它引入了外部的环境，从而增加了==认知负荷（cognitive load）==。

* 系统状态、认知负荷