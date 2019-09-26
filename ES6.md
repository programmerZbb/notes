# ES6 学习笔记

* ES6是为了使得JavaScript 语言可以用来编写复杂的大型应用程序，成为企业级开发语言。
* ECMAScript 和 JavaScript 的关系是，前者是后者的规格，后者是前者的一种实现（另外的 ECMAScript 方言还有 Jscript 和 ActionScript)
  ##ES6开发环境的搭建
* 有的浏览器不支持ES6，因此为了兼容在上线需要搭建环境，将项目放在生产环境当中。

## 起步

* 新建两个文件夹，一个src，编写的ES6代码全都放到这个文件夹下。另一个dist文件夹(distribution 发布的意思，要发布的版本)。

    ==注意== ：在html文件中引入script文件时，要引入dist文件下的js文件。

* 在vscode中control+· 可以快速切换终端

* 在html文件中引入js文件引入的是dist下的文件（相应的转化之后的文件）

* 安装babel转化工具，webpack也可以转化。
    ```shell
    npm install -g babel-cli
    ```
    cli代表命令行，说明我们安装的是命令行
    安装完成之后还需要本地安装一下，方便开发使用
    ```shell
    npm install --save-dev babel-preset-es2015 babel-cli
    ```
    配置`.babelrc`文件（在代码中）
    在命令行执行`babel src/index.js -o dist/index.js`
    把src下的index.js -0（输出）到dist下的文件

* --save是上线是的依赖，--save-dev是开发的时候使用的一些工具依赖，上线的时候不需要使用。
    -dev 代表开发系统

* 我们不想重复执行上述命令可以通过修改`package.json`文件中的scripts属性的值来实现简化操作。

    ![屏幕快照 2018-07-11 下午2.08.26](/Users/zhangbinbin/workspace/note/picture/屏幕快照 2018-07-11 下午2.08.26.png)

    变化为：

    ![屏幕快照 2018-07-11 下午2.10.08](/Users/zhangbinbin/workspace/note/picture/屏幕快照 2018-07-11 下午2.10.08.png)

    直接通过`npm run build`指令就可以实现转化

## 语法

字面理解ES6的三种声明方式：

1. var：它是variable的简写，可以理解成变量的意思。
2. let：它在英文中是“让”的意思，也可以理解为一种声明的意思。
3. const：它在英文中也是常量的意思，在ES6也是用来声明常量的，常量你可以简单理解为不变的量。

 为了减少全局变量污染的问题，在循环中和function中最好使用`let`命名变量。

const为常量命名，命名之后不能再改变。

let是为了ES6中新增的块级作用域。在ES6中只要存在`{}`都是块级作用域，ES5只有全局和函数作用域。

**注意** ：for循环中`()`是fu父级作用域，`{}`为子作用域。

```javascript
for (let j=0;j<3;++j) {
    let j = 'abc'
    console.log(j)
}
```

总的来说，就是父级作用域能够传递到子作用域，子作用域不能传递到父作用域。

=== ES6 引入了块级作用域，明确允许在块级作用域之中声明函数。 ===

浏览器环境下：

- 允许在块级作用域内声明函数。

- 函数声明类似于`var`，即会提升到***全局作用域或函数作用域的头部***。

- 同时，函数声明还会提升到所在的块级作用域的头部。

  ![屏幕快照 2018-07-11 下午4.19.51](/Users/zhangbinbin/workspace/note/picture/屏幕快照 2018-07-11 下午4.19.51.png)

  上图中执行的结果是1111

其他环境下：

​	function类似于let语法

***注意：***

考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。

 ![屏幕快照 2018-07-11 下午4.43.52](/Users/zhangbinbin/workspace/note/picture/屏幕快照 2018-07-11 下午4.43.52.png)

### const

`const`声明一个只读的常量。一旦声明，常量的值就不能改变。

const 一旦声明变量，就必须立即初始化，不能留到以后赋值

const于`let`要注意的使用规则相同。

* `const`实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址不得改动。

* `const`定义复杂类型的数据保存着该对象的地址，不能够对变量再赋值其他复杂类型的数据，但是能够修改该复杂类型的数据。

  将一个对象声明为常量必须非常小心。

  如果需要将对象冻结，应该使用`Object.freeze`方法。

  ```javascript
  const foo = Object.freeze({})
  ```

  如上，严格模式下，foo将无法再添加数据。

ES5 只有两种声明变量的方法：`var`命令和`function`命令。ES6 除了添加`let`和`const`命令，后面章节还会提到，另外两种声明变量的方法：`import`命令和`class`命令。所以，ES6 一共有 6 种声明变量的方法。

### 顶层对象的属性

ES6为了改变window顶层对象的属性与全局变量挂钩的问题，一方面规定，为了保持兼容性，`var`命令和`function`命令声明的全局变量，依旧是顶层对象的属性；***另一方面，`let`命令、`const`命令、`class`命令声明的全局变量，不属于顶层对象的属性***。

### 解构赋值

* 应用在前后端分离的情况，后端传回了json字符串，我们将数据循环输出到每个变量上，用解构非常方便。
* 适合于数组和对象的批量给变量赋值。
* 解构赋值时 一定要注意： 先从外边大结构开始，出现等号就一层一层往里面解析。

#### 数组的解构赋值

需要注意 的情况：

