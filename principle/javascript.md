# Object 补充

## 1. seal 密封

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/seal

通常，一个对象是[可扩展的](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/isExtensible)（可以添加新的属性）。密封一个对象会让这个对象变的不能添加新属性，且所有已有属性会变的不可配置。属性不可配置的效果就是属性变的==不可删除==，以及一个数据属性不能被重新定义成为访问器属性，或者反之。但属性的值仍然可以修改。尝试删除一个密封对象的属性或者将某个密封对象的属性从数据属性转换成访问器属性，结果会静默失败或抛出[`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError)org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode) 中最常见的，但不唯一）。

不会影响从原型链上继承的属性。但 [`__proto__`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/proto)

* 数据描述符变化：`configurable` 变为 false



## 2. freeze

使用`Object.freeze()`冻结的对象中的现有属性值是不可变的。用`Object.seal()`密封的对象可以改变其现有属性值。

* 数据描述符：`configurable` 变为 false，`writable` 变为 false



## 3. Object.prototype.hasOwnProperty

* 只能识别本身的属性，不能识别原型链上的属性

## 4. Object.is

* Object.is 能够判断所有的变量之间是否相等。
* 包括 +0 / -0，NaN



## 5. Object.defineProperty

* **Object.defineProperty()**  方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。

  ==定义或者修改==

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty

### 描述（第三个参数）

该方法允许精确地添加或修改对象的属性。通过赋值操作添加的普通属性是可枚举的，在枚举对象属性时会被枚举到（[`for...in`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in)ps://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)[ ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)方法），可以改变这些属性的值，也可以[`删除`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/delete))))这些属性。这个方法允许修改默认的额外选项（或配置）。默认情况下，使用 `Object.defineProperty()` 添加的属性值是不可修改（immutable）的。

writable 为 false

对象里目前存在的属性描述符有两种主要形式：==*数据描述符*和*存取描述符*==。*数据描述符*是一个==具有值的属性==，该值可以是可写的，也可以是不可写的。*存取描述符*是由 getter 函数和 setter 函数所描述的属性。==一个描述符只能是这两者其中之一；不能同时是两者。==(每个都有四种)

## 6. Object.create

* 以后面的对象为基础，创建一个对象。（绑定到了返回对象的原型上）



## 7. Object.assign

* `Object.assign()`方法用于将==所有可枚举属性的值==从一个或多个源对象复制到目标对象。必须所有的值都可以枚举。
* 是基于第一个参数的修改！



## 8.Object.prototype.hasOwnProperty

* 能够直接使用 改变 this 指向的方案判断任何类型是否有某个属性



### 两种形式共享设置

#### 1. `configurable`

当且仅当该属性的 `configurable` 键值为 `true` 时，该属性的描述符才能够被改变，同时该属性也能从对应的对象上被删除。
**默认为** **`false`**。

如果属性已经存在，`Object.defineProperty()`将尝试根据描述符中的值以及对象当前的配置来修改这个属性。如果旧描述符将其`configurable` 属性设置为`false`，则该属性被认为是“不可配置的”，并且没有属性可以被改变（除了单向改变 writable 为 false）。当属性不可配置时，不能在数据和访问器属性类型之间切换。

`configurable` 特性表示对象的属性是否可以被删除，以及除 `value` 和 `writable` 特性外的其他特性是否可以被修改。(实例限制能否被删除的行为)

==用来控制属性的描述符能否修改==

#### 2. enumerable

* 直接赋值和设置这个配置都会使这个属性变成可遍历的（for in 和 Object.keys）

  ```js
  obj.test = 1 // 可遍历的
  ```

  

#### 描述符默认值汇总

- 拥有布尔值的键 `configurable`、`enumerable` 和 `writable` 的默认值都是 `false`。
- 属性值和函数的键 `value`、`get` 和 `set` 字段的默认值为 `undefined`。

### 存取描述符

```
get
```

属性的 getter 函数，如果没有 getter，则为 `undefined`。当访问该属性时，会调用此函数。执行时不传入任何参数，但是会传入 `this` 对象（由于继承关系，这里的`this`并不一定是定义该属性的对象）。该函数的返回值会被用作属性的值。
**默认为 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)**。

```
set
```

属性的 setter 函数，如果没有 setter，则为 `undefined`。当属性值被修改时，会调用此函数。该方法接受一个参数（也就是被赋予的新值），会传入赋值时的 `this` 对象。
**默认为 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)**。



### Object.keys 和 for in

* for...in 能够遍历原型上的属性。Object.keys 不行

### 继承属性

* 如果劫持的 getter 中使用了 this 。这个this 指向当前实例。不能再继承中共享。

  ```js
  function myclass() {
  }
  
  Object.defineProperty(myclass.prototype, "x", {
    get() {
      return this.stored_x;
    },
    set(x) {
      this.stored_x = x;
    }
  });
  
  var a = new myclass();
  var b = new myclass();
  a.x = 1;
  console.log(b.x); // undefined
  ```

* 不像访问者属性，值属性始终在对象自身上设置，而不是一个原型。然而，如果一个不可写的属性被继承，它仍然可以防止修改对象的属性。

  ```js
  function myclass() {
  }
  
  myclass.prototype.x = 1;
  Object.defineProperty(myclass.prototype, "y", {
    writable: false,
    value: 1
  });
  
  var a = new myclass();
  a.x = 2;
  console.log(a.x); // 2
  console.log(myclass.prototype.x); // 1
  a.y = 2; // Ignored, throws in strict mode
  console.log(a.y); // 1
  console.log(myclass.prototype.y); // 1
  ```

  注意：==使用实例对象 fn.test = 1 修改的属性是他本身的属性，不是 prototype继承的属性==。只是能共享 这个数据

### 总结

* 如果一个描述符不具有 `value`、`writable`、`get` 和 `set` 中的任意一个键，那么它将被认为是一个数据描述符。如果一个描述符同时拥有 `value` 或 `writable` 和 `get` 或 `set` 键，则会产生一个异常。
* 如果直接赋值 `obj.test = 1` 会采用数据描述符。所有 布尔 类型的配置会变成 true
* getter 和 setter 方法的调用者都是实例对象
* 继承的属性只能获取 get，不能直接修改。最起码对继承来源对象无效。



## Object.prototype.valueOf

* 是一个函数，返回这个对象参与计算时的值

* 如果是一个函数，在 valueOf 没有重写的情况下，默认会调用 toString() 方法进行计算。

   valueOf > toString





# JSON

## JSON.parse

* 参数：

  ```
  text
  ```

  要被解析成 JavaScript 值的字符串，关于JSON的语法格式,请参考：[`JSON`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON)。

  `reviver` 可选

  转换器, 如果传入该参数(函数)，可以用来修改解析生成的原始值，调用时机在 parse 函数返回之前。

  > 如果指定了 `reviver` 函数，则解析出的 JavaScript 值（解析值）会经过一次转换后才将被最终返回（返回值）。更具体点讲就是：解析值本身以及它所包含的所有属性，会按照一定的顺序（从最最里层的属性开始，一级级往外，最终到达顶层，也就是解析值本身）分别的去调用 `reviver` 函数，在调用过程中，当前属性所属的对象会作为 `this` 值，当前属性名和属性值会分别作为第一个和第二个参数传入 `reviver` 中。如果 `reviver` 返回 `undefined`，则当前属性会从所属对象中删除，如果返回了其他值，则返回的值会成为当前属性新的属性值。
  >
  > 当遍历到最顶层的值（解析值）时，传入 `reviver` 函数的参数会是空字符串 `""`（因为此时已经没有真正的属性）和当前的解析值（有可能已经被修改过了），当前的 `this` 值会是 `{"": 修改过的解析值}`，在编写 `reviver` 函数时，要注意到这个特例。（这个函数的遍历顺序依照：从最内层开始，按照层级顺序，依次向外遍历）

  ```js
  JSON.parse('{"1": 1, "2": 2,"3": {"4": 4, "5": {"6": 6}}}', function (k, v) {
      console.log(k); // 输出当前的属性名，从而得知遍历顺序是从内向外的，
                      // 最后一个属性名会是个空字符串。
      return v;       // 返回原始属性值，相当于没有传递 reviver 参数。
  });
  
  // 1
  // 2
  // 4
  // 6
  // 5
  // 3
  // ""
  ```



## JSON.stringify

* 参数

  ```
  value
  ```

  将要序列化成 一个 JSON 字符串的值。

  `replacer` 可选

  如果该参数是一个函数，则在序列化过程中，被序列化的值的每个属性都会经过该函数的转换和处理；如果该参数是一个数组，则只有包含在这个数组中的属性名才会被序列化到最终的 JSON 字符串中；如果该参数为 null 或者未提供，则对象所有的属性都会被序列化。

  `space` 可选

  指定缩进用的空白字符串，用于美化输出（pretty-print）；如果参数是个数字，它代表有多少的空格；上限为10。该值若小于1，则意味着没有空格；如果该参数为字符串（当字符串长度超过10个字母，取其前10个字母），该字符串将被作为空格；如果该参数没有提供（或者为 null），将没有空格。

> - 转换值如果有 toJSON() 方法，该方法定义什么值将被序列化。
> - 非数组对象的属性不能保证以特定的顺序出现在序列化后的字符串中。
> - 布尔值、数字、字符串的包装对象在序列化过程中会自动转换成对应的原始值。
> - `undefined`、任意的函数以及 symbol 值，在序列化过程中会被忽略（出现在非数组对象的属性值中时）或者被转换成 `null`（出现在数组中时）。函数、undefined 被单独转换时，会返回 undefined，如`JSON.stringify(function(){})` or `JSON.stringify(undefined)`.
> - 对包含循环引用的对象（对象之间相互引用，形成无限循环）执行此方法，会抛出错误。
> - 所有以 symbol 为属性键的属性都会被完全忽略掉，即便 `replacer` 参数中强制指定包含了它们。
> - Date 日期调用了 toJSON() 将其转换为了 string 字符串（同Date.toISOString()），因此会被当做字符串处理。
> - NaN 和 Infinity 格式的数值及 null 都会被当做 null。
> - 其他类型的对象，包括 Map/Set/WeakMap/WeakSet，仅会序列化可枚举的属性。



# 类型总结

基础类型：string、number、boolean、undefined、null、symbol

引用类型：object (Array，Function，Object，Promise)



# ==array==

## reduce 

https://www.jianshu.com/p/e375ba1cfc47



# ==function==

可调用的对象

## tostring 方法

* Function.prototype.toString
  * 本身是一个方法
  * 返回当前函数源代码的字符串
  * 如果想修改函数体参与计算的值，可以重写 toString 方法，主要是修改它的返回值
* 对比 Object 的 tostring
  * object 打印类型
  * function 打印 源码



# ==原则问题==

* 方法一遍挂载到 prototype 上。这个一定要事先搞清楚



# JavaScript 内存管理

像C语言这样的底层语言一般都有底层的内存管理接口，比如 `malloc()`和`free()`。相反，JavaScript是在创建变量（对象，字符串等）时自动进行了分配内存，并且在不使用它们时“自动”释放。 释放的过程称为垃圾回收。这个“自动”是混乱的根源，并让JavaScript（和其他高级语言）开发者错误的感觉他们可以不关心内存管理。 

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Memory_Management

## 内存声明周期

不管什么程序语言，内存生命周期基本是一致的：  

1. 分配你所需要的内存
2. 使用分配到的内存（读、写）
3. 不需要时将其释放\归还

所有语言第二部分都是明确的。第一和第三部分在底层语言中是明确的，但在像JavaScript这些高级语言中，大部分都是隐含的。

<font color="red" style="font-weight: 700;" >重点内容</font>

## [JavaScript 的内存分配](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Memory_Management#javascript_的内存分配)

### 垃圾回收

大多数内存管理的问题都在这个阶段。在这里最艰难的任务是找到“哪些被分配的内存确实已经不再需要了”。它往往要求开发人员来确定在程序中哪一块内存不再需要并且释放它。

高级语言解释器嵌入了“垃圾回收器”，它的主要工作是跟踪内存的分配和使用，以便当分配的内存不再使用时，自动释放它。这只能是一个近似的过程，因为要知道是否仍然需要某块内存是[无法判定的](http://en.wikipedia.org/wiki/Decidability_(logic))（无法通过某种算法解决）。



如上文所述自动寻找是否一些内存“不再需要”的问题是无法判定的。因此，垃圾回收实现只能有限制的解决一般问题。本节将解释必要的概念，了解主要的垃圾回收算法和它们的局限性。

* 回收也是先回收 函数执行玩的局部变量。

### [引用计数垃圾收集](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Memory_Management#引用计数垃圾收集)

这是最初级的垃圾收集算法。此算法把“对象是否不再需要”简化定义为“对象有没有其他对象引用到它”。如果没有引用指向该对象（零引用），对象将被垃圾回收机制回收。

#### 问题

该算法有个限制：无法处理循环引用的事例。在下面的例子中，两个对象被创建，并互相引用，形成了一个循环。它们被调用之后会离开函数作用域，所以它们已经没有用了，可以被回收了。然而，引用计数算法考虑到它们互相都有至少一次引用，所以它们不会被回收。

```js
function f(){
  var o = {};
  var o2 = {};
  o.a = o2; // o 引用 o2
  o2.a = o; // o2 引用 o

  return "azerty";
}

f();
```

#### 实际例子

IE 6, 7 使用引用计数方式对 DOM 对象进行垃圾回收。该方式常常造成对象被循环引用时内存发生泄漏：

```js
var div;
window.onload = function(){
  div = document.getElementById("myDivElement");
  div.circularReference = div;
  div.lotsOfData = new Array(10000).join("*");
};
```

在上面的例子里，`myDivElement` 这个 DOM 元素里的 `circularReference 属性`引用了 `myDivElement`，造成了循环引用。如果该属性没有显示移除或者设为 null，引用计数式垃圾收集器将总是且至少有一个引用，并将一直保持在内存里的 DOM 元素，即使其从DOM 树中删去了。如果这个 DOM 元素拥有大量的数据 (如上的 `lotsOfData` 属性)，而这个数据占用的内存将永远不会被释放。

### [标记-清除算法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Memory_Management#标记-清除算法)

这个算法把“对象是否不再需要”简化定义为“对象是否可以获得”。

这个算法假定设置一个叫做根（root）的对象（在Javascript里，根是全局对象）。垃圾回收器将定期从根开始，找所有从根开始引用的对象，然后找这些对象引用的对象……从根开始，垃圾回收器将找到所有可以获得的对象和收集所有不能获得的对象。

从2012年起，所有现代浏览器都使用了标记-清除垃圾回收算法。所有对JavaScript垃圾回收算法的改进都是基于标记-清除算法的改进，并没有改进标记-清除算法本身和它对“对象是否不再需要”的简化定义。

### 扩展

https://zhuanlan.zhihu.com/p/352323793

## JavaScript 内存管理

部分语言需要（例如`C`语言）需要手动去释放内存，但是会很麻烦，所以很多语言，例如`JAVA`都会提供自动的内存管理机制，称为“**垃圾回收机制**”，`JavaScript`语言中也提供了垃圾回收机制（`Garbage Collecation`），简称**GC机制**。

### 全停顿

在介绍垃圾回收算法之前，我们先了解一下「**全停顿**」。垃圾回收算法在执行前，需要将应用逻辑暂停，执行完垃圾回收后再执行应用逻辑，这种行为称为 「**全停顿**」（`Stop The World`）。例如，如果一次GC需要50ms，应用逻辑就会暂停50ms。

全停顿的目的，是为了解决应用逻辑与垃圾回收器看到的情况不一致的问题。

举个例子，在自助餐厅吃饭，高高兴兴地取完食物回来时，结果发现自己餐具被服务员收走了。这里，服务员好比垃圾回收器，餐具就像是分配的对象，我们就是应用逻辑。在我们看来，只是将餐具临时放在桌上，但是服务员看来觉得你已经不需要使用了，因此就收走了。你与服务员对于同一个事物看到的情况是不一致，导致服务员做了与我们不期望的事情。因此，为避免应用逻辑与垃圾回收器看到的情况不一致，垃圾回收算法在执行时，需要停止应用逻辑。

### `JavaScript`中的垃圾回收

JavaScript中会被判定为垃圾的情形如下：

- 对象不再被引用；
- 对象不能从根上访问到；

### `GC`算法

常见的`GC`算法如下：

- 引用计数
- 标记清除
- 标记整理
- 分代回收

### 引用计数

早期的浏览器最常使用的垃圾回收方法叫做"**引用计数**"（`reference counting`）：语言引擎有一张"引用表"，保存了内存里面所有的资源（通常是各种值）的引用次数。如果一个值的引用次数是0，就表示这个值不再用到了，因此可以将这块内存释放。

* 问题：循环引用

引用计数算法优点：

- 引用计数为零时，发现垃圾立即回收；
- 最大限度减少程序暂停；

引用计数算法缺点：

- 无法回收循环引用的对象；
- 空间开销比较大；

### 标记清除（Mark-Sweep）

核心思想：分标记和清除两个阶段完成。

1. 遍历所有对象找标记活动对象；
2. 遍历所有对象清除没有标记对象；
3. 回收相应的空间。

标记清除算法的优点是：对比引用计数算法，标记清除算法最大的优点是能够回收循环引用的对象，它也是v8引擎使用最多的算法。

上图我们可以看到，红色区域是一个根对象，就是一个全局变量，会被标记；而蓝色区域就是没有被标记的对象，会被回收机制回收。这时就会出现一个问题，表面上蓝色区域被回收了三个空间，但是这三个空间是不连续的，当我们有一个需要三个空间的对象，那么我们刚刚被回收的空间是不能被分配的，这就是“空间碎片化”。

* 全局对象在执行完成之后，也不被回收，因为有引用（即浏览器占用的内存）。取决于 scope chain——全局上下文。

### 标记整理（Mark-Compact）

为了解决内存碎片化的问题，提高对内存的利用，引入了标记整理算法。

标记整理可以看做是标记清除的**增强**。标记阶段的操作和标记清除一致。

清除阶段会先执行整理，移动对象位置,将存活的对象移动到一边，然后再清理端边界外的内存。

标记整理的缺点是：移动对象位置，不会立即回收对象，回收的效率比较慢。

### 增量标记（Incremental Marking）

为了减少全停顿的时间，`V8`对标记进行了优化，将一次停顿进行的标记过程，分成了很多小步。每执行完一小步就让应用逻辑执行一会儿，这样交替多次后完成标记。

长时间的`GC`，会导致应用暂停和无响应，将会导致糟糕的用户体验。从2011年起，v8就将「全暂停」标记换成了增量标记。改进后的标记方式，最大停顿时间减少到原来的1/6。

## v8引擎垃圾回收策略——==分代回收==

- 采用==分代回收==的思想；
- 内存分为新生代、老生代；

针对不同对象采用不同算法：
（1）新生代：对象的存活时间较短。新生对象或只经过一次垃圾回收的对象。
（2）老生代：对象存活时间较长。经历过一次或多次垃圾回收的对象。

`V8`堆的空间等于新生代空间加上老生代空间。==且针对不同的操作系统对空间做了内存的限制==。（垃圾回收策略限制了浏览器使用的内存）



![img](https://pic2.zhimg.com/80/v2-7571cc360b1f2d47186a822f1101032d_1440w.jpg)



针对浏览器来说，这样的内存是足够使用的。限制内存的原因：

针对浏览器的GC机制，经过不断的测试，如果内存再设置大一点，`GC`回收的时间就会达到用户的感知，会造成感知上的卡顿。



### 回收新生代对象

回收新生代对象主要采用**复制算法**（`Scavenge 算法`）加标记整理算法。而`Scavenge 算法`的具体实现，主要采用了`Cheney算法`。



![img](https://pic1.zhimg.com/80/v2-1256d39574c7ec06b36582037f9b1014_1440w.jpg)



`Cheney算法`将内存分为两个等大空间，使用空间为`From`，空闲空间为`To`。

检查`From`空间内的存活对象，若对象存活，检查对象是否符合晋升条件，若符合条件则晋升到老生代，否则将对象从 `From` 空间复制到 `To` 空间。若对象不存活，则释放不存活对象的空间。完成复制后，将 `From` 空间与 `To` 空间进行角色翻转。

### 对象晋升机制

一轮`GC`还存活的新生代需要晋升。
当对象从`From` 空间复制到 `To` 空间时，若 `To` 空间使用超过 25%，则对象直接晋升到老生代中。设置为25%的比例的原因是，当完成 `Scavenge` 回收后，`To` 空间将翻转成`From` 空间，继续进行对象内存的分配。若占比过大，将影响后续内存分配。

### 回收老生代对象

回收老生代对象主要采用**标记清除**、**标记整理**、**增量标记**算法，主要使用**标记清除**算法，只有在内存分配不足时，采用**标记整理**算法。

1. 首先使用**标记清除**完成垃圾空间的**回收**；
2. 采用**标记整理**进行**空间优化**；
3. 采用**增量标记**进行**效率优化**；

### 新生代和老生代回收对比

新生代由于占用空间比较少，采用空间换时间机制。
老生代区域空间比较大，不太适合大量的复制算法和标记整理，所以最常用的是**标记清除**算法，为了就是让全停顿的时间尽量减少。



### 总结

1. 引用计数的方式，循环引用不太友好。
2. 标记清除：全停顿时间过长，空间碎片化
3. 标记整理：优化碎片空间
4. 增量标记：优化标记清除的效率，交替执行

## 性能优化

1. 避免使用全局变量

- 全局变量会挂载在window下；
- 全局变量至少有一个引用计数；
- 全局变量存活更久，持续占用内存；
- 在明确数据作用域的情况下，尽量使用局部变量；

2. 减少判断层级。函数调用亦是如此

```text
function doSomething(part, chapter) {
    const parts = ['ES2016', '工程化', 'Vue', 'React', 'Node']

    if (part) {
        if (parts.includes(part)) {
            console.log('属于当前课程')
            if (chapter > 5) {
                console.log('您需要提供 VIP 身份')
            }
        }
    } else {
        console.log('请确认模块信息')
    }
}

doSomething('Vue', 6)

// 减少判断层级
function doSomething(part, chapter) {
    const parts = ['ES2016', '工程化', 'Vue', 'React', 'Node']

    if (!part) {
        console.log('请确认模块信息')
        return
    }

    if (!parts.includes(part)) return
    console.log('属于当前课程')

    if (chapter > 5) {
        console.log('您需要提供 VIP 身份')
    }
}

doSomething('Vue', 6)
```

3. 减少数据读取次数
   对于频繁使用的数据，我们要对数据进行缓存。

4. 减少循环体中的活动。循环执行，相当于执行多次

   ```js
   var test = () => {
       var i
       var arr = ['Hello World!', 25, '岂曰无衣，与子同袍']
       for(i = 0; i < arr.length; i++) {
           console.log(arr[i])
       }
   }
   
   // 优化后，将arr.length单独提出，防止每次循环都获取一次
   var test = () => {
       var i
       var arr = ['Hello World!', 25, '岂曰无衣，与子同袍']
       var len = arr.length
       for(i = 0; i < len; i++) {
           console.log(arr[i])
       }
   }
   ```

5. 事件绑定优化。多个子元素的事件，不如用外层元素一个事件代理

   ```html
   <ul class="ul">
       <li>Hello World!</li>
       <li>25</li>
       <li>岂曰无衣，与子同袍</li>
   </ul>
   
   <script>
       var list = document.querySelectorAll('li')
       function showTxt(ev) {
           console.log(ev.target.innerHTML)
       }
   
       for (item of list) {
           item.onclick = showTxt
       }
   
       // 优化后
       function showTxt(ev) {
           var target = ev.target
           if (target.nodeName.toLowerCase() === 'li') {
               console.log(ev.target.innerHTML)
           }
       }
   
       var ul = document.querySelector('.ul')
       ul.addEventListener('click', showTxt)
   </script>
   ```

6. 避开闭包陷阱。函数内的变量如果在执行完函数之后不再使用，需要清零。

   ```html
   <button class="btn">点击</button>
   
   <script>
       function foo() {
           let el = document.querySelector('.btn')
           el.onclick = function() {
               console.log(el.className)
           }
       }
       foo()
   
       // 优化后
       function foo1() {
           let el = document.querySelector('.btn')
           el.onclick = function() {
               console.log(el.className)
           }
           el = null // 将el置为 null 防止闭包中的引用使得不能被回收
       }
       foo1()
   </script>
   ```

   



# iterator

* https://mp.weixin.qq.com/s/JmXtccxNQSE3kzVQPlK8tQ

## 迭代器

* ES6 中的**迭代器**使惰性求值和创建用户定义的数据序列成为可能。**迭代**是一种遍历数据的机制。迭代器是用于遍历数据结构元素（称为`Iterable`）的指针，用于产生值序列的指针。

  **迭代器**是一个可以被迭代的对象。它抽象了数据容器，使其行为类似于可迭代对象。

* 机制在 js 中内置

## 可迭代对象

可迭代对象是希望其元素可被公众访问的数据结构。JS 中的很多对象都是可迭代的，它们可能不是很好的察觉，但是如果仔细检查，就会发现迭代的特征：

- new Map([iterable])
- new WeakMap([iterable])
- new Set([iterable])
- new WeakSet([iterable])
- Promise.all([iterable])
- Promise.race([iterable])
- Array.from([iterable])

还有需要一个可迭代的对象，否则，它将抛出一个类型错误，例如：

- `for ... of`
- `...` (展开操作符)`const [a, b, ..] = iterable` (解构赋值)
- `yield*` (生成器)

JavaScript中已有许多内置的可迭代项：

`String`,`Array`,`TypedArray`,`Map`,`Set`。

## 迭代协议

可迭代对象 <--迭代协议--> 迭代器（js 内置的机制）

```tsx
interface Iterable {
  [Symbol.iterator]() : Iterator;
}
```

* 可迭代对象一定有一个 `Symbol.iterator`方法，执行之后返回一个迭代器，迭代器有 next 方法

## 迭代器协议

迭代器协议定义了产生值序列的标准方法。

为了使对象成为迭代器，它必须实现`next()`方法。迭代器可以实现`return()`方法，我们将在本文后面讨论这个问题。

## 可迭代对象和迭代器关系

![图片](https://mmbiz.qpic.cn/mmbiz_png/LDPLltmNy55DHiaBACSeIImeY7l8AAoPTbon2jDnIJ6bGRlCx6BtbSODao3j4NCABesmJPjWibrMUiaF4USCn1Xng/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## 总结

1. 执行 `Symbol.iterator` 就会返回一个==遍历器对象==。该对象的根本特征就是具有`next`方法。每次调用`next`方法，都会返回一个代表当前成员的信息对象，具有`value`和`done`两个属性。

   `Symbol.iterator` 相当于一个遍历器生成函数

2. 主要是统一接口，方便 for of 方法遍历。for of 方法遍历的是值，不是 key

3. 对象（Object）之所以没有默认部署 Iterator 接口，是因为对象的哪个属性先遍历，哪个属性后遍历是不确定的，需要开发者手动指定。本质上，遍历器是一种线性处理，对于任何非线性的数据结构，部署遍历器接口，就等于部署一种线性转换。不过，严格地说，对象部署遍历器接口并不是很必要，因为这时对象实际上被当作 Map 结构使用，ES5 没有 Map 结构，而 ES6 原生提供了。

## 遍历器对象的 return()，throw()

遍历器对象除了具有`next()`方法，还可以具有`return()`方法和`throw()`方法。如果你自己写遍历器对象生成函数，那么`next()`方法是必须部署的，`return()`方法和`throw()`方法是否部署是可选的。

`return()`方法的使用场合是，如果`for...of`循环提前退出（通常是因为出错，或者有`break`语句），就会调用`return()`方法。如果一个对象在完成遍历前，需要清理或释放资源，就可以部署`return()`方法。

# for in 和 in 

* in 能够判定手动添加到原型对象上的键

`for...in`循环有几个==缺点==。

- 数组的键名是数字，但是`for...in`循环是以字符串作为键名“0”、“1”、“2”等等。
- `for...in`循环不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键。
- 某些情况下，`for...in`循环会以任意顺序遍历键名。

总之，`for...in`循环主要是为遍历对象而设计的，不适用于遍历数组。

## 与 hasOwnProperty 的对比 ==（遍历对象相关）==

* hasOwnProperty 查找的是对象本身的属性，不包括原型链上的。不论 对象的 key 是否可枚举
* for in ==包括原型链上的==，查找规则和 [Object.assign](./## 7. Object.assign) 一样。
* in  能够识别原型链上的，并且包括本身不可枚举的值
* Object.assign  能够复制原型链上的数据
* Object.keys 只能够获取到对象本身的属性

# generator

* generator 函数赋值之后不会立即执行，只有在调用 next 方法的时候才会执行

  ```js
  function* f() {
    console.log('执行了！')
  }
  
  var generator = f();
  
  setTimeout(function () {
    generator.next()
  }, 2000);
  ```

* yield 紧跟着的函数一定是一个 generator 函数，否则报错

* `yield`表达式如果用在另一个表达式之中，必须放在圆括号里面。

  ```javascript
  function* demo() {
    console.log('Hello' + yield); // SyntaxError
    console.log('Hello' + yield 123); // SyntaxError
  
    console.log('Hello' + (yield)); // OK
    console.log('Hello' + (yield 123)); // OK
  }
  ```

* `yield`表达式用作函数参数或放在赋值表达式的右边，可以不加括号。

  ```javascript
  function* demo() {
    foo(yield 'a', yield 'b'); // OK
    let input = yield; // OK
  }
  ```

* 每次调用 next 都只执行到 yield 结束。

## Symbol.iterator

* generator 函数执行之后返回的值（遍历器对象）本身也是有 Symbol.iterator 属性的，Symbol.iterator方法执行之后返回自身

  即执行完函数之后，立马能用 [...] 方式遍历

## lazy evaluation

* 即执行函数并不会执行所有的语句，会等状态发生变化之后，才会去求值

## Generator.prototype.throw()

* 即便是想要执行 throw 方法，也要先执行 next 方法，让 generator 方法先执行
* 如果 Generator 函数内部没有部署`try...catch`代码块，那么`throw`方法抛出的错误，将被外部`try...catch`代码块捕获。
* `throw`方法被捕获以后，会附带执行下一条`yield`表达式。也就是说，会附带执行一次`next`方法。



# string

* js 试用 unicode 码表示字符

* Unicode 是「字符集」

  UTF-8 是「编码规则」

> 最小存储的单位是比特(Bit)，代表一位二进制 `0或1`，当有8个 Bit 的时候 譬如:`00001001`
> 就是常说的
> 1字节(Byte) = 8位二进制 = 2位的十六进制
>
> https://zhuanlan.zhihu.com/p/386511092

1. https://www.zhihu.com/question/23374078
2. http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html?20110621174302



# js 并发模型与事件循环

* https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop

![Stack, heap, queue](https://mdn.mozillademos.org/files/17124/The_Javascript_Runtime_Environment_Example.svg)

存储示意图

JavaScript有一个基于**事件循环**的并发模型，事件循环负责执行代码、收集和处理事件以及执行队列中的子任务。这个模型与其它语言中的模型截然不同，比如 C 和 Java。

## [堆](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop#堆)

对象被分配在堆中，堆是一个用来表示一大块（通常是非结构化的）内存区域的计算机术语。

## [队列](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop#队列)

一个 JavaScript ==运行时==包含了一个待处理消息的消息队列。每一个消息都关联着一个用以处理这个消息的回调函数。

在 [事件循环](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop#事件循环) 期间的某个时刻，运行时会从最先进入队列的消息开始处理队列中的消息。被处理的消息会被移出队列，并作为输入参数来调用与之关联的函数。==正如前面所提到的，调用一个函数总是会为其创造一个新的栈帧。==

* 调用函数创建 栈帧

函数的处理会一直进行到执行栈再次为空为止；然后事件循环将会处理队列中的下一个消息（如果还有的话）。

## [事件循环](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop#事件循环)

之所以称之为 **事件循环**，是因为它经常按照类似如下的方式来被实现：

```
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```

Copy to Clipboard

`queue.waitForMessage()` 会同步地等待消息到达(如果当前没有任何消息等待被处理)。



# 浏览器相关

## 浏览器线程和阻塞

https://segmentfault.com/a/1190000006143086

常见问题：

* js 引擎和渲染引擎是互斥的



# indexedDB 

* https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API
* Todo



# ==类型判断总结==

## instanceof

* 缺点：Object 在一切引用类型的原型链上，可能无法区分 Object 和 Array 等类型，要从小到达过滤



## .prototype.isPrototypeOf

* 类似于上述操作的反向操作，缺点同上



## Object.prototype.toString

* 缺点：只能判断 js 原有的类型，不能判断自定义类型

  ```js
  function A(){}
  
  let aa = new A()
  
  aa.toString()
  "[object Object]"
  A.toString()
  "function A(){}"
  
  ```

  

# 编码方式： utf-8 和 Unicode

unicode是信源编码，对字符集数字化;
utf8是信道编码，为更好的存储和传输。