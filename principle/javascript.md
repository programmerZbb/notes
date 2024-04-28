# Object 补充

## 1. seal 密封

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/seal

通常，一个对象是[可扩展的](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/isExtensible)（可以添加新的属性）。密封一个对象会让这个对象变的不能添加新属性，且所有已有属性会变的不可配置。属性不可配置的效果就是属性变的==不可删除==，以及一个数据属性不能被重新定义成为访问器属性，或者反之。但属性的值仍然可以修改。尝试删除一个密封对象的属性或者将某个密封对象的属性从数据属性转换成访问器属性，结果会静默失败或抛出[`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError)org/zh-CN/dnocs/Web/JavaScript/Reference/Strict_mode) 中最常见的，但不唯一）。

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


### 数据描述符

* value、writable、configurable、enumerable

### 存取描述符

* get、set、configurable、enumerable



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



## 获取属性描述符——Object.getOwnPropertyDescriptor

* 使用 Object.getOwnPropertyDescriptor 能够获取某个属性的属性描述符。



## Object.prototype.valueOf——数值计算的场景

JavaScript 调用 `valueOf` 方法将对象转换为原始值。原始值也就是能参与计算的值。你很少需要自己调用 `valueOf` 方法；==当遇到要预期的原始值的对象时==，JavaScript 会==自动调用==它。

默认情况下，`valueOf` 方法由 [`Object`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object) 后面的每个对象继承。 每个内置的核心对象都会覆盖此方法以返回适当的值。如果对象没有原始值，则 `valueOf` ==将返回对象本身==。(所有的 valueof 都继承自这个方法，只有这个部署了 valueof)

> // 返回本身
>
> let o = {}

### 面试题——函数计算的场景

* 是一个函数，返回这个对象参与计算时的值

* 如果是一个函数，在 valueOf 没有重写的情况下，默认会调用 toString() 方法进行计算。——==计算的场景！！！==

   valueOf > toString

## Object.prototype.toString()——进行文本表示的时候

**`toString()`** 方法返回一个表示该对象的字符串。

每个对象都有一个 `toString()` 方法，当该对象被表示为一个文本值时，或者一个对象以预期的字符串方式引用时自动调用。

* 如果没部署，则调用 Object.prototype.toString，返回一个 [object *type*] 字符串

比如：

```typescript
let obj = {
	test: 'aaa'
}
`${obj}` // 文本表示的场景，会调用 toString 方法
```

## Object.setPropertyOf

* 手动修改一个对象的原型

## Object.getPropertyOf

* 获取原型对象

## 内置属性

用[[]]包裹的属性是内置属性，只对引擎（语言实现平台）可见，JS是枚举不出来的。



# JSON

## JSON.parse

* 这个方法是同步的，可能引起性能问题

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
      console.log(k); // 输出当前的属性名，从而得知遍历顺序是从内向外的，先找到某一层的最后一个
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

## json 安全字符串

参考：https://zhuanlan.zhihu.com/p/29958439

推荐替换掉 `\u2028` 和 `\u2029` 字符串，否则在某些浏览器中会被当做终止符 而出现JSON解析出错！



# 数组

## 1. 扩展运算符和 concat 方法比较

* 感觉 数组的 concat 方法更强一些，参数可以是数组或者单个元素（一维数组可以直接拍平）。

  扩展运算符只能传递数组。



# 类型总结

基础类型：string、number、boolean、undefined、null、symbol、BigInt

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



# 模块

## import

### 元信息

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/import.meta

关键字 `import.meta`，注意是一个关键字，并不是一个对象的属性。可以作为代码组织时，根据query进行定制，做为 location.href 和 nodejs process.env 的补充。

* 试想一种情况：需要再引入js文件并执行里面代码的时候添加参数，那么 import.meta 就是一种非常好的方式。

* 获取绝对路径：import.meta 返回的是该模块的绝对路径（网络路径），那么就可以根据这个路径获得静态资源的路径。

  参考：https://cn.vitejs.dev/guide/assets.html#new-url-url-import-meta-url



# Reflect

## 简介

`Reflect`对象与`Proxy`对象一样，也是 ES6 为了操作对象而提供的新 API。`Reflect`对象的设计目的有这样几个。

1. 将Object对象上一些明显语言内部的方法（比如`Object.defineProperty`），放到`Reflect`对象上。现阶段，某些方法同时在`Object`和`Reflect`对象上部署，未来的新方法将只部署在`Reflect`对象上。也就是说，从`Reflect`对象上可以拿到语言内部的方法。

   * 主要是一些 Object 静态的方法

2. 修改了一些 Object 方法的返回结果，使返回更合理。比如说 Object.definePropety 的configurable设置为 false，则不能继续修改它的属性描述符，Object调用此方法可能抛出错误。Reflect 不会抛出错误，而是返回 Boolean 值

   ```typescript
   // 老写法
   try {
     Object.defineProperty(target, property, attributes);
     // success
   } catch (e) {
     // failure
   }
   
   // 新写法
   if (Reflect.defineProperty(target, property, attributes)) {
     // success
   } else {
     // failure
   }
   ```

3. 修改了一些原来命令式的语句，变成函数式的。比如`name in obj`和`delete obj[name]`，而`Reflect.has(obj, name)`和`Reflect.deleteProperty(obj, name)`让它们变成了函数行为。

4. `Reflect`对象的方法与`Proxy`对象的方法一一对应，只要是`Proxy`对象的方法，就能在`Reflect`对象上找到对应的方法。这就让`Proxy`对象可以方便地调用对应的`Reflect`方法，完成默认行为，作为修改行为的基础。也就是说，不管`Proxy`怎么修改默认行为，你总可以在`Reflect`上获取默认行为。

   * 也就是 proxy 定义 handle 的方法，都能用 Reflect 调用
   * ==这proxy一一对应这个才是最重要的！==
   * 防止重复调用这个方法造成循环调用。

## 总结

* 遇到 proxy 或者 Object 的静态方法，可以想到使用 Reflect 来解决，一定有对应的（有一小部分改了名称）



# api

## encodeURI

> **`encodeURI()`** 函数通过将特定字符的每个实例替换为一个、两个、三或四转义序列来对统一资源标识符 (URI) 进行编码 (该字符的 UTF-8 编码仅为四转义序列) 由两个 "代理" 字符组成)。

> UTF-8 (UCS Transformation Format 8) 是万维网上最常用的[字符编码](https://developer.mozilla.org/zh-CN/docs/Glossary/character_encoding)。每个字符由 1 到 4 个字节表示。UTF-8 与 [ASCII](https://developer.mozilla.org/zh-CN/docs/Glossary/ASCII) 向后兼容，可以表示任何标准的 Unicode 字符。

* 假定一个 URI 是完整的 URI，那么无需对那些保留的并且在 URI 中有特殊意思的字符进行编码。

在url中有特殊含义的字符将不会被编码: 比如 & # / 等

`encodeURI` 会替换所有的字符，但不包括以下字符，即使它们具有适当的 UTF-8 转义序列：

| 类型         | 包含                                          |
| :----------- | :-------------------------------------------- |
| 保留字符     | `;` `,` `/` `?` `:` `@` `&` `=` `+` `$`       |
| 非转义的字符 | 字母 数字 `-` `_` `.` `!` `~` `*` `'` `(` `)` |
| 数字符号     | `#`                                           |

> 请注意，`encodeURI` 自身*无法*产生能适用于 HTTP GET 或 POST 请求的 URI，例如对于 XMLHTTPRequests，因为 "&", "+", 和 "=" 不会被编码，然而在 GET 和 POST 请求中它们是特殊字符。然而[`encodeURIComponent`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent)这个方法会对这些字符编码。

### 为什么要转义呢?

因为你请求参数中的特殊字符可能和http中的关键字重复，所以需要转义！  encodeURI 不能够生产出 http 请求时候的 uri

### 转义的对象

* 转义的对象应该是你传递的参数。一般情况下，不能直接转义整个 url，要不然得不到最终的结果！！！

### 浏览器默认采用的转义方式

* 就是 EncodeURI

## encodeURIComponent

> **`encodeURIComponent()`** 函数通过将一个，两个，三个或四个表示字符的 UTF-8 编码的转义序列替换某些字符的每个实例来编码 [URI](https://developer.mozilla.org/zh-CN/docs/Glossary/URI)（对于由两个“代理”字符组成的字符而言，将仅是四个转义序列）。

```tex
不转义的字符：
    A-Z a-z 0-9 - _ . ! ~ * ' ( )
```

### 任何用户输入的==作为 URI 部分的内容（也就是输入的参数等）==都需要转义

为了避免服务器收到不可预知的请求，对任何用户输入的作为 URI 部分的内容你都需要用 encodeURIComponent 进行转义。==这也是为什么浏览器会对地址栏输入的字符串默认进行转义（只对参数进行转义！！！）==

* 主要说的是参数的转义（有可能包括路由等）

* 因此我们在主动进行请求的时候也要进行转义，除非使用的全是英文



* 与 encodeURI 的区别

  encodeURI()不会对本身属于URI的[特殊字符](https://www.zhihu.com/search?q=特殊字符&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"135882275"})进行编码，比如说 = # 等，因此不建议直接对参数数据进行转义，如果是一个完整的 url 可以试试；

  encodeURIComponent()则会对他发现的任何[非标准字符](https://www.zhihu.com/search?q=非标准字符&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"135882275"})（字母或数字为标准字符）进行编码，它针对的是对url中的参数进行细致化的转义，如果直接对完整的url进行转义，那么就会把原有的 `= #` 等有意义的字符给转义掉。

### decodeURIComponent

* 能够解析 encodeURI 编码的uri。
* 一般情况下都是用来解析uri中 编码的参数



> 对于 [`application/x-www-form-urlencoded`](https://www.whatwg.org/specs/web-apps/current-work/multipage/association-of-controls-and-forms.html#application/x-www-form-urlencoded-encoding-algorithm) (POST) 这种数据方式，空格需要被替换成 '+'，所以通常使用 `encodeURIComponent` 的时候还会把 "%20" 替换为 "+"。

## uri

### http 中的 uri

浏览器会自动对请求中的 uri 中用户输入的部分（非http部分）进行编码，使用 decodeURIComponent 可以进行解码



## utf-8

> UTF-8 (UCS Transformation Format 8) 是万维网上最常用的[字符编码](https://developer.mozilla.org/zh-CN/docs/Glossary/character_encoding)。每个字符由 1 到 4 个字节表示。UTF-8 与 [ASCII](https://developer.mozilla.org/zh-CN/docs/Glossary/ASCII) 向后兼容，可以表示任何标准的 Unicode 字符。





# ==原则问题==

* 方法一遍挂载到 prototype 上。这个一定要事先搞清楚



# JavaScript 变量、作用域和内存

## 1. 变量

## 小结

js变量可以保存两种类型值：原始值和引用值。那也就预示着变量的内存只有两种类型：栈、堆

原始值可能是以下 6 种原始数据类型之 一:Undefined、Null、Boolean、Number、String 和 Symbol。

原始值和引用值特点如下：

* 原始值大小固定，因此存在栈内存上。

* 从一个变量到另一个变量复制原始值会创建该值的第二个副本。

* 引用类型是对象，存储在堆内存上

* 包含引用值的变量实际上只包含指向相应对象的一个指针，而不是对象本身。

* 从一个变量到另一个变量复制引用值只会复制指针，因此结果是两个变量都指向同一个对象

* 一般使用 typeof 确定原始值的类型，使用 instanceof 确认引用类型

  > typeof 确定原始类型的数据类型，如果确定引用值的类型会都为 object
  >
  > instanceof 确定引用值需要改类型的构造函数在其原型链上



# JavaScript 内存

## js 中的内存空间

https://juejin.cn/post/6868166748709847053#heading-7

JS的内存空间分为栈(stack)、堆(heap)、池(一般也会归类为栈中)。

其中栈存放变量，堆存放复杂对象，池存放常量，所以也叫常量池。

![Stack, heap, queue](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Event_loop/the_javascript_runtime_environment_example.svg)

### const

* const 关键字保存的是该变量的地址。对于基础数据类型，保存的就是该值；对于引用数据类型，保存着就是引用类型的地址。

## 堆内存

> 编程语言中的堆内存是一种用于动态分配和管理内存的内存区域。堆内存通常用于存储动态分配的数据结构，例如对象、数组和其他复杂数据类型。

* 一种用于动态分配和管理内存的内存区域，动态的，也就是能够根据对象的创建进行动态申请。

* 我们平时所说的 GC 也就是对 堆内存 进行垃圾回收。

### 与栈内存

> 堆内存通常是在运行时动态分配的，它的大小可以根据程序的需要动态扩展或收缩。由于堆内存的动态性和灵活性，它通常用于存储在程序运行时无法确定大小和生命周期的数据，而栈内存一般用于存储具有确定生命周期和大小的数据。

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



==接下来是集中垃圾回收方式！并不是js引擎全部采用！！！==

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

> 对象是否不在需要 -> 引用计数，简化为对象是否可以获得。

这个算法假定设置一个叫做根（root）的对象（在Javascript里，根是全局对象）。垃圾回收器将定期从根开始，找所有从根开始引用的对象，然后找这些对象引用的对象……从根开始，垃圾回收器将找到所有可以获得的对象和收集所有不能获得的对象。

> 从根元素开始查找这些对象，因此不推荐写层级深的对象。

* 这也是为啥不让写全局对象的原因！

从2012年起，所有现代浏览器都使用了标记-清除垃圾回收算法。所有对JavaScript垃圾回收算法的改进都是基于标记-清除算法的改进，并没有改进标记-清除算法本身和它对“对象是否不再需要”的简化定义。

### 限制：那些无法从根对象查询到的对象都将被清除-这也就是不写全局对象的原因

尽管这是一个限制，但实践中我们很少会碰到类似的情况，所以开发者不太会去关心垃圾回收机制。

### 扩展

https://zhuanlan.zhihu.com/p/352323793

## JavaScript 内存管理

部分语言需要（例如`C`语言）需要手动去释放内存，但是会很麻烦，所以很多语言，例如`JAVA`都会提供自动的内存管理机制，称为“**垃圾回收机制**”，`JavaScript`语言中也提供了垃圾回收机制（`Garbage Collecation`），简称**GC机制**。

内存管理：

申请 -> 使用 -> 释放

### 函数调用栈

#### 栈帧

```js
function doSomething() {
    return doSomethingElse();
}
```

ES5中在执行 doSomethingElse 函数的时候会新增一个栈帧，doSomething对应的栈帧会被保留在内存中，当存在循环调用时就容易出现程序问题，比如栈溢出等不可预期的错误。

> https://juejin.cn/post/6972036399503507487

#### es6 的 tail call 尾调用优化

* https://www.ruanyifeng.com/blog/2015/04/tail-call.html
* https://juejin.cn/post/6972036399503507487

### 全停顿

在介绍垃圾回收算法之前，我们先了解一下「**全停顿**」。垃圾回收算法在执行前，需要将应用逻辑暂停，执行完垃圾回收后再执行应用逻辑，这种行为称为 「**全停顿**」（`Stop The World`）。例如，如果一次GC需要50ms，应用逻辑就会暂停50ms。

全停顿的目的，是为了解决应用逻辑与垃圾回收器看到的情况不一致的问题。

* 类似于数据库的加锁

举个例子，在自助餐厅吃饭，高高兴兴地取完食物回来时，结果发现自己餐具被服务员收走了。这里，服务员好比垃圾回收器，餐具就像是分配的对象，我们就是应用逻辑。在我们看来，只是将餐具临时放在桌上，但是服务员看来觉得你已经不需要使用了，因此就收走了。你与服务员对于同一个事物看到的情况是不一致，导致服务员做了与我们不期望的事情。因此，为避免应用逻辑与垃圾回收器看到的情况不一致，垃圾回收算法在执行时，需要停止应用逻辑。

### ==`JavaScript`中的垃圾回收==

JavaScript中会被判定为垃圾的情形如下：

- 对象不再被引用；
- 对象不能从根上访问到；

### `GC`算法

常见的`GC`算法如下：

- 引用计数
- 标记清除
- 标记整理
- 分代回收

js 引擎并不一定使用如上一种，而是采用标记-清除算法。

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

![img](https://pic4.zhimg.com/80/v2-3d2c9ac05ca18333f7b1f136a4976913_1440w.webp)

上图我们可以看到，红色区域是一个根对象，就是一个全局变量，会被标记；而蓝色区域就是没有被标记的对象，会被回收机制回收。这时就会出现一个问题，表面上蓝色区域被回收了三个空间，但是这三个空间是不连续的，当我们有一个需要三个空间的对象，那么我们刚刚被回收的空间是不能被分配的，这就是“空间碎片化”。

* 全局对象在执行完成之后，也不被回收，因为有引用（即浏览器占用的内存）。取决于 scope chain——全局上下文。
* 那么局部上下文已经被回收了

### 标记整理（Mark-Compact）

为了解决内存碎片化的问题，提高对内存的利用，引入了标记整理算法。

标记整理可以看做是标记清除的**增强**。标记阶段的操作和标记清除一致。

清除阶段会先执行整理，移动对象位置,将存活的对象移动到一边，然后再清理端边界外的内存。

标记整理的缺点是：移动对象位置，不会立即回收对象，回收的效率比较慢。

### 增量标记（Incremental Marking）

为了减少全停顿的时间，`V8`对标记进行了优化，将一次停顿进行的标记过程，分成了很多小步。每执行完一小步就让应用逻辑执行一会儿，这样交替多次后完成标记。

长时间的`GC`，会导致应用暂停和无响应，将会导致糟糕的用户体验。从2011年起，v8就将「全暂停」标记换成了增量标记。改进后的标记方式，最大停顿时间减少到原来的1/6。

## v8引擎垃圾回收策略——==分代回收==

### 简介

> 由于不同的对象，其生存周期长短不一，一种垃圾回收算法不能完全应对所有情况，所以 v8 的垃圾回收策略采用了更高效的**分代式垃圾回收机制**。即：按对象的存储时间将内存的垃圾回收进行不同的分代，然后**分别对不同分代的内存使用更适合其特征的 GC 算法**。

==针对不同类型的对象，针对其生存周期长短，v8 采用了更高效的分代式垃圾回收机制。==

- 采用==分代回收==的思想；
- 内存分为新生代、老生代；

V8 将内存设置为了 2 个分代，V8 堆的整体大小就是这 2 个分代所用的内存之和，在启动时候可以通过下面的参数设置其大小：

- 新生代：存储的是存活时间较短的对象，存储在 V8 的小空间中（16M 或者 32M）。
  - `--max-new-space-size` 参数用来设置新生代空间的最大值，单位为 KB，在 64 位中占据 32M，32 位占据 16M。
- 老生代：存储的是长期活动的对象。
  - `--max-old-space-size` 参数用来设置老生代空间的最大值，单位为 MB，在 64 位中占据 1400M，32 位占据 700M。

V8 中主要使用的策略与算法有：分代回收、空间复制、标记清除、标记整理、标记增量。



针对不同对象采用不同算法：
（1）新生代：对象的存活时间较短。新生对象或只经过一次垃圾回收的对象。
（2）老生代：对象存活时间较长。经历过一次或多次垃圾回收的对象。

`V8`堆的空间等于新生代空间加上老生代空间。==且针对不同的操作系统对空间做了内存的限制==。（垃圾回收策略限制了浏览器使用的内存）



![img](https://pic2.zhimg.com/80/v2-7571cc360b1f2d47186a822f1101032d_1440w.jpg)



针对浏览器来说，这样的内存是足够使用的。限制内存的原因：

针对浏览器的GC机制，经过不断的测试，如果内存再设置大一点，`GC`回收的时间就会达到用户的感知，会造成感知上的卡顿。

> 这个是v8引擎的回收策略，和具体的算法没关系。先分为新生代和老生代，也就是 GC 算法中的分代回收。

### Chrome 何时触发垃圾回收

比如，根据 V8 团队 2016 年的一篇博文的说法:

“在一次完整的垃圾回收之后，V8 的堆增长策略会根据活跃对象的数量外加一些余量来确定何时再次垃 圾回收。”

### 回收新生代对象

回收新生代对象主要采用**复制算法**（`Scavenge 算法`）加标记整理算法。而`Scavenge 算法`的具体实现，主要采用了`Cheney算法`。



![img](https://pic1.zhimg.com/80/v2-1256d39574c7ec06b36582037f9b1014_1440w.jpg)



`Cheney算法`将内存分为两个等大空间，使用空间为`From`，空闲空间为`To`。

检查`From`空间内的存活对象，若对象存活，检查对象是否符合晋升条件，若符合条件则晋升到老生代，否则将对象从 `From` 空间复制到 `To` 空间。若对象不存活，则释放不存活对象的空间。完成复制后，将 `From` 空间与 `To` 空间进行角色翻转。

* 也就是新生代才会使用 from to 转换的方式
* from to 转换，并且伴随着晋升老生代
* 很像一个筛子反复的筛选

### 对象晋升机制

一轮`GC`还存活的新生代需要晋升。
当对象从`From` 空间复制到 `To` 空间时，若 `To` 空间使用超过 25%，则对象直接晋升到老生代中。设置为25%的比例的原因是，当完成 `Scavenge` 回收后，`To` 空间将翻转成`From` 空间，继续进行对象内存的分配。若占比过大，将影响后续内存分配。

### 回收老生代对象

回收老生代对象主要采用**标记清除**、**标记整理**、**增量标记**算法，主要使用**标记清除**算法，只有在内存分配不足时，采用**标记整理**算法。

1. 首先使用**标记清除**完成垃圾空间的**回收**；
2. 采用**标记整理**进行**空间优化**；
3. 采用**增量标记**进行**效率优化**；

### 新生代和老生代回收对比

新生代由于占用空间比较少，采用空间换时间机制，使用的标记整理算法。
老生代区域空间比较大，不太适合大量的复制算法和标记整理，所以最常用的是**标记清除**算法，为了就是让全停顿的时间尽量减少。

* 老生代主要是标记清除，内存不足的时候会使用标记整理算法。

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

```js
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


## 阅读参考

* https://juejin.cn/post/7173644980240515085?searchId=202307201505058E02B8C4D85F4A017C11



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

# JavaScript 上下文

## 1. 变量提升

* 函数内的变量只有执行的时候不存在才会报错。因此函数内使用变量不惧怕任何变量提升问题！

## 上下文

> JavaScript 代码运行时，会产生一个全局的上下文环境（context，又称运行环境），包含了当前所有的变量和对象。然后，执行函数（或块级代码）的时候，又会在当前上下文环境的上层，产生一个函数运行的上下文，变成当前（active）的上下文，由此形成一个上下文环境的堆栈（context stack）。
>
> 这个堆栈是“后进先出”的数据结构，最后产生的上下文环境首先执行完成，退出堆栈，然后再执行完成它下层的上下文，直至所有代码执行完成，堆栈清空
>
> ————https://es6.ruanyifeng.com/?search=%E4%B8%8A%E4%B8%8B%E6%96%87&x=0&y=0#docs/generator#Generator-%E4%B8%8E%E4%B8%8A%E4%B8%8B%E6%96%87



# js 并发模型与事件循环

* https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop

![Stack, heap, queue](https://mdn.mozillademos.org/files/17124/The_Javascript_Runtime_Environment_Example.svg)

存储示意图

JavaScript有一个基于**事件循环**的并发模型，事件循环负责执行代码、收集和处理事件以及执行队列中的子任务。这个模型与其它语言中的模型截然不同，比如 C 和 Java。

* 事件循环就是js的并发模型

## 栈

函数的调用形成一个若干帧组成的栈。

## [堆](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop#堆)

对象被分配在堆中，堆是一个用来表示一大块（通常是非结构化的）内存区域的计算机术语。

## [队列](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop#队列)

一个 JavaScript ==运行时==包含了一个待处理消息的消息队列。每一个消息都关联着一个用以处理这个消息的回调函数。

* ==这就是JavaScript事件循环的精髓——消息队列==

在 [事件循环](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop#事件循环) 期间的某个时刻，运行时会从最先进入队列的消息开始处理队列中的消息。被处理的消息会被移出队列，并作为输入参数来调用与之关联的函数。==正如前面所提到的，调用一个函数总是会为其创造一个新的栈帧。==

* 调用函数创建 栈帧

==函数的处理会一直进行到执行栈再次为空为止；然后事件循环将会处理队列中的下一个消息（如果还有的话）。==

* 程序执行完成的标志就是函数调用栈为空！

### 消息

* 消息就是宏任务，一个消息的执行就是一个事件循环。

## [事件循环](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop#事件循环)

之所以称之为 **事件循环**，是因为它经常按照类似如下的方式来被实现：

```
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```

Copy to Clipboard

`queue.waitForMessage()` 会同步地等待消息到达(如果当前没有任何消息等待被处理)。

### 执行至完成

> 每一个消息完整地执行后，其它消息才会被执行。这为程序的分析提供了一些优秀的特性，包括：当一个函数执行时，它不会被抢占，只有在它运行完毕之后才会去运行任何其他的代码，才能修改这个函数操作的数据。这与 C 语言不同，例如，如果函数在线程中运行，它可能在任何位置被终止，然后在另一个线程中运行其他代码。
>
> 这个模型的一个缺点在于当一个消息需要太长时间才能处理完毕时，Web 应用程序就无法处理与用户的交互，例如点击或滚动。为了缓解这个问题，浏览器一般会弹出一个“这个脚本运行时间过长”的对话框。一个良好的习惯是缩短单个消息处理时间，并在可能的情况下将一个消息裁剪成多个消息。

* 特性1：执行到完成，这样保证了JavaScript单线程的自洽性。但是会有一个缺点就是阻塞性。
* 给出方案就是将一个消息裁剪成多个消息（消息就是异步宏任务！）这也就是react正在做的

### 添加消息

* 在浏览器里，每当一个事件发生并且有一个事件监听器绑定在该事件上时，一个消息就会被添加进消息队列。

  添加事件

* setTimeout 等

  函数 [`setTimeout`](https://developer.mozilla.org/zh-CN/docs/Web/API/setTimeout) 接受两个参数：待加入队列的消息和一个时间值（可选，默认为 0）。这个时间值代表了消息被实际加入到队列的最小延迟时间。如果队列中没有其它消息并且栈为空，在这段延迟时间过去之后，消息会被马上处理。

### 零延迟

零延迟并不意味着回调会立即执行。需要等待队列里待处理的消息数量。

### 多个运行时互相通信

一个 web worker 或者一个跨域的 `iframe` 都有自己的栈、堆和消息队列。两个不同的运行时只能通过 [`postMessage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/postMessage) 方法进行通信。如果另一个运行时侦听 `message` 事件，则此方法会向该运行时添加消息。

* 通过postmessage 的方式进行通信

### 永不阻塞

* 理论上的永不阻塞

### 消息被处理的前提条件

* ==函数调用栈为空==，意味着上一个消息已经执行完成。

  * 调用栈没有情况的情况（dom.click的方式，冒泡的事件会作为调用栈）

    ```typescript
    // click 的优先级是高于 timeout 的
    
    const out = document.querySelector('.out')
    const inner = document.querySelector('.inner')
    
    function fn() {
        console.log('click')
    
        setTimeout(() => {
            console.log('timeout')
        }, 0);
    
        let ob = new MutationObserver(() => {
            console.log('mutate')
        })
        ob.observe(out, {
            attributes: true,
        })
    
        Promise.resolve().then(() => {
            console.log('promise')
        })
    }
    
    // 优先级测试
    out.onclick = fn
    inner.onclick = fn
    
    // 调用栈测试。这种方式调用栈不会清空
    inner.click()
    ```

* 没有其他的消息（至于调用栈为空，消息队列的上下文问题，是词法上下文决定的，并不是堆栈没有清理掉）

### 重要

* 事件循环是 JavaScript 的并发模型，这个是语言的特性。

## 宏队列

* 浏览器宏队列是一个有序集合？有优先级的概念？

==一定要注意：是执行设定完成的时刻才会加入到消息队列，而不是一开始就加入了消息队列！！！！！！！！！！！！==

### 优先级？？

* click > timer

```typescript
// click 的优先级是高于 timeout 的
setTimeout(() => {
    console.log(222)
}, 0)
document.documentElement.click()
```

```js
// click 的优先级是高于 timeout 的

const out = document.querySelector('.out')
const inner = document.querySelector('.inner')

function fn() {
    console.log('click')

    setTimeout(() => {
        console.log('timeout')
    }, 0);

    let ob = new MutationObserver(() => {
        console.log('mutate')
    })
    ob.observe(out, {
        attributes: true,
    })

    Promise.resolve().then(() => {
        console.log('promise')
    })
}

out.onclick = fn
inner.onclick = fn
```

#### DOM 事件和http 响应

* 在执行完成之后会立即加入到宏任务，不需要进入下次消息。也就预示着它们的优先级比较高。

### 调用栈没清空？

* 参照上面的例子

### http 等事件的宏任务

* 在拿到响应或者事件触发的时候，会立即加入到宏任务中（去排队吧，如果需要的话）

* settimeout 会在定时器到的时候加入到消息队列

### 啥叫排队

* 如果两个异步的消息在同事完成的时候才会出现排队的现象（比如两个相同的settimeout）

## queueMicrotask

* todo

执行顺序，和普通的 promise 回调执行顺序相同，主要看调用时序。

### 使用场景

1. 调整时序

```js
const messageQueue = [];

let sendMessage = message => {
  messageQueue.push(message);

  if (messageQueue.length === 1) {
    queueMicrotask(() => {
      const json = JSON.stringify(messageQueue);
      messageQueue.length = 0;
      fetch("url-of-receiver", json);
    });
  }
};
```

### messageChannel-消息通道

* 个人理解就是官方实现的 eventbus

* 阅读：https://zhuanlan.zhihu.com/p/432726048
  * https://zhuanlan.zhihu.com/p/37589777

> MessageChannel允许我们在不同的浏览上下文，比如window.open()打开的窗口或者iframe等之间建立通信管道，并通过两端的端口（`port1`和`port2`）发送消息。MessageChannel以`DOM Event`的形式发送消息，所以它属于异步的宏任务。

* messageChannel 以 DOM Event 的形式发送，因此属于宏任务！

* 上面这个简单的例子就是`MessageChannel`的大致使用过程了。概括来说就是使用`MessageChannel`这个构造函数开创建一个消息管道对象。这个对象实例有两个**只读**属性`port1`和`port2`。我们可以通过其`postMessage`来发送数据，`onmessage`来接收数据。

```text
const { port1, port2 } = new MessageChannel();
port1.onmessage = function (event) {
  console.log('收到来自port2的消息：', event.data); // 收到来自port2的消息： pong
};
port2.onmessage = function (event) {
  console.log('收到来自port1的消息：', event.data); // 收到来自port1的消息： ping
  port2.postMessage('pong');
};
port1.postMessage('ping');
```

`addEventListener`的写法也可以。

```text
const { port1, port2 } = new MessageChannel();
port1.addEventListener('message', function (event) {
  console.log('收到来自port2的消息：', event.data); // 收到来自port2的消息： pong
});
port1.start();
port2.addEventListener('message', function (event) {
  console.log('收到来自port1的消息：', event.data); // 收到来自port1的消息： ping
  port2.postMessage('pong');
});
port2.start();
port1.postMessage('ping');
```

>  注意，`addEventListener`之后要手动调用`start()`方法消息才能流动，因为初始化的时候是暂停的。`onmessage`已经隐式调用了`start()`方法。

我们把port1和port2统一叫做`MessagePort`。 ——概念！

MessagePort还有两个方法：`close`和`onmessageerror`。 

`close`方法能断开MessagePort的连接，之后两个断开之间将无法通信。建议通信结束后主动调用close方法以便资源回收。

消息不能反序列化时，会出现错误，这时可以用`onmessageerror`方法捕获。

#### node 中的 messageChannel

* node 中也能使用 messageChannel

### messageChannel 和 customEvent 使用区别



## settimeout、 requestAnimationFrame、 requestIdleCallback、messageChannel

* messageChennel 是宏任务！

`宏Task -> micro Task -> requestAnimationFrame -> 重拍、重绘 -> requestIdleCallback`

* messageChannel 和 settimeout 执行顺序和调用顺序相关。

## requestIdleCallback

* 这个是检测 CPU 是否处于空闲的

## requestAnimationFrame

* 下一次重绘之前执行

## 宏任务总结

* requestAnimationFrame、messageChannel 是宏任务

|                                | 浏览器 | nodejs |
| ------------------------------ | ------ | ------ |
| setTimeout                     | ✅      | ✅      |
| setInterval                    |        |        |
| I/O                            |        |        |
| requestAnimationFrame          |        |        |
| UI rendering(说的就是UI的渲染) |        |        |
| setTimediate                   |        |        |
| messageChannel                 | ✅      | ❌      |

* http响应和DOM事件会在本次立即插入宏任务（优先级高）

## 微任务

* mutationObserver、queueMicrotask

|                                                 | 浏览器 | nodejs |
| ----------------------------------------------- | ------ | ------ |
| Promise.then catch finally(不能是pending的情况) | ✅      | ✅      |
| queueMicrotask                                  | ✅      | ✅      |
| MutationObserver                                | ✅      | ❌      |
| process.nextTick                                | ❌      | ✅      |

## 多个script怎么算呢？

* 因为每个script都是网络获取的，所以每个script都是相当于一个异步宏任务。

  分析的时候只需要把其当成异步宏任务来分析即可。它在拿到响应之后会立即加入到宏任务中！！（和http响应一样的）

* 唯一不同：

  把它当成一个普通的宏任务也不太对，因为它会阻塞当前html后面的代码执行。

### 多个script顺序执行

如果多个script标签，因为加载和执行都会阻塞html渲染，因此可以保证他们的执行顺序。

### webpack 打包

* 正如上所述，webpack 采用 external 打包把 external js 放在最上面就能够保证js的顺序执行（下面的js可能需要依赖external js）

## script 是怎么阻塞渲染的呢？

> 解析阻塞的 Javascript 会导致浏览器必须加载并且执行脚本，之后才能继续解析。

* script标签内的js代码确实会阻塞页面渲染。比如一个script标签打上断点，它后面的html将不会被渲染出来
* 阻塞的是解析。加载和执行都能阻塞。

```typescript
console.log(222)

debugger
setTimeout(() => {
    console.log('222 timeout')
}, 5);
```

* 因为没有script拿到js代码之后会立即执行，因此在没有获取到js代码之前，后续的html代码不会被渲染。

  script 之前的代码不会手动影响，因此需要把script标签放到最后执行。

### 为什么是这样的呢？

`宏Task -> micro Task -> requestAnimationFrame -> 重拍、重绘 -> requestIdleCallback`

* 而js代码是script动态获取的，在没有加载到script标签的时候（也就是没有js代码的时候），html已经被渲染了一部分。

异步的宏任务并不能阻塞页面渲染（如上面的渲染流程），用如下代码可测试：

```typescript
console.log(222)

// debugger
setTimeout(() => {
    console.log('222 timeout')
    debugger
}, 5);
```

## script 加载会阻塞html解析

同样的如果js没有加载完成，是不能继续解析HTML的，因为js有可能继续往html中添加内容。

* 如下代码js加载被阻塞就不能继续解析HTML

代码：some-practice -> page-render-priciple

### defer

html内容加载完成之后才会执行，也就是 script 的加载和执行（延后执行），不会再阻塞页面的执行了。

### async

> 对于普通脚本，如果存在 `async` 属性，那么普通脚本会被并行请求，并尽快解析和执行。 对于[模块脚本](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Modules)，如果存在 `async` 属性，那么脚本及其所有依赖都会在延缓队列中执行，因此它们会被并行请求，并尽快解析和执行。该属性能够消除解析阻塞的 Javascript。解析阻塞的 Javascript 会导致浏览器必须加载并且执行脚本，之后才能继续解析。

https://developer.mozilla.org/zh-CN/docs/Games/Techniques/Async_scripts

* Async 页面渲染完成 + 其他普通的script 执行完成之后才会执行

参考：https://juejin.cn/post/7236383034559905849

## css 阻塞页面渲染

script 阻塞的是html解析，css阻塞页面渲染（没有样式）

### css 加载同样会阻塞 html 解析

* 看来页面上获取资源的标签都能阻塞HTML的解析
* 那也就是说静态资源的获取都会阻塞HTML的解析

## 通过 performance 工具查看浏览器渲染流程

* 通过 performance 录制能够看到，渲染流程大概是

`parseHtml -> evaluatescript(如果解析到script) -> 宏Task -> micro Task -> requestAnimationFrame -> 重拍、重绘 -> requestIdleCallback `



# 异步编程

## 函数保持同步或异步

* 如果一个函数需要返回值，涉及到返回异步的问题，建议都返回异步，而不是既能返回同步又能返回异步！
* 函数作为参数同理

因为涉及到 release zalog 问题

## **release Zalgo**

https://zhuanlan.zhihu.com/p/30630902

* 简单的就是说一个传入 callback 可能在同步调用，也可能在异步调用，这样是不合理的

```typescript
// 注: 这是一个相当乌托邦,且省略诸多内容的函数 
function login (callback) {
    // 当取得的账号变量name的值为空时， 立即调用函数，此时callback同步调用） 
    if(!name) {
        callback();
        return // name为空时在这里结束函数         
    }
    // 当取得的账号变量name的值不为空时， 在请求成功后调用函数（此时callback异步调用）
    request('post', name, callback)
}
```

> **的确，这种函数的编写是公认的需要杜绝的，在英语世界里， 这种可能同步也可能异步调用的回调以及包裹它的函数， 被称作是 “Zalgo” （一种都市传说中的魔鬼）， 而编写这种函数的行为， 被称作是"release Zalgo" (将Zalgo释放了出来)**

* 这种代码是非常可怕的，让我们的代码变得无法预测。函数的调用时间是不确定的，代码变得难以掌控

一个简单的例子：

例如：

```js
var a =1
zalgoFunction () {
  // 这里还有很多其他代码,使得a = 2可能被异步调用也可能被同步调用
    [  a = 2  ]
  }
console.log(a)
```



结果会输出什么呢？ **如果zalgoFunction是同步的， 那么a 显然等于2， 但如果 zalgoFunction是异步的，那么 a显然等于1。于是， 我们陷入了无法判断调用影响的窘境。**



## 竞态问题

* 就是异步任务不按照书写顺序执行，原因：异步任务顺序未知
* 参考：https://juejin.cn/post/6844903863749705741#heading-4

例子：

输入框频繁输入，返回数据对不上等。

* 解决：
  * 避免多次请求
  * 请求前后状态对应
* 解决竞态问题的一种方案

```typescript
// 可以封装一个defer工具，实现同步调用，可以直接取消
class Defer<T = void> {
    protected _promise: Promise<T>;
    protected _res: (value: T | PromiseLike<T>) => void;
    protected _rej: (reason?: any) => void;

    public constructor() {
        // promise的类型可以这样推出来
        this._promise = new Promise((res, rej) => {
            this._res = res;
            this._rej = rej;
        });
    }

    public resolve(value: T): void {
        this._res(value);
    }

    public reject(reason: unknown): void {
        this._rej(reason);
    }

    public get promise(): Promise<T> {
        return this._promise;
    }
}

// 解决竞态问题
let defer: Defer<number>;
setTimeout(() => {
    if (defer != null) {
        defer.reject('');
    }
    defer = new Defer<number>();
    defer.resolve(1);
}, 3000);
setTimeout(() => {
    defer = new Defer<number>();
    defer.resolve(2);
}, 1000);
```

## promise cancel

* 如上竞态问题，promise 无法取消，是目前 promise 存在的一个问题

  更多例子查看：https://segmentfault.com/q/1010000009781257

> 比如点赞，取消点赞操作
> 如果是使用promise当你误点赞了你想点取消赞时， 你就必须等点赞操作结束才能操作
> 所以有了加强版的promise → observable

> winter的回答
>
> 其实还好，多数情况下，我们要取消的不是Promise，而是生成Promise的那个任务。
>
> 而且这个任务本身的取消，有时还附带清理现场的需求，比方说，我await了一个存储文件的任务，那么取消这个存储文件任务，意味着中断文件写入，把已经写入的一些信息回滚等等操作。单纯从Promise的角度取消，其实只是取消了Promise后的回调，原本的任务还是执行完成了，若真设计出取消功能，很容易造成误判。
>
> 此外，当真正需要取消时，我们完全可以在await之后或者then的回调中加入控制逻辑，这能够保证代码逻辑紧凑，否则，我们调试时可能面临“Promise被取消时到处找取消的代码”这样的窘境

* 讨论参考：https://www.zhihu.com/question/495412354/answer/2244899074

## generator



## async

* async 是 generator实现的语法糖，generator能做的，async也能做

## 基础

async 肯定返回一个 promise，promise 的值由内部中断的语句赋值

* 有return 就是该return 的值

* 没有return就是 return undefined

* 如果有 promise.reject 或者 throw error(也就是error对象) 的出现（也视为中断），那就是该错误值

  ==一起函数的返回值都是看其中断值！！！==

await 可以跟着一个 promise 实例，返回的值就是其中断值。如果是一个普通常量，就是该常量（常量没有中断值，就是其本身）

==await返回就是一个值，不是一个promise。而且是一个成功的值，不进行错误处理==

* await 意味着后面会有一个值出现，它等到直到这个值出现



### 并发异步操作——promise.all

* 因为 generator 可以处理并发异步操作，async也可以实现

* 对象

  ```typescript
  let fn1 = async function () {
      let res = await {
      1: Promise.resolve(1),
      2: Promise.resolve(2),
    };
      console.log(res)
  }
  ```

* 数组

  ```typescript
  let fn1 = async function () {
      let res = await [
          new Promise((res, rej) => {
              setTimeout(() => {
                  res(11)
              },2000)
          }),
          Promise.reject(2),
          new Promise((res, rej) => {
              setTimeout(() => {
                  res(22)
              },1000)
          })
      ]
      console.log(res)
  }
  ```

并发处理和 Promise.all 一模一样，响应的数据和数组的 index 对应。等到所有并发处理完成，才会进行下一步。



# 编程范式

## 1. FRP——函数响应式编程

* functional-reactive-programming

  https://juejin.cn/post/6844903929814188045

* 典型应用：rxjs



# 消息

## PostMessage

* 一般用于跨线程或者进程通信

## 专用信道 messageChannel

* 一般用于创建一个专用信道，把广播或者多播转换为一个专门的信道！

  ==这也是跨进程通用解决方案！==



# 浏览器相关

## 浏览器线程和阻塞

https://segmentfault.com/a/1190000006143086

常见问题：

* js 引擎和渲染引擎是互斥的

## 主线程

参考：https://developer.mozilla.org/zh-CN/docs/Glossary/Main_thread

**主线程**用于浏览器处理用户事件和页面绘制等。默认情况下，浏览器在一个线程中运行一个页面中的所有 JavaScript 脚本，以及呈现布局，回流，和垃圾回收。这意味着一个长时间运行的 JavaScript 会阻塞线程，导致页面无法响应，造成不佳的用户体验。

* 主线程用于处理用户事件和页面渲染。包含：JavaScript脚本、布局、回流和垃圾回收。
* 长时间的JavaScript运行会阻塞线程，导致页面无法响应。



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



# 原型链

* 主要是区分函数的原型链
  * 一个函数的 `__proto__`  指向 Function.prototype
  
  * Function.prototype 的 `__proto__`  指向 Object.prototype
  
  * 一个新建的函数，会初始化一个 prototype 对象，这个对象和 Function 没有一点关系
  
    ```js
    function Fn() {}
    Function.prototype.a = 1
    
    const f = new Fn()
    f.a // 不存在
    ```
  
  * ==函数的实例对象和 Function 对象没有一点关系，只和定义 function 时候绑定的prototype有关系，和Object.prototype有关系==

## 题目

```js
function F() {}
F.prototype.a = 1;
var f1 = new F()
F.prototype = {
    a: 2
}
var f2 = new F()
console.log(f1.a) // 1
console.log(f2.a) // 2

```



# 继承

## 单词

* sup 超级，上层的
* Sub 附属的，下层的
* sibling 兄弟的



## new - 继承

* 使用 new 实例出来的对象对于构造函数中的引用类型，不会引用同一个，而是创建一个新的

  ```js
  function Fn() { this.test = [1] }
  
  let fn1 = new Fn()
  
  let fn2 = new Fn()
  
  fn1.test.push(2)
  
  fn1.test
  // (2) [1, 2]
  fn2.test
  // [1]
  ```

  

### 继承实现——寄生组合

```js
function SuperType(name) {
    this.name = name
}

SuperType.prototype = {
    fn() {
        console.log('测试')
    }
}


function SubType() {
    SuperType.call(this)
}

SubType.prototype = Object.create(SuperType.prototype)

SubType.prototype.constructor = SubType

```

* ES6 的类中的方法都挂载到 原型上了，是不可枚举的。



# 连续赋值

* 连续赋值的情况，一定要拆分开看，而不是从右到左

```js
var foo = {n: 1};
var bar = foo;
foo.x = foo = {n: 2};

bar = ？
foo = ？
```



# 关于作用域的问题

* 变量提升是 函数提升到最前边，如果函数前边有赋值操作，会进行覆盖的情况

  ```js
  a = 2
  function a() {}
  // a = 2
  ```

  



# 相等（==）

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Equality

等于运算符（`==`）检查其两个操作数是否相等，并返回`Boolean`结果。与[严格相等](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Strict_equality)运算符（`===`）不同，它会尝试==强制类型转换==并且比较不同类型的操作数。

相等运算符（`==`和`!=`）使用[抽象相等比较算法](https://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3)比较两个操作数。可以大致概括如下：

- 如果两个操作数都是对象，则仅当两个操作数都引用同一个对象时才返回`true`。

- ==如果一个操作数是`null`，另一个操作数是`undefined`，则返回`true`。==

  但是不等于 0。这个是相等运算的特例，要记住。

  也就是null和undefined可以互相成就。

- ==如果两个操作数是不同类型的，就会尝试在比较之前将它们转换为相同类型：==

  - 当数字与字符串进行比较时，会尝试将字符串转换为数字值。(这个转换使用的是 Number() 方法)

  - 如果操作数之一是

    ```
    Boolean
    ```

    ，则将布尔操作数转换为1或0。

    - 如果是`true`，则转换为`1`。
    - 如果是 `false`，则转换为`0`。

  - 如果操作数之一是对象，另一个是数字或字符串，会尝试使用对象的`valueOf()`和`toString()`方法将对象转换为原始值。

- 如果操作数具有相同的类型，则将它们进行如下比较：

  - `String`：`true`仅当两个操作数具有相同顺序的相同字符时才返回。
  - `Number`：`true`仅当两个操作数具有相同的值时才返回。`+0`并被`-0`视为相同的值。如果任一操作数为`NaN`，则返回`false`。
  - `Boolean`：`true`仅当操作数为两个`true`或两个`false`时才返回`true`。

此运算符与[严格等于](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Strict_equality)（`===`）运算符之间最显着的区别在于，严格等于运算符不尝试类型转换。相反，严格相等运算符始终将不同类型的操作数视为不同。



## 总结

异类：

1. 对象比对象
2. unll undefined

普遍

1. 不同类型
2. 相同类型



### 重要（+、-、==）

* 除了两边都是引用类型

* 如果有一个是基本类型，就会用基本类型的方法进行计算。有以方是数字就用 Number 转换，Boolean 类型必须用 Number 转换。(除了字符串的加法，直接往上堆)

  引用类型的计算，最后是基本类型的计算。

  * 特例：undefined 转换为NaN，null 转换为 0。null == 0 为 false，所有在比较时，undefined 和 null 是特例。

## valueOf 与 toString

* 对象做计算的时候，先调用 valueOf 方法，如果 valueOf 方法没有重写或者返回一个引用类型，就会去调用 toString 方法



# call apply 还有一些不知道的 

* 如果 call 第一个参数是 基础类型（除了 null、undefined），则会返回该类型的构造函数

## bind 返回的函数，this不会改变

```js
let obj = {test: 1}

function fn(){ console.log(this) }

const an1 = fn.bind(obj)

an1.call(window)
// {test: 1}
```

## 箭头函数中 this 不会被修改

* 因为构造函数要执行this，因此箭头函数不能被 new

## bind 与 new

```js
function func() {
  console.log(this, this.__proto__ === func.prototype)
}

boundFunc = func.bind(1)
new boundFunc() 
```

* bind 只是修改调用时候的 this ，不是修改 new 时候的this



## bind 和 箭头函数

* 箭头函数的优先级小于 bind



# undefined 和 null

* 在 es5 中 undefined 是可以修改的，因此建议和 `void(0)` 进行比较。



# context

## 变量提升

https://juejin.cn/post/6943035836691087397

https://juejin.cn/post/6933377315573497864#heading-10

* ==只有 var 和 function 关键字会出现变量提升，如果直接赋值一个值，不会出现变量提升。==
* 如果使用 var 变量提升，则代码块 if 是不会作为一个单独作用域的。
* `var a = 1, b =2` 相当于每一个都有 var ，和 ; 不同
* `var a = b = 1` b 没有变量提升

* 如果 函数内部的值没有赋值，var 会变量提升，参数没有变量提升，不是用var定义的。因此形参会覆盖掉内部的数据。

  var 提升，形参不提升

  ```js
  var a = 1;
  function foo(a) {
      console.log(a)
      var a
      console.log(a)
  }
  foo(a);
  // 11
  ```



### 易错点

* ==一点要注要，没有 var 和 function 不会变量提升!!!!!!==

  ```js
  function foo(){
      console.log(a)
      a =12;
      b = '林一一'
      console.log('b' in window)
      console.log(a, b)
  }
  foo()
  // 报错
  ```

* 直接使用一个值会报错，一定要搞清楚有没有定义！！！

  ```js
  fn();
  console.log(v1); // 报错
  console.log(v2);
  console.log(v3);
  function fn(){
      var v1 = v2 = v3 = 2019;
      console.log(v1);
      console.log(v2);
      console.log(v3);
  }
  
  ```

* if 语句中的变量没有提升

* 如果遇到 函数和普通变量，在赋值之前调用，一定调用的是函数

  ```js
  console.log(a);   
  var a=1;
  function a(){
      console.log(1);
  }
  ```

* 修改 arguments 能够修改形参的值

  ```js
  function fn(a) {
    a = 1
    arguments[0] = 2
    // a === 2
  }
  ```

  



## 赋值这个动作发生之后才会改变变量的值

```js
var b = {
    a,
    c: b
}
console.log(b.c);
// b.c 执行的时候，b还是 undefined的
```

## arguments 能够修改形参的值

```js
function fn(a) { arguments[0] = 2; console.log(a) }
// 输出 2
```

## 非匿名自执行函数的变量提升

* 匿名执行函数和非匿名自执行函数在全局环境下不具备变量提升的机制

  ```js
  var a = 10;
  (function c(){
  })()
  console.log(c)
  // Uncaught ReferenceError: c is not defined
  
  ```

  IIFE 函数具备自己的作用域，所以全局下不会变量提升。

* 匿名自执行函数在自己的作用域内存在正常的变量提升，说白了就是拿的最外层的数据。

  ```js
  var a = 10;
  (function(){
      console.log(a)
      a = 20
      console.log(a)
  })()
  console.log(a)
  // 10, 20, 20
  ```

* 自调用函数不能修改自己的函数名

  ```js
  var a = 10;
  (function a(){
      console.log(a)
      a = 20
      console.log(a)
  })()
  ```

### 总结

1. 不会变量提升，在外边拿不到这个函数
2. 内部不能修改函数的赋值，并且这个函数能够在内部访问到（在外部访问不到）







# 原型链

* 原型链整个替换不影响原来的实例，因为已经赋值了，明白吗？
* 如果通过 this.\__prop__.test() 调用的方法 this 执行原型对象，不是 this

https://juejin.cn/post/6943035836691087397#heading-14



## 函数原型链

* 只有构造函数有 prototype

函数本身：

```js
Function.prototype.__proto__ === Object.prototype
// true

function fn() {}
fn.__proto__ === Function.prototype
// 明显函数是由 Function 实例而来的

Function.__proto__ === Object.prototype
// false

fn1.prototype.__proto__ === Object.prototype
// true，构造函数一开始的 prototype 不是Object 的原型对象
```





# new 操作符

* 如果不进行括号，默认跟着后边的第一个带括号的函数



# 前端模板化

## 1. dsl

https://zhuanlan.zhihu.com/p/107947462

* DSL 即「Domain Specific Language」，中文一般译为「领域特定语言」，在[《领域特定语言》](https://link.zhihu.com/?target=https%3A//book.douban.com/subject/21964984/)这本书中它有了一个定义：

  > 一种为**特定领域**设计的，具有**受限表达性**的**编程语言**



# setInterval 的问题

- 使用 setInterval 时，某些间隔会被跳过；
- 可能多个定时器会连续执行；

setInterval 只会告诉你，固定一段时间执行里边的回调，并不会告诉你数据更新的频率，取决于你计算的快慢。



# event 事件对象

## 1. target 和 currentTarget

* currentTarget 永远等于 this
* target 等于当前触发的元素，可能是由它上报上来的



# 图片懒加载

* 使用 intersectionObserver，实现一下?

  例子：

  ```js
  // Register IntersectionObserver
  const io = new IntersectionObserver(entries => {
    entries.forEach(entry => {
      // Add 'active' class if observation target is inside viewport
      if (entry.intersectionRatio > 0) {
        entry.target.classList.add('active');
      }
      // Remove 'active' class otherwise
      else {
        entry.target.classList.remove('active');
      }
    })
  })
  
  // Declares what to observe, and observes its properties.
  const boxElList = document.querySelectorAll('.box');
  boxElList.forEach((el) => {
      io.observe(el);
  })
  ```



# Blob

## 简介

Blob 对象表示一个不可变、原始数据的类文件对象。它的数据可以按文本或二进制的格式进行读取，也可以转换为 ReadableStream 来用于数据操作

Blob 表示的不一定是 JavaScript 原生格式的数据。File 接口基于 Blob，集成了 blob 的功能并将其扩展以支持用户系统的文件

## 使用 Blob

* 创建一个 blob 对象

  要从其它非 blob 对象和数据构造一个 `Blob`，请使用 [`Blob()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob/Blob) 构造函数

* 创建一个子集

  要创建一个 blob 数据的子集 blob，请使用 [`slice()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob/slice) 方法。

* 获取用户文件系统上文件对应的 Blob 对象，参考 File 文档

  File 对象继承了 Blob 的 api

## 创建一个 Blob 对象

```
var aBlob = new Blob( array, options );
```

- *array* 是一个由[`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer), [`ArrayBufferView`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypedArray), [`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob), [`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String) 等对象构成的 [`Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array) ，或者其他类似对象的混合体，它将会被放进 [`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)。DOMStrings 会被编码为 UTF-8。

  注意：arraybufferview 也能被创建

- options

  是一个可选的

  ```
  BlobPropertyBag
  ```

  字典，它可能会指定如下两个属性：

  - `type`，默认值为 `""`，它代表了将会被放入到 blob 中的数组内容的 MIME 类型。
  - `endings`，默认值为`"transparent"`，用于指定包含行结束符`\n`的字符串如何被写入。它是以下两个值中的一个：`"native"`，代表行结束符会被更改为适合宿主操作系统文件系统的换行符，或者 `"transparent"`，代表会保持 blob 中保存的结束符不变 非标准

## 属性

* size: *返回 Blob 对象中所包含数据的大小（字节）。利用这个方法能够获取字符串所占的内存大小*
* Type: 返回创建 Blob 对象时的 MIME 类型，如果类型未知，则该值返回为空

## 实例方法

### [`Blob.prototype.arrayBuffer()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob/arrayBuffer)

返回一个 promise，其会兑现一个包含 `Blob` 所有内容的二进制格式的 [`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)。

### [`Blob.prototype.slice()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob/slice)

返回一个新的 Blob 对象，包含了源 Blob 对象中指定范围内的数据

```typescript
blob2.slice(1,3,'test')
// 开始、结束、新blob的类型
```

[`Blob.prototype.stream()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob/stream)

* 返回一个能读取 Blob 内容的 ReadableStream

[`Blob.prototype.text()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob/text)

* 返回一个 promise，能够获取一个包含 Blob 所有内容的 UTF-8 格式的字符串

  获取 blob 中的内容

## 从 Blob 中读取数据

### 1. 使用 fileReader

**`FileReader`** 对象允许 Web 应用程序异步读取存储在用户计算机上的文件（或原始数据缓冲区）的内容，使用 [`File`](https://developer.mozilla.org/zh-CN/docs/Web/API/File) 或 [`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob) 对象指定要读取的文件或数据。

* Input 上传的文件 File
* 拖拽的数据 DataTransfer 对象
* 来自在一个[`HTMLCanvasElement`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLCanvasElement)上执行`mozGetAsFile()`方法后返回结果。

#### 用法

```typescript
// 1. 创建一个 reader 对象
const reader = new FileReader()
// 2. 监听事件
reader.onload = e => {
    console.log(e.target.result) // reader.result 就是结果
}
// 读取方式
reader.readAsText(blob)
```

#### 事件

[`FileReader.onabort`](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader/abort_event)

处理[`abort`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLMediaElement/abort_event)事件。该事件在读取操作被中断时触发。

[`FileReader.onerror` (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/FileReader/error_event)

处理[`error`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/error_event)事件。该事件在读取操作发生错误时触发。

[`FileReader.onload`](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader/load_event)

处理[`load`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/load_event)事件。该事件在读取操作完成时触发。

#### 读取方式

[`FileReader.abort()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader/abort)

中止读取操作。在返回时，`readyState`属性为`DONE`。

[`FileReader.readAsArrayBuffer()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader/readAsArrayBuffer)

开始读取指定的 [`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)中的内容，一旦完成，result 属性中保存的将是被读取文件的 [`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) 数据对象。

[`FileReader.readAsBinaryString()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader/readAsBinaryString) 非标准

开始读取指定的[`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)中的内容。一旦完成，`result`属性中将包含所读取文件的原始二进制数据。

[`FileReader.readAsDataURL()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader/readAsDataURL)

开始读取指定的[`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)中的内容。一旦完成，`result`属性中将包含一个`data:` URL 格式的 Base64 字符串以表示所读取文件的内容。

[`FileReader.readAsText()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader/readAsText)

开始读取指定的[`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)中的内容。一旦完成，`result`属性中将包含一个字符串以表示所读取的文件内容。

#### 将图片文件转换为base64

如上 fileReader 的 readAsObjectUrl 方法能够将文件转换为 URL 格式的 base64 字符串，而 URL.createObjectUrl 是转换为一个 blob url。

* 那是不是意味着能够使用 fileReader 将字符转换为 base64 呢？

#### 将字符串转换为base64

* 如上

### 2. 使用 Response 对象

```typescript
const text = await (new Response(blob)).text();
```

* Fetch api 的响应对象 Response 能够处理二进制数据

### 3. 直接使用 Blob.prototype.text() 方法

* 能够直接读取 blob 中的内容，以 UTF-8 展示。

### 4. 通用方案：TextDecoder

* 这是一个通用方案，它能够转换 类型化数组，因此Blob也能使用该方法

## 与ArrayBuffer关系

* Blob 能够通过 ArrayBuffer 方法返回一个 arraybuffer 对象
* arraybuffer 对象能够通过 Blob 构造函数转换为一个 Blob 对象

注意：Blob 对象不会返回一个具象的 TypedArray，只会返回一个缓存区，让用户自由选择。

## 引用 Blob 对象——预览Blob

* 使用 URL.createObjectURL，参见下面

## 应用场景

### 1. 获取字符串内存大小

```typescript
/**
 * 用来校验内容大小。通过返回true，不通过返回false
 */
export const checkContentSize = (content: string): boolean => {
    const blob = new Blob([content]);
    // const targetSize = 8 * 1024 * 1024; // 目标大小8M
    const targetSize = 100 * 1024; // 目标大小临时100k
    if (blob.size > targetSize) {
        return false;
    }
    return true;
};
```



## FileReader

* 这个api主要是用来读取文件或者Blob的！！看名字就能猜出来。

## 合并多个 Blob

* 使用构造函数的方式

```typescript
let blob1 = new Blob(['1234'])
undefined
let blob2 = new Blob(['4567'])
undefined
let blob = new Blob([blob1, blob2])
```

## 拆分Blob

* 使用 slice 方法，返回一个新的 Blob

```typescript
await blob.slice(0,4).text()
```

# File

* 基于 Blob，集成了 blob 的功能并将其扩展以支持用户系统的文件。就是基于 Blob 然后进行了扩展，比如：文件名，修改时间
* 文件（File）接口提供有关文件的信息，允许JavaScript访问其内容

> 通常情况下，File对象是来自用户在 input file 选择文件后的 Filelist 对象（e.target.files），也有可能是拖拽操作生成的 DataTransfer 对象。或者来自 [`HTMLCanvasElement`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLCanvasElement) 上的 `mozGetAsFile`() API

File 对象时特殊类型的 Blob，且可以用在任意 Blob 类型的 context 中。

## 构造函数

* 和 Blob 有点像

  第一个参数是 一个包含[`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)，[`ArrayBufferView`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)，[`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)，或者 [`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String) 对象的 [`Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array) — 或者任何这些对象的组合。这是 UTF-8 编码的文件内容。

  第二个参数是 filename 或者文件路径

  第三个参数：options 选项

  * type: 类型
  * lastmodified: 最后修改时间，不传默认当前

## 属性

`File` 接口也继承了 [`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob) 接口的属性：

* [`File.lastModified`](https://developer.mozilla.org/zh-CN/docs/Web/API/File/lastModified) 只读： 返回毫秒数
* [`File.lastModifiedDate`](https://developer.mozilla.org/zh-CN/docs/Web/API/File/lastModifiedDate) 返回时间戳（带时区的）

## 方法

* 参考 Blob



# ArrayBuffer

* 知乎 arrayBuffer 使用来干嘛的？

  https://www.zhihu.com/question/30401979/answer/133686569

* mdn: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer

> JS里的Array，因为有很多功能，而且是不限制类型的，或者它还可能是稀疏的……总之这个Array是“托管”的，它内部有比较复杂的实现。
>
> 而如果你从XHR、File API、Canvas等等各种地方，读取了一大串字节流，如果用JS里的Array去存，又浪费，又低效。
>
> 于是为了配合这些新的API增强JS的二进制处理能力，就有了ArrayBuffer。

> ArrayBuffer简单说是一片内存，但是你不能（也不方便）直接用它。这就好比你在C里面，malloc一片内存出来，你也会把它转换成unsigned_int32或者int16这些你需要的实际类型的数组/指针来用。
>
> 这就是JS里的TypedArray的作用，那些Uint32Array也好，Int16Array也好，都是给ArrayBuffer提供了一个“View”，MDN上的原话叫做“Multiple views on the same data”，对它们进行下标读写，最终都会反应到它所建立在的ArrayBuffer之上。
>
> 除了TypedArray以外，也可以使用DataView来读写ArrayBuffer，这样会麻烦一些，但也更灵活。DataView能更自由的选择字节序，对于对齐的要求也更低。

* 贺师俊大佬的回答：JavaScript中"ArrayBuffer"对象与"Blob"对象到底有什么区别？ - 贺师俊的回答 - 知乎 https://www.zhihu.com/question/400286737/answer/1272247244

> Blob对象并不对应内存，一个blob引用更像文件句柄，你读取blob的内容，可以是全放进一个ArrayBuffer里，也可以直接得到一个字符串（如果是文本文件），还可以通过Stream来读取，特别是blob很大的情况下内存也放不下，只能通过流处理。
>
> 注意，Blob并不像ArrayBuffer是JS语言内置的，而是Web API，Node.js的API里就没有Blob。这也是为什么MDN说「Blobs can represent data that isn't necessarily in a JavaScript-native format」（中文版的翻译「Blob表示的不一定是JavaScript原生格式的数据」反而比英文原文难理解）。

## 特点

**`ArrayBuffer`** 对象用来表示==通用的、固定长度==的==原始二进制数据==缓冲区，也就是初始化了一段区域，那么就需要传入该区域的长度。

* 也就是一开始实例化就需要固定长度
* 原始的二进制数据缓存区

它是一个==字节数组==，通常在其他语言中称为“byte array”。

* 它构造函数的 length 表示字节的长度，一个字节只能表示 8 位 二进制数的大小，0-255

你不能直接操作 `ArrayBuffer` 的内容，==而是要通过[类型数组对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)或 [`DataView`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/DataView) 对象来操作==，==它们会将缓冲区中的数据表示为特定的格式==，并通过这些格式来读写缓冲区的内容。

==一定要注意：每一项都是一个字节，位数是固定的，不会因为存储的字符需要多个字节表示就存在一项存储多个字节的情况！！！==

### ==不能直接操作==

> 你不能直接操作 `ArrayBuffer` 中的内容；而是要通过[类型化数组对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)或 [`DataView`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/DataView) 对象来操作，它们会将缓冲区中的数据表示为特定的格式，并通过这些格式来读写缓冲区的内容。

* arraybuffer 是一个原始数据类型的缓存区，不能直接操作。需要通过类型化数组对象和 DataView 对象来操作。

### ==构造函数==

* 需要传入长度，在实例化的时候固定长度

### 静态方法

* ArrayBuffer.isView() 能够获取参数是不是一个视图

## TypedArray——JavaScript类型化数组——把数组类型化了

> 一个 ***TypedArray\*** 对象描述了底层[二进制数据缓冲区](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)的类数组视图。没有称为 `TypedArray` 的全局属性，也没有直接可用的 `TypedArray` 构造函数。

* 为啥叫类型化数组？因为它就是用来操作 ArrayBuffer 的，以某一个具体的类型。

* 一个类似数组的对象，提供了一种用于在内存缓存区中访问原始二进制数据的机制（缓存区、访问二进制）。

  是一个访问原始二进制数组的视图。

* 不能直接使用 TypedArray 类型或者构造函数（下面所述，它分为两类）

* 类型化数组需要新开辟一个内存空间，然后再进行读取和写入

### 理解一下

* ArrayBuffer 本身是创建了一段内存区域
* 如果想对其进行读写，需要再创建一个类型化的数组

### 为什么出现

背景：

> [`Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array) 存储的对象能动态增多和减少，并且可以存储任何 JavaScript 值。JavaScript 引擎会做一些内部优化，以便对数组的操作可以很快。然而，随着 Web 应用程序变得越来越强大，尤其一些新增加的功能例如：音频视频编辑、访问 WebSockets 的原始数据等，很明显有些时候如果使用 JavaScript 代码可以快速方便地通过类型化数组来操作原始的二进制数据将会非常有帮助。JavaScript 类型化数组中的每一个元素都是原始二进制值，而二进制值采用多种支持的格式之一（从 8 位整数到 64 位浮点数）。

* 随着web技术的发展，程序变得强大，需要访问一些原始的二进制数据（音视频、websocket的socket buffer），所以出现了类型化数组
* 二进制编码为可操作字符，然后再解码回去，很消耗性能

### 不是普通的数组

* 使用 Array.isArray 返回false
* 不是所有的方法都能使用，比如扩展数组长度的方法。pop push 等，内存不可动态扩展。

### 抽象的对象

* 是一个 抽象类，为所有类型化数组的子类（比如UInt8Array）提供了使用方法的通用接口。不能被实例化，没有直接暴露全局对象。

### 不可以动态扩展

类型化数组的实例需要传入一个缓冲区，一开始的时候就确定长度，不能动态扩展长度。

### 异常

- [`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError)

  抛出以下情况之一：传递 `typedArray`，但它是 [bigint](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigInt) 类型，而当前构造函数不是，反之亦然。传递 `typedArray`，但它的缓冲区被分离了，或者直接传递分离了视图的 `buffer`。

- [`RangeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RangeError)

  抛出以下情况之一：新的类型化数组的长度太大。`buffer` 的长度（如果 `length` 参数没有指定）或 `byteOffset` 不是新的类型化数组元素大小的整数倍。`byteOffset` 不是有效的数组索引（0 和 253 - 1 之间的整数）。当从一个缓冲区创建视图，边界在缓冲区之外。换句话说，`byteOffset + length * TypedArray.BYTES_PER_ELEMENT > buffer.byteLength`。

传入的arrayBuffer 的长度必须是该类型化数组的内存的整数倍，不然会报 range 错误

### 默认值

* 每一项的默认值都是0

```typescript
Uint8Array.from({length:2})
```

* 没有像普通数组那种字面量实例方法

### 缓冲和视图：类型数组的架构

缓存就是存储数据的实体；视图只是操作这个缓存的API。

* 介绍怎么样操作类型数组

==为了达到最大的灵活性和效率！JavaScript类型化数组将实现拆分为了缓存和视图两部分==

* 缓存（由ArrayBuffer对象实现）描述的是一个数据分块。缓存没有格式可言，并且不提供访问其内容的机制（只是一个数据分块，一个内存区域）

  比如：ArrayBuffer

* 视图：为了访问在缓冲对象中包含的内存，需要使用视图。视图提供了上下文——即数据类型、起始偏移量和元素数——将数据转换为实际有类型的数组。

  Uint8Array等。

操作逻辑：

1. 先定大小
2. 进行读写操作

以下是arrayBuffer

![ArrayBuffer 中的类型化数组](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays/typed_arrays.png)

* 8位数组一个单元格就是一个 byte，也就是一个字节

### 类型化数组视图

* 类型化数组视图具有==自描述性的名字（功能体现在名字上）== 和 ==所有常用的数值类型（像 `Int8`、`Uint32`、`Float64` 等等）==。他们都是类型化数组视图，可以用来操作类型化数组。

他们的范围如下：

| 类型                                                         | 值范围                                         | 字节数 | 描述                                                  | 对应的 Web IDL 类型   | 等效的 C 类型                |
| :----------------------------------------------------------- | :--------------------------------------------- | :----- | :---------------------------------------------------- | :-------------------- | :--------------------------- |
| [`Int8Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Int8Array) | -128~127                                       | 1      | 8 位有符号整数（补码）                                | `byte`                | `int8_t`                     |
| [`Uint8Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) | 0~255                                          | 1      | 8 位无符号整数                                        | `octet`               | `uint8_t`                    |
| [`Uint8ClampedArray`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint8ClampedArray) | 0~255                                          | 1      | 8 位无符号整数（值会被裁剪）                          | `octet`               | `uint8_t`                    |
| [`Int16Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Int16Array) | -32768~32767                                   | 2      | 16 位有符号整数（补码）                               | `short`               | `int16_t`                    |
| [`Uint16Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint16Array) | 0~65535                                        | 2      | 16 位有符号整数                                       | `unsigned short`      | `uint16_t`                   |
| [`Int32Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Int32Array) | -2147483648~2147483647                         | 4      | 32 位有符号整数（补码）                               | `long`                | `int32_t`                    |
| [`Uint32Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint32Array) | 0~4294967295                                   | 4      | 32 位有符号整数                                       | `unsigned long`       | `uint32_t`                   |
| [`Float32Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Float32Array) | `-3.4E38`~`3.4E38` 以及 `1.2E-38`（最小正数）  | 4      | 32 位 IEEE 浮点数（7 位有效数字，例如 `1.123456`）    | `unrestricted float`  | `float`                      |
| [`Float64Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Float64Array) | `-1.8E308`~`1.8E308` 以及 `5E-324`（最小正数） | 8      | 64 位 IEEE 浮点数（16 位有效数字，例如 `1.123...15`） | `unrestricted double` | `double`                     |
| [`BigInt64Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigInt64Array) | -263~263 - 1                                   | 8      | 64 位有符号整数（补码）                               | `bigint`              | `int64_t (signed long long)` |
| [`BigUint64Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigUint64Array) | 0~264 - 1                                      | 8      | 64 位无符号整数                                       | `bigint`              |                              |

* 使用方式：

  ```typescript
  new Int8Array(new ArrayBuffer(10))
  ```

* 他们的数字分别代码 bytes 数，8个 bytes 又是一个字节。

  描述的是每一个元素的字节数，初始化时确定长度。比如创建一个16个字节的 ArrayBuffer

  使用 Int32Array 去处理它，则一共有四个元素，每个元素有四个字节。32 / 8 = 4

### DataView——操作缓冲区的一种

* todo 如下

### 使用

一个类型化数组（TypedArray） 对象描述了一个底层的二进制数据缓冲区（binary data buffer）的一个类数组视图（view）。TypedArray 是一类构造函数的统称。

* 下边是构造方法，能通过 长度、typedArray 构造的实例、对象、buffer 实例等方式进行构造

```js
// 下面代码是语法格式，不能直接运行，
// TypedArray 关键字需要替换为底部列出的构造函数。
new TypedArray(); // ES2017中新增
new TypedArray(length);
new TypedArray(typedArray);
new TypedArray(object); // 当传入一个 object 作为参数时，就像通过 TypedArray.from() 方法创建一个新的类型化数组一样。
new TypedArray(buffer [, byteOffset [, length]]);
```

* 总共有如下几种构造方法。每种构造函数的每个元素支持的字节大小不一样，元素范围也不一样。

```js
const buffer2 = new ArrayBuffer(2)
Int8Array(); // 一个元素表示一个字节，如果引用上述的 buffer2，长度为 2
Uint8Array();
Uint8ClampedArray();
Int16Array(); // 一个元素表示一个字节，如果引用上述的 buffer2，长度为1就能表示这个 buffer2
Uint16Array();
Int32Array();
Uint32Array();
Float32Array(); // 对应32长度的
Float64Array(); // 对应64位
```

* 可以通过 [0] 数组引用的方式去取或者设置数组的每一项

### 静态方法

这些属性定义在 `TypedArray` 构造函数中，因此由所有 `TypedArray` 子类型共享。

- [`TypedArray.from()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypedArray/from)

  从类数组或者可迭代对象创建新的 `TypedArray`。参见 [`Array.from()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/from)。

- [`TypedArray.of()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypedArray/of)

  创建一个具有可变参数的 `TypedArray`。参见 [`Array.of()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/of)。

注意：==静态方法是除了构造函数外，另一种创建类型化数组实例对象的方式。==

==和普通的数组一样，有这三种方式进行实例的创建，除了没有字面量表示方法。==

### 实例方法

* buffer

  获取当前typedArray 存储的 arrayBuffer。

### 怎么样理解 8 16 32 呢？

==说白了就是没一项的容量不一样，用数字表示==

他们都是位数和进制没有啥关系！！！在使用的时候，每一项都是10进制，如果要进行二进制计算，需要转换为二进制。

* 他们表示数组每一个元素的字节长度。比如说一个长度为2的arrayBuffer是不能赋值给 `Uint32Array` 的，因为32位的数组每一项至少存4个字节，长度为2的arrayBuffer字节不够

* 相应的每一种 TypedArray 每一项能存的内容长度不同。8/16/32就代表每一项能存储的字节大小。

最重要的注意点：

* 最重要的注意点是 8，32 每一项存的内容大小不一致，在构造 ArrayBuffer 的时候长度要注意，必须是相应类型的倍数，否则抛出range错误

  比如：32位的类型化数组，只能保存4的倍数长度的arrayBuffer

#### 32 的 typedArray 可以读取吗？

* 可以被读取，也是使用 textDecoder。存的数据是一样的，这样就涉及到了 32位和8位的转换

### 32 位和 8 位的转换

* 缓存区是可以随意转换为任何位数的 typedArray 的，那么我们利用 ArrayBuffer 作为中间值就可以完成32位和8位的互相转换

```typescript
new Uint8Array(view32.buffer)
```

* 转换的计算规则：把对应的二进制进行合并

  比如：Int8Array -> [127, 127, 0, 0] 转换为 16 位的 -> [32639, 0]，前两位二进制合并为8位加8位：`0111111101111111` 转换为10进制就是 32639。

### 同一个数据不通的视图

* 同一个 ArrayBuffer ，使用不同的视图操作（Int16、Int32），他们操作的是同一个数据源，只是展示不同

### 使用类型化数组的web api

[`FileReader.prototype.readAsArrayBuffer()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader/readAsArrayBuffer)

* `FileReader.prototype.readAsArrayBuffer()` 读取对应的 [`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob) 或 [`File`](https://developer.mozilla.org/zh-CN/docs/Web/API/File) 的内容

[`XMLHttpRequest.prototype.send()`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/send)

* `XMLHttpRequest` 实例的 `send()` 方法现在支持使用类型化数组和 [`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) 对象作为参数。

[`ImageData.data`](https://developer.mozilla.org/zh-CN/docs/Web/API/ImageData)

* 是一个 [`Uint8ClampedArray`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint8ClampedArray) 对象，用来描述包含按照 RGBA 序列的颜色数据的一维数组，其值的范围在 `0` 到 `255` 之间。

### 总结

* 操作 缓冲 （ArrayBuffer） 需要新建一个视图

## 类型化数组的操作

### 合并

* 主要思路是使用一般数组的合并方案

#### 1. TypedArray.from

```typescript
Uint8Array.from([...view1, ...view2])
```

#### 2. TypedArray.of

```typescript
Uint8Array.of(...view1, ...view2)
```

### 拆分



### 读取

#### 1. 使用 TextDecoder

* 使用 textDeoder 可以直接把TypedArray 中的数据读取成字符串

```typescript
let decoder = new TextDecoder
decoder.decode(view)
```

#### 2. 转换为 Blob

* 这个就不多说了，直接使用 Blob 构造函数

#### 3. 使用 FileReader

* 这个必须转换为 Blob 对象，不能直接读取 ArrayBuffer

### 修改

#### TypedArray.prototype.set

* 这个方法能够修改类型化数组本身，直接有副作用的

```typescript
buffer.set([98], 0)
// 第一个参数是一个数组，第二个参数是偏移量
```

#### 直接使用数组下标修改

```typescript
buffer[0] = 97
// 修改第一项为a
```

* 如果初始化 typedArray 的时候没有传入长度，就不可以直接某个元素

#### 其他数组的操作

* 这种方式就是使用数组的方法，包括 from 、of ，会生成新的数组

## ArrayBuffer

一下主要是mdn内容，主要为了描述作为缓冲对象在 typedArray 中的作用。 https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Typed_arrays

* ArrayBuffer 是一个种数据类型（不是一个具体的对象），用来表示一个通用的、固定长度（初始化时确定）的二进制数据缓冲区。你不能直接操作其内容，需要创建一个类型化数组的视图或一个描述缓冲数据格式的 DataView，使其能够读写缓冲区中的内容
* 也就是 typedArray 中的缓冲区

初始化长度是字节！！！

## DataView 

[`DataView`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/DataView) 是一种底层接口，它提供有可以操作缓冲区中任意数据的访问器（getter/setter）API

> **`DataView`** 视图是一个可以从 二进制[`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) 对象中读写多种数值类型的底层接口，使用它时，不用考虑不同平台的[字节序](https://developer.mozilla.org/zh-CN/docs/Glossary/Endianness)问题。

```typescript
var buffer = new ArrayBuffer(16);
var view = new DataView(buffer, 0);

view.setInt16(1, 42);
view.getInt16(1); // 42
```

## ArrayBuffer 和字符串转换

https://blog.csdn.net/LightUpHeaven/article/details/81240862

### TextEncoder 和 TextDecoder

* **`TextEncoder`** 接受码位流作为输入，并提供 UTF-8 字节流作为输出。
* **`TextDecoder`** 接口表示一个文本解码器，一个解码器只支持一种特定文本编码，例如 `UTF-8`、`ISO-8859-2`、`KOI8-R`、`GBK`，等等。解码器将字节流作为输入，并提供码位流作为输出。

这两个api提供了字符串和字节流互相转换的方法

### Blob

* 字符串转换为arrayBuffer

```typescript
let blob = new Blob(['我是内容'])
const arr = await blob.arrayBuffer()
```

* ArrayBuffer 转换为字符串

```typescript
let blob2 = new Blob([arrBuffer])
await blob2.text()
```

### String.fromCharCode

```typescript
String.fromCharCode.apply(null, new Uint8Array(res))
```

* 但是只能识别二个字节的编码，比如汉字（三个字节）就无法准确识别。

## 获取字符串内容字节大小

* 所有能把字符串转换为二进制buffer的都能够获取内容大小

### Blob

```typescript
let blob = new Blob(['我是内容'])
blob.size
```

### TextEncoder

* 这个api能够把字符串转换为二进制buffer，那么就能够获取到字节大小

```typescript
let res4 = new TextEncoder().encode('我是测试')
res4.byteLength
```

## 可转移对象

* 也就是可以被结构化和反结构化的对象，可以用来在不同的线程中传递。

https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Transferable_objects

# **[Streams API](https://developer.mozilla.org/zh-CN/docs/Web/API/Streams_API)**

## ReadableStream

* readableStream 接口标识可读的字节数据流，fetch api 通过 response 属性的 body 提供了一个具体的 ReadableStream 对象。

  当然也可以直接使用 `new Response(blob)` 传入一个blob对象的方式创建一个可读流。

### 锁定 - 只能被一个reader读取



### 实例属性

#### getReader()

[`ReadableStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/ReadableStream) 接口的 **`getReader()`** 方法会创建一个 `reader`，并将流锁定。只有当前 `reader` 将流释放后，其他 `reader` 才能使用。

```typescript
let blob = new Blob(['test', 'test2'])
let res = new Response(blob)
let reader2 = res.body.getReader()
const { done, value } = await reader2.read()
// 根据done来判断是否结束
```

## WritableStream

[Stream API](https://developer.mozilla.org/zh-CN/docs/Web/API/Streams_API) 的 **`WritableStream`** 接口为将流数据写入目的地（称为接收器）提供了一个标准的抽象。该对象带有内置的背压和队列。

### getWriter()

write() 方法能够往 writable 中添加数据

# Data URL

参考：https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/Data_URLs

Data URL，即前缀为 `data: ` 协议的URL，其允许内容创建者向文档中嵌入小文件，它们之前称为 data URL

* 也就是 base64图片所用的协议

## 语法

Data URL 由四个部分组成：前缀（`data:`）、指示数据类型的 MIME 类型、如果非文本则为可选的 `base64` 标记、数据本身：

```
data:[<mediatype>][;base64],<data>
```

Copy to Clipboard

`mediatype` 是个 [MIME 类型](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/MIME_types)的字符串，例如 `'image/jpeg'` 表示 JPEG 图像文件。如果被省略，则默认值为 `text/plain;charset=US-ASCII`。

比如一个base64的图片：

```html
<img src="data:image/jpeg;base64,sourcedata" />
```

#  [`blob:` URL](https://developer.mozilla.org/zh-CN/docs/Web/API/URL/createObjectURL_static)

* 由 URL.createObjectURL() 创建的一个 URL 对象

# MIME 类型

> **媒体类型**（通常称为 **Multipurpose Internet Mail Extensions** 或 **MIME** 类型）是一种标准，用来表示文档、文件或字节流的性质和格式。

* 一种标准，用来表示文档、文件或字节流的性质和格式

> 互联网号码分配机构（[IANA](https://www.iana.org/)）是负责跟踪所有官方 MIME 类型的官方机构，您可以在[媒体类型](https://www.iana.org/assignments/media-types/media-types.xhtml)页面中找到最新的完整列表。

* web服务器需要正确的响应 MIME 类型头，客户端才能正确的处理

## 语法

```
type/subtype
```

Copy to Clipboard

MIME 的组成结构非常简单；由类型与子类型两个字符串中间用`'/'`分隔而组成。不允许空格存在。*type* 表示可以被分多个子类的独立类别。*subtype 表示细分后的每个类型。*

* 就是一个类型＋一个子类型，中间用 `/` 分割

MIME 类型对大小写不敏感，但是传统写法都是小写。

* 建议都是小写！

# base64 编码

https://juejin.cn/post/7140571085610680334

base 6(6bit表示一个字节) 4四个字节一单元

## 二进制转换为base64

https://juejin.cn/post/6867878663115767822#heading-4

## 应用场景

是一种编码规范，有自己的编码集，会把目标编码成正常的asc2码返回内的编码（a-z,A-Z）等。不会出现平台兼容不足。（比如在html中<>是有语义的）

* 一共有64个字符，加上 = 补齐末尾的符号，一共65个

* 是一个二进制变成可打印字符的方案
* 每一个字符之间的分割由原编码考虑，base64 不管，只是够 6 个bit就编码

## 为什么要编码

一般两种情况需要编码

1. 转义，防止误识别
2. 压缩，降低传输成本

## 字符串和Base64 编码转换





# DataTransfer

https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer

`DataTransfer` 对象用于保存拖动并放下（drag and drop）过程中的数据。它可以保存一项或多项数据，这些数据项可以是一种或者多种数据类型。关于拖放的更多信息，请参见 [Drag and Drop](https://developer.mozilla.org/en-US/DragDrop/Drag_and_Drop).

这个对象可以从所有拖动事件 [`drag events`](https://developer.mozilla.org/zh-CN/docs/Web/API/DragEvent) 的 [`dataTransfer`](https://developer.mozilla.org/zh-CN/docs/Web/API/DragEvent/dataTransfer) 属性上获取。

## 常用属性

[`DataTransfer.files`](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer/files)

包含数据传输中可用的所有本地文件的列表。如果拖动操作不涉及拖动文件，则此属性为空列表。

[`DataTransfer.items`](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer/items) 只读

提供一个包含所有拖动数据列表的 [`DataTransferItemList`](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransferItemList) 对象。

## 一个拖动区域

```typescript
node.ondragover = e => {
    e.stopPropagation()
    e.preventDefault();
}

node.ondragenter = e => {
    e.stopPropagation()
    e.preventDefault();
}
// 如上两个事件一定要 preventDefault 这样才能作为拖动区域

node.ondrop = e => {
    console.log(e.dataTransfer.files, e.dataTransfer.items)
    e.stopPropagation()
    e.preventDefault();
  	// 一定要 preventDefault 这样才能防止使用 Chrome 打开
}
```

## 与 copy 事件联动

```typescript
document.oncopy = (e) => {
    console.log(e, '---00')
    e.preventDefault()
    e.clipboardData.setData("text/plain", 'dwadwad' + "\n\n 我是添加进来的内容")
}
```

### clipboardData

ClipboardEvent.clipboradData

**`ClipboardEvent.clipboardData`** 属性保存了一个 [`DataTransfer`](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer) 对象，这个对象可用于：

- 描述哪些数据可以由 [`cut`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/cut_event) 和 [`copy`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/copy_event) 事件处理器放入剪切板，通常通过调用 [`setData(format, data)`](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer/setData) 方法；
- 获取由 [`paste`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/paste_event) 事件处理器拷贝进剪切板的数据，通常通过调用 [`getData(format)`](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer/getData) 方法

==也就是能够通过该对象对粘贴板的内容进行获取或者修改！==

## clipboard api相关

* 使用 clipboard api 进行复制粘贴操作：

  参考：https://www.zhihu.com/search?type=content&q=ClipboardItem



# 浏览器事件

## stopImmediatePropagation

[`Event`](https://developer.mozilla.org/zh-CN/docs/Web/API/Event) 接口的 **`stopImmediatePropagation()`** 方法阻止监听同一事件的其他事件监听器被调用。



# URL 对象

## URL 构造函数

* 浏览器的 url 能够通过 location 进行读取写入操作。那么一个新的 url 没有挂载到浏览器的地址栏，该怎么进行处理呢？js有内置对象 URL 进行处理，把url传入返回一个对象，就能够像 location一样进行操作。

```typescript
const url = new URL('/test?name=aaa', 'http://www.baidu.com')
// or
const url1 = new URL('http://www.baidu.com?test=2')
```



## URL.createObjectURL()-用url表示本地文件

* 能够将一个 Blob 或者 File 对象返回一个关联的 string。其中包含一个表示参数中给出的对象的 URL

  这个 URL 的生命周期和创建它的窗口中的 [`document`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document) 绑定。这个新的 URL 对象表示指定的 [`File`](https://developer.mozilla.org/zh-CN/docs/Web/API/File) 对象或 [`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob) 对象。

* 也就是用一个 url 表示一个文件

## URL.revokeObjectURL

* 主要是配合上面的静态方法，释放 string 关联的 File，主要是为了平台在适合的时候进行垃圾回收

```typescript
// URL 的静态方法
const node: HTMLInputElement = document.querySelector('input')!
console.log(node)
node.addEventListener('change', (e: any) => {
    console.log(e.target.files)
    const file: File = e.target.files[0]

    const url = URL.createObjectURL(file)
    const img = document.createElement('img')
    img.src = url
    img.onload = () => {
        document.body.appendChild(img)
        setTimeout(() => {
            URL.revokeObjectURL(url);
        }, 1000);
    }
})
```

### 应用场景

* 图片预览，把本地上传的文件，用 img 标签进行预览
* a 标签下载本地文件，还能设置 a 标签的 href 字段进行下载操作



# ==todo Stream==

https://developer.mozilla.org/zh-CN/docs/Web/API/Streams_API

# URL

* 使用这个构造函数可以轻松的处理url，获取一些属性

## 1. searchParams

* https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams
* 定义了一些使用的 方法来处理URL的查询字符串。
  * 可以迭代、判断是否存在，get，和set



## 2. URLSearchParams

**`URLSearchParams`** 接口定义了一些实用的方法来处理 URL 的查询字符串。

这个在处理queryString 的情况下非常有用。可以做

* search 转换为对象
* 对象转换为 querystring

### 解析 queryString

* 在解析url中的search时非常有用，比如：

  ```typescript
  const url = new URLSearchParams('?test=2')
  ```

### 对象转为query

```typescript
const url = new URLSearchParams({test:1})
url.toString()
```

### 发送formdata请求

* 因为本身是能转换对象为 query，所以可以作为 formdata 的数据获取方式。

  formdata 的 body 就是 query 的方式

```typescript
fetch('http://127.0.0.1:3000/profile?test=222', {
        method: 'POST',
  			// 会直调用 toString 方法，因此会转为 query 的方式
        body: new URLSearchParams({
            test: '222',
            test1: '333'
        }),
        // body: 'test=2&test1=1',
        headers: {
            // "Content-Type": 'application/x-www-form-urlencoded'
            "Content-Type": 'application/json'
        },
    })
```

### 发送json数据

* 与 formdata 最大的区别就是 body 需要使用 JSON.stringify 去序列化

```typescript
fetch('http://127.0.0.1:3000/profile?test=222', {
    method: 'POST',
    body: JSON.stringify({
      test: '222',
      test1: '333&?dawd'
    }),
    // body: 'test=2&test1=1',
    headers: {
      // "Content-Type": 'application/x-www-form-urlencoded'
      "Content-Type": 'application/json'
    },
  })
```

### 默认做了 encode 计算





# 浏览器全局对象

* 在操作窗口（尤其是 iframe）的时候非常有用！

## parent

https://developer.mozilla.org/zh-CN/docs/Web/API/Window/parent

返回当前窗口的父窗口对象.

如果一个窗口没有父窗口,则它的 `parent` 属性为自身的引用.

如果当前窗口是一个 `<iframe>`, `<object>`, 或者 `<frame>`,则它的父窗口是嵌入它的那个窗口

* 拿到当前子窗口的

  ```js
  window.parent.frames
  ```

## top

https://developer.mozilla.org/zh-CN/docs/Web/API/Window/top

window.top 返回窗口层级最顶层窗口的引用。

 `window.parent` 返回当前窗口的直接父对象，而 [`window.top`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/top) 返回最顶层的窗口对象。

## self

https://developer.mozilla.org/zh-CN/docs/Web/API/Window/self

返回当前window对象的引用

## Window.frames

https://developer.mozilla.org/zh-CN/docs/Web/API/Window/frames

* 返回当前窗口，一个类数组对象，列出了当前窗口的所有直接子窗口。

# string

## String.prototype.charCodeAt

* 返回 UTF-16 编码单元

## String.fromCharCode

* 根据 utf-16 编码返回字符串
* 在做二进制转字符串的时候，能够起作用



# 上报相关

## unhandledrejection

https://developer.mozilla.org/zh-CN/docs/Web/API/Window/unhandledrejection_event

* 能够捕获 reject 错误

## rejectionhandle

https://zhuanlan.zhihu.com/p/30047612



# 语言基础

## 换行

在 Linux 和 macOS 上为 `\n`，在 Windows 上为 `\r\n`。



## for循环

* 和js其他情况一致，for循环第二个条件用来判断的，所以一起js能用来判断的情况都可以使用

```js
for ( var i = 0, type; type = [ 'String', 'Array', 'Number' ][ i++ ]; ) {}
// 数组弟四项是undefined，所以第四项为出口

let test = {
    0: 1,
    1: 1,
    2: 0
}
for ( var i = 0, type; test[i++]; ) {
    console.log(111)
}
// 打印两次111，第二项是0，直接退出
```



## for in 和 for of

* for in 能遍历到key，不能遍历到部署 iterator 接口的数据结构（比如Set）
* For of 能够遍历到所有部署 iterator 接口的数据结构

```typescript
let pets = new Set(["Cat", "Dog", "Hamster"]);
pets["species"] = "mammals";
for (let pet in pets) {
  console.log(pet); // "species"
}
for (let pet of pets) {
  console.log(pet); // "Cat", "Dog", "Hamster"
}
```



## label语句——循环标记

* https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Loops_and_iteration#label_%E8%AF%AD%E5%8F%A5

* 循环标记提供了一个让你的程序中其他位置引用它的标识。

  例如，你可以用 label 标识一个循环， 然后使用 `break` 或者 `continue` 来指出程序是否该停止循环还是继续循环。

看起来更像：

```
label :
   statement
```

```typescript
var func = function(){
    outerloop:
    for ( var i = 0; i < 10; i++ ){
        innerloop:
        for ( var j = 0; j < 10; j++ ){
            if ( i * j >30 ){
                break outerloop;
            }
        }
    }
};

// while循环
outer:
while(true) {
    for (let i =0; i< 100; ++i) {
        console.log(i)
        if (i > 10) {
            break outer
        }
    }
}

```



## 函数编程

### 两个没有用的特性——callee，caller

* 可以使用 arguments 对象获取当前函数的name（在匿名函数循环调用时有用）
* arguments.callee.caller可以获取函数调用栈的上一个函数

### length

* 函数的length 代表函数参数的个数

### currying



### uncurrying







## 字符串

* 换行

  `\n` `\r` `\t` 

  \t 跳格    \r 回车    \n 换行 

### 模板字符串

* 在模板字符串中写一个换行，转换成字符串是一个 `\n`，调用 console.log 可以显示换行，字符串不能直接显示。

  html可以使用innerHtml来换行







## bigInt

* 用n结尾的数字类型

* https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigInt

  **`BigInt`** 是一种内置对象，它提供了一种方法来==表示大于 `2^53 - 1` 的整数==。这原本是 Javascript中可以用 [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number) 表示的最大数字。**`BigInt`** 可以表示任意大的整数。

### 注意

不可以直接数字进行操作，需要使用 BigInt 进行转换

```typescript
1 + 1n
// 报错

BigInt(1) + 1n
```





## 解释型语言和编译型语言

![编译型语言和解释型语言的执行流程](http://c.biancheng.net/uploads/allimg/191231/1-1912311J415L7.gif)



* https://baike.baidu.com/item/%E8%A7%A3%E9%87%8A%E6%80%A7%E8%AF%AD%E8%A8%80/4665504?fr=aladdin

  https://baike.baidu.com/item/%E7%BC%96%E8%AF%91%E5%9E%8B%E8%AF%AD%E8%A8%80/9564109

  * 解释型语言的程序不需要在运行前编译，在运行程序的时候才翻译，专门的解释器负责在每个语句执行的时候解释程序代码。这样解释型语言每执行一次就要翻译一次，效率比较低。
  * 编译型语言是相对于解释性语言存在的，编译型语言首先需要将源代码编译成机器语言，再由机器运行机器码（二进制文件）

* http://c.biancheng.net/view/4136.html



## 调用栈

https://developer.mozilla.org/zh-CN/docs/Glossary/Call_stack



## 错误——error

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Error

### Error 对象

Error 对象有两个属性

* message 初始化时候的信息
* stack 抛出错误的堆栈信息



## url——路由相关

### pathname 和 href

pathname 会对 url 进行转义

href 不会进行转义

### 无刷新改变路由

参考：https://segmentfault.com/a/1190000022822185

1. 通过修改hash的方式更新路由

   window.location.hash = 'test'

2. 通过history的方式更新路由

   - **history.back()**: 返回浏览器会话历史中的上一页，跟浏览器的回退按钮功能相同
   - **history.forward()**:指向浏览器会话历史中的下一页，跟浏览器的前进按钮相同
   - **history.go()**: 可以跳转到浏览器会话历史中的指定的某一个记录页
   - **history.pushState()**可以将给定的数据压入到浏览器会话历史栈中，该方法接收3个参数，对象，title和一串url。pushState后会改变当前页面url
   - **history.replaceState()**将当前的会话页面的url替换成指定的数据，replaceState后也会改变当前页面的url

### pushState 和 replaceState

* 两个主要是更新页面状态 （state），一个是添加，一个是更新

* 区别：

  pushState 会给历史堆栈中添加数据，可以点击返回；replaceState 不会给历史堆栈中添加数据，点击返回无响应。

* 两个都不会刷新页面

### 监控路由变化

#### 监听hash变化

```javascript
window.addEventListener('hashchange',function(event){
   console.log(event);
})
```

#### 监听 back/forward/go

```javascript
window.addEventListener('popstate', function(event) {
     console.log(event);
})
```

但是，history.pushState()和history.replaceState()不会触发popstate事件，所以需要自己手动增加事件

#### 监听 pushState 和 replaceState 变化

* todo：通过自定义事件实现

* HTML5 引入了 [history.pushState()](https://developer.mozilla.org/zh-CN/docs/Web/API/History/pushState) 和 [history.replaceState()](https://developer.mozilla.org/zh-CN/docs/Web/API/History_API#The_replaceState()_method) 方法，它们分别可以添加和修改历史记录条目。这些方法通常与[`window.onpopstate`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/popstate_event) 配合使用。

  spa 返回按钮触发就是通过 onpopstate 事件进行监听的

> **备注：** 调用 `history.pushState()` 或者 `history.replaceState()` 不会触发 `popstate` 事件。`popstate` 事件只会在浏览器某些行为下触发，比如点击后退按钮（或者在 JavaScript 中调用 `history.back()` 方法）。即，在同一文档的两个历史记录条目之间导航会触发该事件。



## JavaScript中的数字

### 0.1 + 0.2 != 0.3 和 IEEE-754 标准

https://juejin.cn/post/6940405970954616839

## 位操作符

https://juejin.cn/post/6942291040044843038

### 按位非（~）

#### 取一个小数的整数位

```typescript
~~2.5
```

### 应用场景——前端权限设置

https://juejin.cn/post/6844904003499720712

https://juejin.cn/post/6844904143400730637#heading-5

#### 二进制权限设置——基础权限设置



#### 突破限制——高复杂的权限设置



#### 优势

* 后端数据库只需要存储用户对应的权限字符串，不需要复杂的关系表。

## 正则

### [字面量和构造函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#字面量和构造函数)

* 正则有两种书写方式

如果需要使用变量作为正则的一部分，可以使用正则构造函数的方式进行书写

```typescript
let test = 'text'
let regexp = new RegExp(`${test}dwa`, 'g')
```

#### 构造函数使用注意

* 在使用构造函数的方式使用转义字符的时候需要进行 '\\' 的转义，因为在js字符串中就视为一个符号

```typescript
new RegExp('\b' + 'ltd' + '\b')
```

### 范围

`[]` 

* 可以一个一个罗列，[a,b]等价于(a|b)

* 直接写范围，用于数字和字母，[a-b]、 [0-9]

* 可以用逗号隔开，也可以直接罗列

  ```tsx
  /[14, 12]/.test('4')
  // 能够匹配 1 4 1 2四个
  ```

  eg: 匹配win和mac的目录分割符

  ```typescript
  /[\\/]node_modules[\\/]/
  // [\\/] 能够匹配 mac 下的 / 和 win 下的 \\
  ```

  

### 个数范围

{}

* 至少三个：{3}
* 3到5个：{3,5}

### 特殊字符

https://www.runoob.com/regexp/regexp-syntax.html

\* 个数0或者多个

\. 匹配所有

#### 匹配所有的字符（包括空）

1. `/[\w\s]*/` 或者 `(\w|\s)*`

   第一种方式更友好，没有加 （） 不会匹配出多余的字符

2. 一种更奇葩的方式，`[^]*`

   具体不知道啥原因

| 符号                                                         | 说明                                                         | 例子                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------- |
| .                                                            | 匹配除了换行符所有的符号                                     |                                       |
| [`(x)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_expressions#special-capturing-parentheses) | 像下面的例子展示的那样，它会匹配 'x' 并且记住匹配项。其中括号被称为*捕获括号*。 |                                       |
| [`(?:x)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_expressions#special-non-capturing-parentheses) | 匹配 'x' 但是不记住匹配项。这种括号叫作*非捕获括号*。能够解决一些聚合问题，又不会引入捕获括号引入的问题。 | /abc+/<br />/(?:abc)+/                |
| [`x(?=y)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_expressions#special-lookahead) | 匹配'x'仅仅当'x'后面跟着'y'.这种叫做先行断言。               | 'foo test'.match(/foo(?=(?:\stest))/) |
| [`(?<=y)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_expressions#special-lookahead)x | 匹配'x'仅当'x'前面是'y'.这种叫做后行断言。                   | 'foo test'.match(/(?<=foo\s)test/)    |
| [^]                                                          | 匹配所有字符，包含换行                                       |                                       |
| [`\b`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_expressions#special-word-boundary) | 匹配单词边界                                                 |                                       |
| ?                                                            | 如果**紧跟在任何量词 \*、 +、? 或 {} 的后面**，将会使量词变为**非贪婪**（匹配尽量少的字符），和缺省使用的**贪婪模式**（匹配尽可能多的字符）正好相反。例如，对 "123abc" 使用 `/\d+/` 将会匹配 "123"，而使用 `/\d+?/` 则只会匹配到 "1"。 |                                       |
| `(?!a)`                                                      | 非正则，不匹配后面的正则                                     |                                       |



### 带有标志的正则

| 标志 | 描述                                                        |
| :--- | :---------------------------------------------------------- |
| `g`  | 全局搜索。                                                  |
| `i`  | 不区分大小写搜索。                                          |
| `m`  | 多行搜索。                                                  |
| `s`  | 允许 `.` 匹配换行符。                                       |
| `u`  | 使用 unicode 码的模式进行匹配。                             |
| `y`  | 执行“粘性 (`sticky`)”搜索，匹配从目标字符串的当前位置开始。 |

#### [使用带有 sticky 标志的正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#使用带有_sticky_标志的正则表达式)

* 也就是控制正则从目标字符串匹配的开始位置

带有[`sticky`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/sticky)标志的正则表达式将会从源字符串的[`RegExp.prototype.lastIndex`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/lastIndex)位置开始匹配，也就是进行“粘性匹配”。

```js
let str = '#foo#'
let regex = /foo/y

regex.lastIndex = 1
regex.test(str)      // true
regex.lastIndex = 5
regex.test(str)      // false (lastIndex is taken into account with sticky flag)
regex.lastIndex      // 0 (reset after match failure)
```

#### [sticky 标志和 global 标志的不同点](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#sticky_标志和_global_标志的不同点)

如果正则表达式有粘性 `y` 标志，下一次匹配一定在 `lastIndex` 位置开始；如果正则表达式有全局 `g` 标志，下一次匹配可能在 `lastIndex` 位置开始，也可能在这个位置的后面开始。

```typescript
re = /\d/y;
while (r = re.exec("123 456")) console.log(r, "AND re.lastIndex", re.lastIndex);
```

* 上面y和g表现不一致，因为g可能会跳过空格。y是严格执行递增，g是递增到下一个匹配的位置。

### 使用括号的子字符串匹配

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_expressions#%E4%BD%BF%E7%94%A8%E6%8B%AC%E5%8F%B7%E7%9A%84%E5%AD%90%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%8C%B9%E9%85%8D

#### 捕获括号

`(x)`

#### 非捕获括号

匹配 'x' 但是不记住匹配项。这种括号叫作*非捕获括号*

[`(?:x)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_expressions#special-non-capturing-parentheses)

### [使用正则表达式和 Unicode 字符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#使用正则表达式和_unicode_字符) —— 扩展正则的能力

有一个问题，`\w` 或 `\W` 只会匹配基本的 ASCII 字符；如 `a` 到 `z`、 `A` 到 `Z`、 `0` 到 `9` 及 `_`。如果想要匹配其他国家的文字，要使用 `\uhhhh`，`hhhh` 表示以十六进制表示的字符的 Unicode 值。

```js
let text = "Образец text на русском языке";
let regex = /[\u0400-\u04FF]+/g;

let match = regex.exec(text);
console.log(match[0]);  // prints "Образец"
console.log(regex.lastIndex);  // prints "7"

let match2 = regex.exec(text);
console.log(match2[0]);  // prints "на" [did not print "text"]
console.log(regex.lastIndex);  // prints "15"

// and so on
```

[Unicode 属性转义特性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_expressions/Unicode_property_escapes)引入了一种解决方案，它允许使用像\p{scx=Cyrl}这样简单的语句。这里有一个外部资源，用来获取 Unicode 中的不同区块范围：[Regexp-unicode-block](http://kourge.net/projects/regexp-unicode-block)

### js 正则

#### 使用正则

正则表达式可以被用于 [`RegExp`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp) 的 [`exec`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec) 和 [`test` (en-US)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test) 方法以及 [`String`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String) 的 [`match` (en-US)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match)、[`replace`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace)、[`search` (en-US)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/search) 和 [`split` (en-US)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split) 方法。

#### match 方法

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/match

* match 方法会匹配从外到里所有的规则（如果有括号的话，外层匹配完还去输出括号内的匹配）

```typescript
let str = 'test/str/test2/index'
str.match(/test\/str\/(.+)\/index/)

// 输出
['test/str/test2/index', 'test2', index: 0, input: 'test/str/test2/index', groups: undefined]

```



#### replace 方法

不会改变原值的方法。

* 奇淫技巧todo

```tsx
'18888888888'.replace(/^(\d{3})(\d*)(\d{4})/, '$1-$2-$3')
// 188-8888-8888，一个括号匹配一个，同上
```

使用 $1 这种方式，相当于是匹配，直接返回 pattern 匹配的字符串，而不是替换，不会改变原值。

#### 转义符号

* js 中的转义符号不需要转义，能够在正则中直接使用



### 匹配路径

* js 中字符串中的路径可以为 `'\'`, `'/'` 

* 匹配规则

  ```typescript
  /[\\/]/
  // 第一个 \ 是转义
  ```

  

### js中的与或非

* 与：

  1. 可以用 `[]` 来实现，比如包含 a或b

  ```js
  /[ab]/.test('a')
  ```

  2. 可以使用 `|` 来实现，但是这种方式会使用到 （），可能匹配出多余字符

  ```typescript
  /(a|b)/.test('a')
  ```

  3. 也可以使用非捕获括号来消除这种影响

  ```typescript
  /(?:a|b)/.test('a')
  ```

  

* 非：同样可以使用`[]`来实现。`[^ABC]`表示匹配除了 `[...]` 中字符的所有字符

  ```js
  /[^ABC]/.test('A') // 非ABC
  ```

  如果是非一个正则呢？也就是不匹配一个给定的正则。使用 `?!a`
  
  ```js
  // 需要使用 (?!a)
  /^(?!^<([a-z]+[1-6]?)>)/.test('<div>')
  ```

## 词法作用域

* JavaScript 是词法作用域，与之对立的是动态作用域，问题来源：https://github.com/MuYunyun/blog/issues/2

  todo了解



## js 表达式和运算符

### 加法

* 如果有一方是字符串，另一方则会被转换为字符串，并且它们连接起来
* 如果双方都是 [BigInt](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigInt)，则执行 BigInt 加法。如果一方是 BigInt 而另一方不是，会抛出 [`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError)。
* 否则，双方都会被[转换为数字](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number#number_强制转换)，执行数字加法。

### 转换为数字

相当于直接调用 `Number()`



# webapi

## children 和 childNodes 有啥区别呢？

https://stackoverflow.com/questions/7935689/what-is-the-difference-between-children-and-childnodes-in-javascript

* 可以打印一下，会发现 childNodes 元素是多余 children 元素的

  childNodes 元素主要返回 Node 类型，Node 类型包含 Text 文本节点

  children 返回的都是 Element 类型，返回的都是标签节点

## 怎么知道标签类型呢？

* web 提供了两种方式去获取节点类型

  1. node.nodeType，在node类型的节点上提供了nodeType属性，能够区分不同类型的节点，比如元素、文本和注释

     参考：https://developer.mozilla.org/zh-CN/docs/Web/API/Node/nodeType

  2. Element.tagName，这个属性是挂载到 Element 上面的，这个属性能够获取标签名称，更具体一些

     参考：https://developer.mozilla.org/zh-CN/docs/Web/API/Element/tagName

## 向页面添加元素

### document.write

> **`Document.write()`** 方法将一个文本字符串写入一个由 [`document.open()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/open) 打开的文档流（document stream）。
>
> **备注：** 因为 `document.write` 需要向文档**流**中写入内容，所以，若在一个已关闭（例如，已完成加载）的文档上调用 `document.write`，就会自动调用 `document.open`，[这将清空该文档的内容 (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/Document/open#notes)。

* 如果文档未加载完成，能够给页面添加元素，并且不会被替换页面内容
* 如果文档加载完成，则会自动调用document.open()(会清空页面)，然后执行 write 方法

### document.documentElement.insertAdjacentHTML

* 能够方便的添加html string 元素，但是添加的script不会被执行

### document.appendChild

* 不够灵活

## 四种 observer

浏览器提供了四种 observer，能够对观察者，能够对浏览器中的一些元素变化、性能、视图交叉进行监听，做了一些性能上的优化，提供开发者使用。

参考：https://juejin.cn/post/6844903976937209863

|      | IntersectionObserver                                         | MutationObserver                                             | ResizeObserver                                     | PerformanceObserver                                          |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------------------------------------------------- | ------------------------------------------------------------ |
| 用途 | 观察一个元素是否在视窗可见                                   | 观察DOM中的变化                                              | 观察视口大小的变化                                 | 监测性能度量事件                                             |
| 方法 | observe()  disconnect()   takeRecords()                      | observe()  disconnect()  takeRecords()  unobserve()          | observe()  disconnect()  unobserve()               | observe()  disconnect()  takeRecords()                       |
| 取代 | Dom Mutation events                                          | getBoundingClientRect() 返回元素的大小及其相对于可视窗口的位置      Scroll 和 Resize 事件 | Resize 事件                                        | Performance 接口                                             |
| 用途 | 1. 无限滚动  2. 图片懒加载  3. 兴趣埋点   4. 控制动画/视频执行（性能优化） | 1. 更高性能的数据绑定及响应  2. 实现视觉差滚动    3. 图片预加载   4. 实现富文本编辑器 | 1. 更智能的响应式布局（取代@media）  2. 响应式组件 | 1. 更细颗粒的性能监控  2. 分析性能对业务的影响（交互快/慢是否会影响销量） |



## clipboard api

参考：https://developer.mozilla.org/zh-CN/docs/Web/API/Clipboard_API

### 把图片写入到粘贴板中

https://developer.mozilla.org/zh-CN/docs/Web/API/ClipboardItem

ClipboardItem 能够将文件转换为一个 dataTransfer 对象

```typescript
new ClipboardItem({
  [blob.type]: blob
})
```

然后就可以写入粘贴板了

```typescript
async function writeClipImg() {
  try {
    const imgURL = '/myimage.png';
    const data = await fetch(imgURL);
    const blob = await data.blob();

    await navigator.clipboard.write([
      new ClipboardItem({
        [blob.type]: blob
      })
    ]);
    console.log('Fetched image copied.');
  } catch(err) {
    console.error(err.name, err.message);
  }
}
```



# 名词

## URI

* uniform resource identifier

  统一资源标识符：更像一种协议，抽象定义

* uniform resource locator 统一资源定位符

* uniform resource name （URN）：统一资源命名符



# 开发实践

## 1. 日志相关

### console.log

* 能够识别 `\n` ，可以在做日志的时候进行换行

  ```js
  console.log('1da\ndwada')
  ```

* 或者直接使用 模板字符串

  ```js
  console.log(`
  	111
  	222
  `)
  ```

#### 加颜色——加样式

* 参考：https://developer.mozilla.org/zh-CN/docs/Web/API/Console

可以使用 `%c` 为打印内容定义样式：

```
console.log("This is %cMy stylish message", "color: yellow; font-style: italic; background-color: blue;padding: 2px");
```

指令前的文本不会受到影响，但指令后的文本将会使用参数中声明的 CSS 样式。

* %c 后面跟着的第二个参数字符串就是样式，如果不写则%c会被当成字符串本身。如果写了其他非样式字符串，则该字符串不起任何作用

```js
console.log('%c我是测试')
// %c我是测试

console.log('%c我是测试', 'testtest')
// 我是测试
```

#### 加编组

* 由里到外匹配组

```typescript
console.log('start')
console.group('%cgroup1', 'color:red;') // 甚至可以给分组加颜色
console.log('dwad')
console.group('group2')
console.log('dwadawdaawq')
console.groupEnd()
console.log('qqqqq')
console.groupEnd()
```

### 打印函数调用堆栈

```typescript
function foo() {
  function bar() {
    console.trace('%ctest stack', 'color:red');
  }
  bar();
}

foo();
/**
	输出：
	react_devtools_backend.js:4026 test stack
  overrideMethod	@	react_devtools_backend.js:4026
  bar	@	VM1728:3
  foo	@	VM1728:5
  (anonymous)	@	VM1728:8
*/
```





#### 第三方工具

colors.js https://www.npmjs.com/package/colors

### 日志函数名称

* 

## 2. 错误捕获

* 一个完美的错误闭环，能够捕获到所有的错误

```typescript
window.onerror = () => {
    console.log(666)
}

window.Error = new Proxy(window.Error, {
    get: function(target, name) {
        console.log(111)
        if (name === 'prototype') {
          return Object.prototype;
        }
        return 'Hello, ' + name;
      },
    
      apply: function(target, thisBinding, args) {
          console.log(222)
        return args[0];
      },
    
      construct: function(target, args) {
          console.log(333)
        return {value: args[1]};
      }
})

window.onerror = () => {
    console.log(666)
}
```

* 只需要一个 window.onerror ＋ unhandlerejectederror 即可实现

  一般我们不会简单的这样做，因为 onerror 并不可靠！

> 全局 `window.onerror` 事件不可靠，因为它在一些旧浏览器中不提供堆栈信息，而这些堆栈信息对我们理解错误是如何产生的至关重要
>
> https://cn.redux.js.org/understanding/history-and-design/middleware

## 编程范式

 OOP （面向对象编程）、FP （函数式编程）和 FRP （函数响应式编程）。

todo 单独一章去学习

### IIFE

* 立即调用函数在前端领域也有广泛的用处。

  * 能够作为一个模块，内部的局部变量不会被暴露
  * 全局只能执行一次，没有暴露全局属性，不能再调用第二次。作为一个全局注入的场景非常有用。

  例子：typescript 在运行时注入的 `reflect-metadata`



## 复制图片上传的服务器处理&复制图片到粘贴板

https://www.zhangxinxu.com/wordpress/2023/09/js-copy-image-clipboard/

* 主要难点是浏览器不支持复制 JPG 图片到粘贴板
  * 可以通过 canvas 进行复制
  * 使用 fileReader 转换为 base64 图片
* 构造 clipboardItem 对象

### canvas 复制图片

```typescript
/**
 * 主要是canvas绘制图片
 */
declare let img: HTMLImageElement
const canvas = document.createElement('canvas')
canvas.width = 100
canvas.height = 100
const context = canvas.getContext('2d')!
context.drawImage(img, 0, 0, 100, 200)
// 把canvas读取为 blob
canvas.toBlob(blob => {
  // 当成 Blob 对象进行处理
})
```



# 哲学

## 1. 渐进增强和优雅降级

> 渐进增强 progressive enhancement：针对低版本浏览器进行构建页面，保证最基本的功能，然后再针对高级浏览器进行效果、交互等改进和追加功能达到更好的用户体验。

> 优雅降级：在网站设计时，术语优雅降级指的是新的或者是复杂特点的明智实施，目的是确保大部分的因特网使用者可以有效的和站点上的页面交互。过去几年站点设计和因特网使用的重要的里程碑包括图片，帧，在线游戏，Java, JavaScript, ActiveX控制，浏览标签，因特网上的语音通话(VoIP)和视频会议技术的引入。当浏览器或操作系统的更新版本发布时，它们经常包含新的特征来保持和因特网功能的最新增强的同步。因为各种各样的原因，许多因特网使用者喜欢使用他们已有的浏览器而不是每当一个新的Web站点技术流行时，立即更新到最新版本。当一个站点被有意识设计成有优雅降级的特点时，这些使用者不会突然被强迫升级他们的浏览器除非他们正在使用“古董”。
>
> 优雅降级是因某些新发布的CSS样式或HTML标签在老的浏览器上不兼容，而在写代码时做的了一定的处理，确保在浏览器不兼容时，也能够达到原效果或部分原效果。

### 渐进增强

参考：https://www.cnblogs.com/analyzer/articles/1375464.html