```javascript
let [a,...t] = [1,2,3]
console.log(a);
console.log(t);
```

输出a为1，t为[2,3]，***只要带…的变量获取到的都是数组，不会出现undefined的情况，最差是一个空数组***

********

```javascript
let [a, ,d] = [1,2,3,5]
console.log(a);
console.log(d);
```

a为1，d为3

```javascript
let [a,[b,c],d] = [1,2,3]
console.log(a);
console.log(b);
console.log(c);

```

上述情况会报错，因为左右解构不一样

***注意： 解构赋值允许变量对应不一样的结构，变量可以赋值为数组，不予许数据对应不一样的变量，变量必须有值，才会不报错***

* 只要出现变量的值为undefined，就是解构不成功

***事实上，只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。***

详见后面的`Iterator`和`set`？？？（set也可以解构赋值？）

##### 带默认值

注意，ES6 内部使用严格相等运算符（`===`），判断一个位置是否有值。所以，只有当一个数组成员严格等于`undefined`，默认值才会生效。

```javascript
let [a=1] = [null]
```

a的值为null

#### ==注意==

* 数组的扩展运算符的使用有两种情况

  1. 已定义的数组去赋值

     已定义的数组参与赋值的话，会被解析成以 `,` 分开的序列，分成一个一个的元素。

     ```js
     let arr = [1,2,3]
     let b = [1,2,4,...arr,5]
     fn(...arr)
     ```

     也能在函数的参数中使用（但是不能在其他的`()`中使用）

  2. 未定义的数组给自己赋值

     ==只能写在数组的最后面，默认为空数组==

#### 对象的解构赋值

* 数组的解构赋值不需要按位置对齐，只需要匹配相应的属性就可以。

* ***对象的结构赋值没有顺序，只需要属性对应就可以***

  ```javascript
  let {foo: name} = {foo: 'zbb'}
  ```

  实际赋值给了name变量（name不需要加引号，个性化定制），如果不写name则赋值给了变量foo。

  foo是匹配的模式，name才是变量。

* 嵌套形式的解构赋值

```javascript
let obj = {
    p: [
        'hello',
        {y: 'world'}
    ]
}
let {p: o} // o的值为p表示的数组

let {p: [x]} // x的值为'hello'

let {p: [x, y]}
```

重点要看给变量赋的值是否合理。

```javascript
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};

let { p, p: [x, { y }] } = obj;
x // "Hello"
y // "World"
p // ["Hello", {y: "World"}]
```

上述代码中，p的赋值是因为左边找属性为p的元素，一个p就代表属性为p的元素。

***这就是对象赋值强大之处，出现一个属性好几次，分别给不同的属性赋值***

只有对象解构赋值的情况（必须包含在{}里面），左边的规模才可以‘大于’右面的规模\

##### 带默认值

* 只有对象的属性没有赋值和严格等于`undefined`(===)才会赋值为默认值

```javascript
let {x, y = 2} = {x: 'zbb'}
```

`{}`不能够放在一行的行首，es6会将它作为一个代码块来看待。因此需要用（）把类似代码块包起来。

数组是特殊的对象，可以进行如下的解构赋值

```javascript
let arr = [1, 2, 3]
let {0: a} = arr

```

a的值为1。

***由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构。***

#### 字符串的解构赋值

* 字符串在解构赋值时，被转化成一个类似数组的对象。

```javascript
let {length : len} = 'hello'
//length的值为5
```

#### 数值和布尔值的解构赋值

* 解构赋值时，如果等号右边是数值和布尔值，则会先转化为对象。
* 解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于`undefined`和`null`无法转为对象，所以对它们进行解构赋值，都会报错。

#### 函数参数的解构赋值

* 函数的参数也可以使用解构赋值。

```JavaScript
function add ([x,y]) {
    return x + y;
}
add([1, 2]);
```

#### 不能够使用圆括号的问题

1. 变量声明语句

```javascript
let (a) = 1;
```

* 不能够出现let等声明的语句。

2. 函数的参数

* 函数的参数属于变量的声明

```javascript
function add ((a)) {}
```

3. 赋值语句的模式不能用括号

```javascript
let ([a,b]) = [1,2]
```

* 注意：赋值语句的变量是可以使用括号的

***事实上，只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。***

map和set的解构赋值？？？

### 字符串的扩展

#### 1. 字符串的 Unicode 表示法

字符串的采用`\uxxxx`的表示方法只能表示`\u0000~\uFFFF`之间的字符，超出这个范围必须使用两个双字节的形式表示。

超出范围，JavaScript只能使用双字节表示，ES6规定使用大括号就可以正确表示超出范围的字符。

例：

```javascript
"\uD8422"   // \u 代表转义
```

下面的两个方法都是解决charCode的问题。

#### 2. codePointAt() 方法

JavaScript 内部，字符以 UTF-16 的格式储存，每个字符固定为个字节。

一般字符可以使用`charCodeAt()`方法来获取字符的码点，超出范围（4个字节表示的字符）的字符需使用`codePointAt()`方法来获取码点，`charCodeAt()`方法拆分为两个字符只能获取前两个字节和后两个字节。

charCode 和 codePoint 都需要传入参数数字来获取对应的码点。

#### for...of 方法

```javascript
let arr = [
    [1,2]
];
for(let [a,b] of arr) {
    console.log(a,b);
}
```

for...of方法能够使用解构赋值的方式通过类数组的对象给变量赋值。

具有 Iterator 接口的对象都可以使用 for...of 来实现遍历。

#### 3. String.fromCodePoint()方法

ES5 提供了 String.fromCharCode() 方法，用于从码点返回字符。这个方法不能识别32位的UTF-16 字符（Unicode编号大于 0xFFFF）。

ES6为超出范围的字符提供了方法`String.fromCodePoint()`方法，能够识别大于`0xFFFF`的字符，正好与charPointAt()方法相反。

* 与原有的方法相比，ES6就是把 code 提前，char 换成了 point 

注意： 注意，`fromCodePoint`方法定义在`String`对象上，而`codePointAt`方法定义在字符串的实例对象上。

#### 4. 字符串的遍历器接口

* ES6 为字符串添加了遍历接口。可以使用 for...of 方式进行遍历，遍历为一个一个的字符，也可以识别超出UTF-16的字符。

#### 5.at()方法

该方法返回的是字符，上面的方法返回的是码点。

ES5提供了 charAt() 方法来获取字符串的某个字符，目前还没有实现。

#### 6. includes(), startsWith(), endsWith() 方法

三个方法都返回布尔值，判断字符是否在字符串的相应位置。

#### 7. repeat()方法

将字符串重复几次。

#### 8. padStart() 和 padEnd()

在字符串的前面或后面补充字符串

参数：

1. 补齐之后的字符串长度
2. 补充的字符串。（多割少补）

#### 9. 模板字符串

使用反引号 `` 来包含字符串，可以换行，加变量(${}的形式)。    

模板字符串会按字符串的原样式输出，因此在innerHTML中应用换行会加一个 <br>

如果大括号中的值不是字符串，将按照一般的规则转为字符串。比如，大括号中是一个对象，将默认调用对象的`toString`方法。

#### 10. 标签模板

fn(\`aaaa$(a)`) 的形式调用参数会把参数直接当做一个字符串处理；

Fn\`aaa$(a)`的形式调用会把参数当成标签模板来处理，第一个参数为字符串数组（最后一个字符是变量，不是一般字符会自动加一个空字符），第二个、第三个参数……为模板中的变量。

#### 11. String.raw() 方法

该方法会解析模板字符串并返回。

第一个参数可以传递一个 {raw: 'test'} ，会穿插后面的字符串。

### 正则

#### 1. 构造函数

ES5 ： new RegExp('xyz', 'i')

​	    new RegExp(/xyz/i)

ES6新增： new RegExp(/abc/ig, 'i')   (后面的会覆盖前面的修饰符)	

#### 2. u 修饰符

添加 u 修饰符之后会识别字符串中的“Unicode 模式”的字符串。

使用“Unicode 模式”的字符串或者匹配大于’0xFFFF‘的字符串具体规则，查看文档。

### 函数扩展

##### 1. 默认值

```javascript
function foo({x, y = 5} = {}) {
    console.log(x, y);
}
foo();
```

上述代码中 foo 参数的默认值是 {} ，y的默认值是 5。***这样不仅可以默认 foo 函数有参数，而且可以给内部的变量默认值***

```javascript
// 写法一
function m1({x = 0, y = 0} = {}) {
  return [x, y];
}

// 写法二
function m2({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
```

注意上述两种写法的区别，第二种写法只要函数传入对象参数之后，右边的默认值就失效了。第一种方法还会继续解构赋值下去。

* 有默认值的参数最好写在尾参数。如果默认的参数不传值，后面的参数也不能传值。除非默认的参数传入undefined，因为只有不传值和undefined能触发传递默认值。

```javascript
function f(x, y = 5, z) {
  return [x, y, z];
}
f(1, 1, 2);
```

##### 2. 函数的 length 属性

函数的 length 属性将返回有默认值的参数之前没有添加默认值的参数的个数。

```javascript
(function (a, b=5, c) {}).length //值为1
```

##### 3. 作用域

一旦设置了参数的默认值，函数进行声明初始化时，参数会形成一个单独的作用域（context）。等到初始化结束，这个作用域就会消失。

```javascript
var x = 1;

function f(x, y = x) {
  console.log(y);
}

f(2) // 2
```

函数初始化的时候，参数（） 相当于 {}。

##### 4. 函数默认参数的应用

利用参数默认值，可以指定某一个参数不得省略，如果省略就抛出一个错误。

```javascript
function throwIfMissing() {
  throw new Error('Missing parameter');
}

function foo(mustBeProvided = throwIfMissing()) {
  return mustBeProvided;
}

foo()
// Error: Missing parameter
```

##### 5. 制定抛出什么异常

使用 throw new Error('异常信息')。

##### 6. rest 参数

`...变量名` 能够获取函数的多余参数，rest参数是一个数组。

rest 参数之后不能再有其他的参数（即只能是最后一个参数），否则会报错。

函数的 length 属性也不包括 rest 参数。

##### 7. name属性

函数的 name 属性会返回函数的名称，赋值形式的函数会返回实际函数的名称（若为匿名函数，则返回左边的名称）。

##### 8. 箭头函数

`=>`

多行的箭头函数使用`{}`包括起来。

```javascript
let fn = (a,b) => {a = a + b; return a;}
```

箭头函数只能通过赋值的形式来定义，不能够直接定义函数名。

* 返回一个对象要使用 （） 括起来，不然会解析为一个语句，直接执行该语句，不会返回任何值。

***注意：***

1. 函数体内的this 对象，就是定义时所在的对象，而不是使用时所在的对象。（一定是指向的对象，没有对象就指向全局）

   原因是箭头函数内部没有this 就会指向最外层的 对象。

   例子:

   ```javascript
   var handler = {
     id: '123456',
   
     init: function() {
       document.addEventListener('click',
         event => this.doSomething(event.type), false);
     },
   
     doSomething: function(type) {
       console.log('Handling ' + type  + ' for ' + this.id);
     }
   };
   ```

   上述代码中，箭头函数中的 this 指向了 handler 而不是调用时使用的 document 对象。指向最外层的对象。

2. 没有 arguments 对象，指向最外层的  arguments 对象。

3. 不能当做构造函数使用，没有 this ，也不能使用 call 和 apply 方法。

* 如果箭头函数内部是一个结构，比如说是返回一个对象或者是一个函数，都需要使用（）将其包围起来。

##### 9. 尾调用

函数在最后一步骤调用。

下面的都不属于尾调用：

```javascript
// 情况一
function f(x){
  let y = g(x);
  return y;
}

// 情况二
function f(x){
  return g(x) + 1;
}

// 情况三
function f(x){
  g(x);
}
//后面 有 return；
```

* 尾调用优化

  在内层函数不再用到外层函数的内部变量的时候，内层的调用帧会取代外层的调用帧。节省内存。

我们知道，函数调用会在内存形成一个“调用记录”，又称“调用帧”（call frame），保存调用位置和内部变量等信息。如果在函数`A`的内部调用函数`B`，那么在`A`的调用帧上方，还会形成一个`B`的调用帧。等到`B`运行结束，将结果返回到`A`，`B`的调用帧才会消失。如果函数`B`内部还调用函数`C`，那就还有一个`C`的调用帧，以此类推。所有的调用帧，就形成一个“调用栈”（call stack）。

#### 数组的扩展

###### 1. 扩展运算符

`...a`

扩展运算符可以展开为数组，与rest 参数是逆运算。函数可以通过接受数组的形式接受参数。

```javascript
function fn(...args) {
    
}
let args = [1,2,3];
fn(...args);
```

* 通过扩展运算符可以取代 aplly () 方法实现对数组的操作。

扩展运算符的应用：

克隆数组。

扩展运算符只能放在数组的最后一位！

* 将字符串转化为真正的数组

扩展运算符只能在有 Iterator 接口的对象中使用。

###### 2.Array.from()

 所谓类似数组的对象，本质特征只有一点，即必须有`length`属性。因此，任何有`length`属性的对象，都可以通过`Array.from`方法转为数组，而此时扩展运算符就无法转换。

* 能够转换没有 Iterator 接口的类数组对象。
* 对于没有部署该方法的浏览器只能通过 Array.prototype.slice.apply 来实现
* 第二个参数能够对数组中的每一个元素进行处理，然后返回处理后的数组。

除了强制转化为数组，该方法可以分别对每个元素进行处理。

```javascript
let arr = Array.from([0,1,2], x=>{if(x>0){return x = 2} return x});
console.log(arr);
```

###### 3. Array.of()

将一组值转换为数组。返回一个数组。

###### 4. 数组实例的copyWithin() 方法

在当前数组的内部拷贝，并指定覆盖。

###### 5. 数组实例的 fill() 方法

填充数组。

##### in 运算符

判断数组或对象中是否有该属性。

###### 6. 数组中的空位

尽量避免出现空位，处理不一样，有的会转换为 undefined。

#### 对象的扩展

##### 属性表达式

es6 属性名能使用 ["a"+"b"] 方括号的形式。

方法名也可以使用 ["a"] 的形式

```js
["a"] () {


}
```



* super 关键字，指向当前对象的原型对象。

##### Object.assign()

* 使用该方法能够轻松的实现对对象的合并

##### 对象的新增方法

* Object.keys() 方法

  该方法能够遍历并返回对象自身（不含继承的）所有的（可遍历 enumerable）属性键名。返回一个数组

  ```js
  var obj = { foo: 'bar', baz: 42 };
  Object.keys(obj)
  // ["foo", "baz"]
  ```

* Object.values() 方法

  该方法能够遍历并返回对象自身（不含继承的）所有的（可遍历 enumerable）属性键值。返回一个数组

* Object.entries() 方法

  返回一个数组，成员是参数自身的（不含继承的）所有可遍历属性的键值对数组。

#### 解决异步编程的问题

所谓"异步"，简单说就是一个任务不是连续完成的，可以理解成该任务被人为分成两段，先执行第一段，然后转而执行其他任务，等做好了准备，再回过头执行第二段。

比如，有一个任务是读取文件进行处理，任务的第一段是向操作系统发出请求，要求读取文件。然后，程序执行其他任务，等到操作系统返回文件，再接着执行任务的第二段（处理文件）。这种不连续的执行，就叫做异步。

相应地，连续的执行就叫做同步。由于是连续执行，不能插入其他任务，所以操作系统从硬盘读取文件的这段时间，程序只能干等着。同步必须按顺序一下执行完成。

传统的编程语言，早有异步编程的解决方案（其实是多任务的解决方案）。其中有一种叫做"协程"（coroutine），意思是多个线程互相协作，完成异步任务。 

”协程“的运行流程：

- 第一步，协程`A`开始执行。
- 第二步，协程`A`执行到一半，进入暂停，执行权转移到协程`B`。
- 第三步，（一段时间后）协程`B`交还执行权。
- 第四步，协程`A`恢复执行。

 上面流程的协程`A`，就是异步任务，因为它分成两段（或多段）执行。



以下的任何一种解决异步的方法都是为了异步编程的顺序。回调地狱。

#### Generator 函数的语法

* Generator ： 生成器

* 形式上 Generator 就是一个普通的函数

  有两个特征：

  1. function 关键字与函数名之间有一个星号
  2. 函数体内部使用 yield （“产出”）表达式，定义不同的内部状态。

```javascript
function* helloGenerator() {
    yield 'hello';
    yield 'world';
    return 'ending'
}
```

function 后面跟星号（*）。

上述函数有三个状态：hello， world， return语句。

Generator 的使用方法与普通的函数一样。

* 调用 Generator 函数，返回一个遍历器对象，代表着 Generator 函数的内部指针。每次调用遍历器对象的 next 方法，就会返回一个有着 `value`和`done`两个属性的对象。value属性表示当前的内部状态的值（yield 后面的表达式），done 是一个布尔值，表示是否遍历结束。

* `yield`表达式与`return`语句既有相似之处，也有区别。相似之处在于，都能返回紧跟在语句后面的那个表达式的值。区别在于每次遇到`yield`，函数暂停执行，下一次再从该位置继续向后执行，而`return`语句不具备位置记忆的功能。

* yield 只能够用在 Generator 函数中，在普通的函数和单独使用都会报错。

  yield 在语句中使用，与另一个表达式结合，必须使用（）包括。

  ```javascript
  function* demo() {
      console.log('hello' + (yield)); // 与其他的语句结合，必须要使用 () 包括起来
  }
  ```

  * 调用Generator 函数不会返回执行的结果。返回的是指针对象，每次调用 next() 方法会执行遇到的 yield 语句。调用 next() 方法会返回一个对象， value 为 yield 后面执行的结果。
  * next() 方法中传递的参数，会作为上一个 yield 异步任务的返回结果。

yield 用作函数参数或者放在赋值表达式的右边，可以不加括号。

##### Thunk 函数的含义

* ”传名调用“的实现。是一个临时函数，然后再将这个临时函数传入函数体内。

```javascript
function f(m) {
  return m * 2;
}

f(x + 5);

// 等同于

var thunk = function () {
  return x + 5;
};

function f(thunk) {
  return thunk() * 2;
}
```

`fs`模块的`readFile`方法是一个多参数函数，两个参数分别为文件名和回调函数。经过转换器处理，它变成了一个单参数函数，只接受回调函数作为参数。这个单参数版本，就叫做 Thunk 函数。

#### Promise 补充

* ==Promise 新建后就会立即执行内部的代码。==
* resolve() 成功， reject() 失败。 resolve 和 reject 只能够执行一个。

```javascript
let pro1 = new Promise((resolve, reject) => {
    console.log('1')
    resolve("success"); //成功 fulfilled
    reject("error") //失败 rejected
    console.log("hello");
})  //里面传一个函数

pro1.then((res)=>{
    console.log(res);
    console.log('3')
}, (err)=> {
    console.log(err);
})
console.log('2')
```

then 方法中传递的对应的信息。 回调函数都是异步的？

注意上述代码的执行顺序。如果 Promise 中出现语法错误，会跳过错误代码后面的语句，执行log('2')，然后直接执行 then() 方法中的第二个回调函数。

注意 状态的改变不会阻断Promise 中代码的执行，Promise 中的代码会一直执行到尾。

常用场景：

```javascript
let fs = require('fs');
function fsRead () {
    return new Promise ((resolve, reject) => {
        fs.readFile('./image.html', (err, data) => {
            if (err) {
                return reject(err);
            }
            resolve(data.toString());
        });
    });
}

fsRead().then((data) => {
    console.log(data);
    
}, (err) => {
    console.log(err);
});
```

处理 error 的回调函数也是异步的。

一般来说，调用`resolve`或`reject`以后，Promise 的使命就完成了，后继操作应该放到`then`方法里面，而不应该直接写在`resolve`或`reject`的后面。所以，最好在它们前面加上`return`语句，这样就不会有意外。

使用过程：

一个Promise 对象 p1 调用then 方法，处理 p1 的状态改变后的数据，然后返回另一个Promise对象 p2 调用第二个then 方法，处理 p2 的状态改变后的数据。

“Promise 会吃掉错误”：Promise 内部的错误不会影响到 Promise 外部的代码。

catch() 方法也会返回一个 Promise 对象，还可以继续使用 then 。建议使用 catch() 方法接受错误信息。

```javascript
fsRead().then((data)=>{
    console.log(data);
    
}).catch((err)=>{
    console.log(err);
    
}) 
```

注意： catch 方法只能够捕获Promise 对象中的错误，不能够捕获then 方法中的语法错误。

******

#### async 

* 通常叫做 async 函数

* async 函数默认返回一个 Promise 对象

  ```javascript
  async function getA() {
      //如果这里有错误就会被 catch 捕获
      throw new Error('我是错误');
      return '传给 then 中的 res';
  }
  getA().then(res=>{}).catch(e=>{})
  ```

  async 通常和 await 一起使用。

* await 后面跟着一个 Promise 实例，如果不是也会转化为 Promise。

  await 是一个语法糖，不用通过 then 方法就可以拿到 resolve 或者 reject 的参数。await 返回的值就是该参数。

```javascript
let p = new Promise((res, rej)=>{
    resolve("888");
})
async function getA() {
    await p;
    return "aa"; //then 方法中能够接受到的变量
}
getA().then(res=>{}).catch(e=>{})
```

Promise 创建处理就会立即执行。await 加上Promise 对象，就会优先等待该对象的执行，返回执行时的参数，再去执行后面的代码。就是为了省略 then 方法

#### 补充一个 image 对象

New Image() 

属性 src

事件 onload、 onerror两个常用事件。

*************

### Symbol
一个新的基本类型，能够创建一个不相同的值。

```js
let sym = Symbol("test")
let sym1 = Symbol("test")
// sym 不等于 sym1

let obj = {
    // [sym]: "aaaa"
}
// let sym = "test"

obj[sym] = "i am symbol"
obj[sym1] = "i am second"

console.log(obj)
```



## 面向对象

class 趋于 Java

* 子类的构造函数必须执行一次 super 函数。
* new.target 指向当前正在执行的函数。

### 类的注意

* 类中的方法全部是定义在该类的原型对象上面的

  ```javascript
  class A {
      constructor() {
          
      }
      foo() {
          
      }
  }
  
  //相当于
  //A.prototype.foo 方式
  ```
```

  如果想要直接给对象 A 添加直接方法，需要在它的constructor 方法中添加。

  对象 A 的 constructor 方法就是 A.prototype.constructor

* 不存在变量提升

* 不显式写一个 constructor 就会默认生成一个空 constructor 方法

* this 的指向

  单独调用一个 class  中的方法，该方法中如果有 this ，在使用的时候回报错，需要绑定 this。

  * 需要使用到 js 的 bind() 方法

    bind() 与call() 方法区别就是 bind() 回绑定上下文 context 。call() 方法是切换 this。

  例如在外面使用 class 中的 foo 方法：

  this.foo = this.foo.bind(this)

  * 或者使用箭头函数在构造函数中直接定义该方法

* 静态方法

  只可以被类调用，不能够被实例化对象调用。

  ```javascript
  class Foo {
    static classMethod() {
      return 'hello';
    }
  }
  
  Foo.classMethod() // 'hello'
  
  var foo = new Foo();
  foo.classMethod()
  // TypeError: foo.classMethod is not a function
```

  上面代码中，`Foo`类的`classMethod`方法前有`static`关键字，表明该方法是一个静态方法，可以直接在`Foo`类上调用（`Foo.classMethod()`），而不是在`Foo`类的实例上调用。如果在实例上调用静态方法，会抛出一个错误，表示不存在该方法。

   注意，如果静态方法包含`this`关键字，这个`this`指的是类，而不是实例。


### super 关键字

* 可以作为函数和对象使用

1. 作为函数使用

   super 作为函数使用时，代表父类的构造函数。ES6 要求，子类的构造函数必须执行一次 super 函数。

   * super 必须放在第一行。
   * 只有调用了 super 之后才能够使用 this 关键字。

   ```javascript
   class A {}
   
   class B extends A {
     constructor() {
       super();
     }
   }
   
   ```

   

   注意，`super`虽然代表了父类`A`的构造函数，但是返回的是子类`B`的实例，即`super`内部的`this`指的是`B`，因此`super()`在这里相当于`A.prototype.constructor.call(this)`。

   ==在super()执行之后，子类就拥有了父类的变量，不需要再super后面再写属性赋值了。==

2. super 作为对象使用

   `super`作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。

   super 作为对象调用父类的方法时，方法内部的 this 指向当前的子类实例。

   ```javascript
   class A {
     constructor() {
       this.x = 1;
     }
     print() {
       console.log(this.x);
     }
   }
   
   class B extends A {
     constructor() {
       super();
       this.x = 2;
     }
     m() {
       super.print();
     }
   }
   
   let b = new B();
   b.m() // 2
   ```

### 静态属性

* 在 class 内部通过 static 修饰的属性，只能被构造函数来使用，不能被实例来调用。

  相当于  Person.foo = 'aaa'

* 在 class 内部可以写静态属性

  不予许 let a = 1

  但是就可以 static foo = 'aaa'

  因为默认挂在 class 类名上面了。其他形式也必须挂在 类上。

* class 内部还是通过 原型对象和 构造函数来实现的，只是方便程序员使用，称作 语法糖。（就像药片的外衣）

### 注意：

1. 子类想继承父类的方法，直接继承就可以使用。

2. constructor 中可以 在实例化的时候传入值，也可以直接传入固定的值

   this.name = name  或者  this.name = 'zzz'

## 模块

* cmd sea.js / amd require.js

* Umd 为了做一个兼容处理（综合各种规范的处理）

* Esmodule ES6 提供的

  * 如何定义一个模块 （一个JS 就是一个模块）
  * 如何导出模块 （export）
  * 如何引入一个模块 （import）

* ES6 模块相当于整个导出一个接口对象，对象里面又所有的 key，引入的时候，直接在 {} 中写接口的key 值，

  不在{} 中写就相当于 传入了 default

规则：

* 模块功能主要由两个命令构成：`export`和`import`。`export`命令用于规定模块的对外接口，`import`命令用于输入其他模块提供的功能。

* export 可以通过 两种方式导出

  1. 

  1. ```javascript
     export let a = 1;
     export let b = 1;
     ```

  2. 

     ```javascript
     let a = 1;
     let b = 2;
     export {a, b};
     ```

  注意： 

  ```javascript
  // 报错
  export 1;
  
  // 报错
  var m = 1;
  export m;
  ```

  上述两种情况会报错。函数和 class 也一样

  上面两种写法都会报错，因为没有提供对外的接口。第一种写法直接输出 1，第二种写法通过变量`m`，还是直接输出 1。`1`只是一个值，不是接口。正确的写法是下面这样。

  * `export`语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值。

     ```javascript
      export var foo = 'bar';
      setTimeout(() => foo = 'baz', 500);
     ```

    上面代码输出变量`foo`，值为`bar`，500 毫秒之后变成`baz`。

    这一点与 CommonJS 规范完全不同。CommonJS 模块输出的是值的缓存，不存在动态更新。

    export 命令必须出现在全局中，不能再代码块中。

* export  可以输出多个变量或函数，换名称使用 as 

  ```javascript
  export {
  	name as a,
      cname as b
  }
  ```

  输出的多个变量使用对象的形式。

* 引入使用 import 

  使用一个对象来接受。使用解构赋值。

  import 会变量提升。下面的代码不能够重复声明name。

  ````javascript
  import {name} form '相对路径'
  ````

  全部获取

  ```javascript
  import * as obj form '路径'
  ```

注意：

* `import`命令输入的变量都是只读的，因为它的本质是输入接口。也就是说，不允许在加载模块的脚本里面，改写接口。

* 注意，`import`命令具有提升效果，会提升到整个模块的头部，首先执行。

  ```javascript
  foo();
  
  import { foo } from 'my_module';
  
  ```

* 在 import 中加载时，不需要使用 {} 的情况有一种，一种是 export default ， export {a as b}在另一个模块中引入也必须要加 {}。

* export 一个变量 需要加 {}

  export default 一个变量则不需要加 {}

  正是因为`export default`命令其实只是输出一个叫做`default`的变量，所以它后面不能跟变量声明语句。输出函数和对象的时候则可以整体输出。

  ```javascript
  // 正确
  export var a = 1;
  
  // 正确
  var a = 1;
  export default a;
  
  // 错误
  export default var a = 1;
  ```

### 加载规则

* 浏览器对于带有`type="module"`的`<script>`，都是异步加载，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，等同于打开了`<script>`标签的`defer`属性。

* 如果脚本体积很大，下载和执行的时间就会很长，因此造成浏览器堵塞，用户会感觉到浏览器“卡死”了，没有任何响应。这显然是很不好的体验，所以浏览器允许脚本异步加载，下面就是两种异步加载的语法。

  ```html
  <script src="path/to/myModule.js" defer></script>
  <script src="path/to/myModule.js" async></script>
  ```

  `<script>`标签打开`defer`或`async`属性，脚本就会异步加载。渲染引擎遇到这一行命令，就会开始下载外部脚本，但不会等它下载和执行，而是直接执行后面的命令。

  `defer`与`async`的区别是：`defer`要等到整个页面在内存中正常渲染结束（DOM 结构完全生成，以及其他脚本执行完成），才会执行；`async`一旦下载完，渲染引擎就会中断渲染，执行这个脚本以后，再继续渲染。一句话，`defer`是“渲染完再执行”，`async`是“下载完就执行”。另外，如果有多个`defer`脚本，会按照它们在页面出现的顺序加载，而多个`async`脚本是不能保证加载顺序的。

#### 在页面中加载

* 浏览器加载 ES6 模块，也使用`<script>`标签，但是要加入`type="module"`属性。
* 浏览器对于带有`type="module"`的`<script>`，都是异步加载，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，等同于打开了`<script>`标签的`defer`属性。
* 如果网页有多个`<script type="module">`，它们会按照在页面出现的顺序依次执行。
* 模块之中，顶层的`this`关键字返回`undefined`，而不是指向`window`。也就是说，在模块顶层使用`this`关键字，是无意义的。
* 同一个模块如果加载多次，将只执行一次。

*******

* default 

  使用 default 

  export default let a = 'zbb';

  引入模块时，就可以随便定义名字。

  Import haha from '路径'

  这种形式导出的是一个对象。

  export default {arr,str}

  import px from ''

* 浏览器使用模块的时候

  ```javascript
  <script type="module" src="main.js"></script>
  ```


模块的分类：

1. 第三方，node_modules

   不需要相对路径，直接引入

******

### Iterator 和 for...of

一是为各种数据结构，提供一个统一的、简便的访问接口；二是使得数据结构的成员能够按某种次序排列；三是 ES6 创造了一种新的遍历命令`for...of`循环，Iterator 接口主要供`for...of`消费。

遍历器（Iterator）就是这样一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。

iterator 遍历过程：

（1）创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。

（2）第一次调用指针对象的`next`方法，可以将指针指向数据结构的第一个成员。

（3）第二次调用指针对象的`next`方法，指针就指向数据结构的第二个成员。

（4）不断调用指针对象的`next`方法，直到它指向数据结构的结束位置。

`next`方法返回一个对象，表示当前数据成员的信息。这个对象具有`value`和`done`两个属性，`value`属性返回当前位置的成员，`done`属性是一个布尔值，表示遍历是否结束，即是否还有必要再一次调用`next`方法。 done 为 true 时，

* 默认的 Iterator 接口部署在数据结构的`Symbol.iterator`属性，或者说，一个数据结构只要具有`Symbol.iterator`属性，就可以认为是“可遍历的”（iterable）。
* `Symbol.iterator`属性本身是一个函数，就是当前数据结构默认的遍历器生成函数。执行这个函数，就会返回一个遍历器。

```javascript
let arr = [1, 2, 3];
let iter = arr[Symnol.iterator]();
iter.next();
......
```

#### 可以使用 for...of 的数据结构

* 数组

* Set 和 Map

* 类似数组的对象（伪数组）

  == <u>并不是所有的伪数组都有 Iterator 接口，可以使用 Array.from() 方法将带有  length 属性的对象转换为数组，然后再使用 for...of 进行遍历。</u> ==



注意：

1. for...of 循环读取键值，for...in 循环读取键名。
2. 如果想要通过 for...of 读取键名，可以通过数组的 keys 方法来先获取键名数组，然后使用 for...of 遍历。

### node服务器 http-server

* 安装：npm i http-server -g

* 使用： http-server -p 8999 -o

  ​	p 为端口， o 为open



## () 和 {} 的问题

* () 中一般需要传递处理完成的变量或者表达式
* {} 中一般放着语句
* 语句加个 () 可以变成表达式



注意： 只能在对象中才能简写 fn() {} 表示 fn: function() {}

let fn = function () {}  这种的不能简写成上面的形式

自己的属性用 _this 的形式来定义

## 总结一下闭包

* JS 中函数在调用完成之后，会自动释放内存，内部的变量也会被释放内存。下一次调用，会重新分配内存，所有的变量重新初始化

```javascript
　　function f1(){

　　　　var n=999;

　　　　nAdd=function(){n+=1}

　　　　function f2(){
　　　　　　alert(n);
　　　　}

　　　　return f2;

　　}

　　var result=f1();

　　result(); // 999

　　nAdd();

　　result(); // 1000
```

在这段代码中，result实际上就是闭包f2函数。它一共运行了两次，第一次的值是999，第二次的值是1000。这证明了，函数f1中的局部变量n一直保存在内存中，并没有在f1调用后被自动清除。

为什么会这样呢？原因就在于f1是f2的父函数，而f2被赋给了一个全局变量（没有采用 var 的形式），这导致f2始终在内存中，而f2的存在依赖于f1，因此f1也始终在内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。

# 总结一下 JS 事件

* 意思也就是说当我们设置一个延迟函数的时候，当前脚本并不会阻塞，它只是会在浏览器的**事件表**中进行记录，程序会继续向下执行。当延迟的时间结束之后，事件表会将回调函数添加至**事件队列（task queue）**中，事件队列拿到了任务过后便将任务压入**执行栈（stack）**当中，执行栈执行任务，输出 'setTimeout'。
* 事件队列是一个存储着待执行任务的队列，其中的任务严格按照时间先后顺序执行，排在队头的任务将会率先执行，而排在队尾的任务会最后执行。事件队列每次仅执行一个任务，在该任务执行完毕之后，再执行下一个任务。执行栈则是一个类似于函数调用栈的运行容器，当执行栈为空时，JS 引擎便检查事件队列，如果不为空的话，事件队列便将第一个任务压入执行栈中运行。

## 事件循环机制

事件循环的运行机制大致分为以下步骤：

1. 检查事件队列是否为空，如果为空，则继续检查；如不为空，则执行 2；
2. 取出事件队列的首部，压入执行栈；
3. 执行任务；
4. 检查执行栈，如果执行栈为空，则跳回第 1 步；如不为空，则继续检查；

* **Microtask** 通常来说就是需要在当前 task 执行结束后立即执行的任务。例如需要对一系列的任务做出回应，或者是需要异步的执行任务而又不需要分配一个新的 task，这样便可以减小一点性能的开销。microtask 任务队列是一个与 task 任务队列相互独立的队列，microtask 任务将会在每一个 task 任务执行结束之后执行。每一个 task 中产生的 microtask 都将会添加到 microtask 队列中，microtask 中产生的 microtask 将会添加至当前队列的尾部，并且 microtask 会按序的处理完队列中的所有任务。microtask 类型的任务目前包括了 MutationObserver 以及 Promise 的回调函数。

  ```js
   1 console.log('script start');
   2 
   3 setTimeout(function() {
   4   console.log('setTimeout');
   5 }, 0);
   6 
   7 Promise.resolve().then(function() {
   8   console.log('promise1');
   9 }).then(function() {
  10   console.log('promise2');
  11 });
  12 
  13 console.log('script end');
  ```

  'script start'、'script end'、'promise1'、'promise2'、'setTimeout'。

  microtask 执行在当前 task 结束之后，下一个 task 开始之前。

  Promise 任务就是一个 microTask 