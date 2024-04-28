# 1. 为什么选择 TypeScript

1. typescript 增加了代码的可读性和可维护性

   * 类型系统实际上是最好的文档，大部分的函数看看类型的定义就可以知道如何使用了

   * 可以在编译阶段就发现大部分错误，这总比在运行时候出错好

   * 增强了编辑器和 IDE 的功能，包括代码补全、接口提示、跳转到定义、重构等

2. TypeScript 非常包容

   - TypeScript 是 JavaScript 的超集，`.js` 文件可以直接重命名为 `.ts` 即可
   - 即使不显式的定义类型，也能够自动做出[类型推论](https://ts.xcatliu.com/basics/type-inference.html)
   - 可以定义从简单到复杂的几乎一切类型
   - 即使 TypeScript 编译报错，也可以生成 JavaScript 文件
   - 兼容第三方库，即使第三方库不是用 TypeScript 写的，也可以编写单独的类型文件供 TypeScript 读取

...

## 1.3 静态和动态类型语言

* 静态类型语言：在编译阶段确定所有变量的类型
* 动态类型语言：在执行阶段确定所有变量的类型

![动态和静态](./picture/tsPic/动态和静态.jpg)

# 2. 开始使用

全局安装 ts 

```shell
npm i -g typescript
```

编译一个ts文件

```shell
tsc hello.ts
```

> 约定使用 typescript 编写的文件使用 .ts 为后缀，使用 ts 编写 react 的时候以 `.tsx` 为后缀

* 为什么用 `tsc`来编译

  tsc 代表 typescript compiler 

1. TypeScript 中，使用 `:` 指定变量的类型，`:` 的前后有没有空格都可以。

* vscode 实现  .ts 文件的自动编译

  执行   tsc   --init， 生成  tsconfig.json 文件，修改代码输出目录  outDir，

  ```js
  "outDir": "./js", 
  ```

  vscode 中  终端 -> 运行任务 -> 监视 tsc 即可。

**TypeScript 只会进行静态检查，如果发现有错误，编译的时候就会报错。**

**TypeScript 编译的时候即使报错了，还是会生成编译结果**，我们仍然可以使用这个编译之后的文件。

## tsconfig.json 文件

如果一个目录下存在一个`tsconfig.json`文件，那么它意味着这个目录是TypeScript项目的==根目录==。`tsconfig.json`文件中指定了用来编译这个项目的根文件和编译选项。 一个项目可以通过以下方式之一来编译：

### 使用tsconfig.json

- ==不带任何输入文件的情况下调用`tsc`，编译器会从当前目录开始去查找`tsconfig.json`文件，逐级向上搜索父目录。==
- 不带任何输入文件的情况下调用`tsc`，且使用命令行参数`--project`（或`-p`）指定一个包含`tsconfig.json`文件的目录。

当命令行上指定了输入文件时，`tsconfig.json`文件会被忽略。（直接使用 tsc index.ts 的形式）

* 如果要在报错的时候终止 js 文件的生成，可以在 `tsconfig.json` 中配置 `noEmitOnError` 即可。

## webpack中配置 ts

* `npm i webpack webpack-cli webpack-dev-server -D`

* 在 rules 中配置
* npm i ts-loader -D

```js
module: {
  rules: [
    {
      test: /\.tsx?$/i,
      use: [{
        loader: 'ts-loader'
      }],
      exclude: /node_modules/
    }
  ]
}
```

### 测试环境配置

```js
module.exports = {
    devtool: 'cheap-module-eval-source-map'
}
```

* 开启 source map ，cheap 代表忽略文件的类信息，在调试的时候类信息是没有用的；module 代表定位 ts源码，不是 ts-loader 解析之后的js代码；eval-source-map 把 source map以dataurl 的方式把文件中，重编译速度非常快，不用担心性能问题

### 生产环境配置

```js
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
module.exports = {
    plugins: [
        new CleanWebpackPlugin()
    ]
}
```

* 安装 clean-webpack-plugin，每次成功构建之后帮助我们清楚 dist 目录，清空缓存，清除无用的文件。因为在每次构建的时候文件名都是 hash生成的，多次构建之后就会产生无用的文件。

### 使用 webpack-merge 将两个文件合并

```js
const merge = require('webpack-merge')
const baseConfig = require('./webpack.base.config')
const devConfig = require('./webpack.dev.config')
const proConfig = require('./webpack.pro.config')

let config = process.env.NODE_ENV === 'development' ? devConfig : proConfig

module.exports = merge(baseConfig, config)
```

### 启动命令配置

```js
"start": "cross-env NODE_ENV=development webpack-dev-server --hot --open --config ./build/webpack.config.js"
```

* 需要安装：

  ```shell
  npm i cross-env
  ```

  

## 初始化一个 ts 项目

```shell
tsc --init
```

* 会生成一个 tsconfig 文件，里面就是 ts 的配置

# 3. 基础

## 0. JavaScript中的数据类型

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Data_structures

## 1. 原始数据类型

* 所有的原始数据类型使用 小写 字母表示



### 布尔值

在 ts 中，使用 Boolean 定义布尔值类型。

```js
let isTrue: boolean = false
```

注意，使用构造函数的形式创建的对象不是布尔值：

```
let createdByNewBoolean: boolean = new Boolean(1);
```

==特别注意==

使用 new Boolean 或者  new String的方式，new 出来的变量是引用类型的，typeof 变量返回的是 object。new Boolean 的实际类型为 Boolean 类型，new String的类型为 String 类型。

事实上 `new Boolean()` 返回的是一个 `Boolean` 对象：

```js
let createdByNewBoolean: Boolean = new Boolean(1);
```

直接调用 `Boolean` 也可以返回一个 `boolean` 类型：

```js
let createdByBoolean: boolean = Boolean(1);
```

直接使用 Boolean 调用的方式，也能够返回一个 boolean 类型。

在 TypeScript 中，`boolean` 是 JavaScript 中的基本类型，而 `Boolean` 是 JavaScript 中的构造函数。其他基本类型（除了 `null`和 `undefined`）一样，不再赘述。

### 数值

使用 number 定义数值类型：

```js
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
// ES6 中的二进制表示法
let binaryLiteral: number = 0b1010;
// ES6 中的八进制表示法
let octalLiteral: number = 0o744;
let notANumber: number = NaN;
let infinityNumber: number = Infinity;
```

### 字符串

使用 string 定义字符串类型：

```js
let myName: string = "tom"
let myAge: number = 12
// 模板字符串
let sentence: string = `Hello, my name is ${myName}.
I'll be ${myAge + 1} years old next month.`;
```

### 空值

JavaScript 没有空值（Void）的概念，在 TypeScript 中，可以用 `void` 表示没有任何返回值的函数：（写法都是在）

```js
function fn(): void {
    
}
```

声明一个`void`类型的变量没有什么用，只能将它赋值为 undefined 和 null。

```js
let unusable: void = undefined
```

* 在js 中使用 void 跟一个变量来定义 undefined ，因为 js 中没有 undefined 这个保留字，能够自定义一个 undefined 来覆盖全局的 undefined，因此用 void 0 这种方式来产生真正的 undefined。

### null 和 undefined

在 TypeScript 中，可以使用 `null` 和 `undefined` 来定义这两个原始数据类型：

```js
let u: undefined = undefined;
let n: null = null;
```

`undefined` 类型的变量只能被赋值为 `undefined`，`null` 类型的变量只能被赋值为 `null`。

与 `void` 的区别是，==`undefined` 和 `null` 是所有类型的子类型==。也就是说 `undefined` 类型的变量，可以赋值给 `number` 类型的变量：

> 主要是判断后面的类型是否在前面的类型里面包括

```js
// 这样不会报错
let num: number = undefined;
// 这样也不会报错
let u: undefined;
let num: number = u;
```

而 `void` 类型的变量不能赋值给 `number` 类型的变量：

```js
let u: void;
let num: number = u;

// index.ts(2,5): error TS2322: Type 'void' is not assignable to type 'number'.
```

### unknown

* unknown 类型只能被赋值给 any 类型和 unknown 类型本身。



### Object

* `object`表示非原始类型，也就是除`number`，`string`，`boolean`，`symbol`，`null`或`undefined`之外的类型。



### 任意值

任意值（Any）用来表示允许赋值为任意类型。

如果是一个普通类型，在赋值过程中改变类型是不被允许的：

```js
let myFavoriteNumber: string = 'seven';
myFavoriteNumber = 7;

// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

但如果是 `any` 类型，则允许被赋值为任意类型。

```js
let myFavoriteNumber: any = 'seven';
myFavoriteNumber = 7;
```

==任意值在赋值的过程中能够改变该变量的类型。==

#### 1. 任意值的属性和方法

在任意值上访问任何的属性和方法都是允许的：

就是说在任意值的后面加调用任何的属性和方法都不会报错，但是具体的执行还是看该变量有没有这个属性或方法。

可以认为，**声明一个变量为任意值之后，对它的任何操作，返回的内容的类型都是任意值**。返回的

#### 2. 未声明类型的变量

变量如果在声明的时候，未指定其类型，那么它会被识别成为任意值类型：

```js
let something;
something = 'seven';
something = 7;

something.setName('Tom');
```

等价于

```js
let something: any;
something = 'seven';
something = 7;

something.setName('Tom');
```

#### never 类型

* never 类型代表无返回，联 undefined 也不会返回，永远不会返回

  ```js
  // never
  let a: () => never = () => {
      throw new Error('111')
  }
  
  let b = () => {
    white (true) {
      
    }
  }
  ```

  

### ts 类型推论

在定义的时候如果没有赋值，则 ts 会把该变量的类型推断成 any 类型；如果在定义的时候已经赋值了，则会把变量的类型推断为等号后面的类型，在之后的赋值中不能再改变该变量的类型。

```js
let str
str = "string"
str = 1
// 正确
```

```js
let str = "string"
str = 1
// 报错
```

上面的代码，默认第一行  `let str: string = "string"`

### 联合类型

联合类型（union types） 表示取值可以为多种类型中的一种。

就是在定义的时候给予多个类型，使用 | 来分割不同的类型。

```js
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
// 正确

let myFavoriteNumber: string | number;
myFavoriteNumber = true;

// index.ts(2,1): error TS2322: Type 'boolean' is not assignable to type 'string | number'.
//   Type 'boolean' is not assignable to type 'number'.
```

#### 访问联合类型的属性和方法

> 当typescript 不确定一个联合类型的变量到底是哪个类型的时候（该类型还未被赋值具体的值），我们只能访问次联合类型的所有类型里共有的属性或方法。

联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型：（这个推出的类型是暂时的，在下一个赋值之前都可以用这个类型的属性，一旦有下一个赋值，就只能调用下一个赋值的属性）

 ## 对象的类型——接口

在 typescript 中我们使用接口（interfaces）来定义对象的类型。

### 接口（interfaces）

在面向对象的语言中，接口（interfaces）是一个很重要的概念。它是对行为的抽象，而具体如何行动需要由类（classes）去实现（implements）。

接口写法是一个对象，

* 对对象的 shape 进行描述

* 对类（class）进行抽象 

* duck typing （鸭子类型 ），有一些共同的特点，那他就是那个东西（鸭子）

  在动态语言中，更关注对象如何 被使用，而不是对象本身。



#### 例子

```ts
interface Person {
    name: string;
    age: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};
```

上面的例子中，我们定义了一个接口 `Person`，接着定义了一个变量 `tom`，它的类型是 `Person`。这样，我们就约束了 `tom` 的形状必须和接口 `Person` 一致。

定义的变量采用了接口的类型，不能比接口少一些属性，也不能多出一些属性。因此，赋值额时候变量的形状必须和接口的形状保持一致。

接口的类型也可以定义为 any 类型，在赋值的时候能够赋值为任意的类型。

#### 鸭子类型

* 在前后端定义的接口中，获取到后端的字段是定义的接口的必要条件

```typescript
// interface
interface Iprops {
    name: string;
    age: number;
}
const fn = (arg: Iprops) => {
    console.log(arg)
}
const result = {
    name: 'zzz',
    age: 11,
    gender: 'female'
}
fn(result)
```

* 多了一个 gender 属性并没有报错

### 可选属性

有时候我们希望不要完全匹配一个形状，那么可以使用的可选属性：

```ts
interface Person {
    name: string;
    age?: number;
}

let tom: Person = {
    name: 'Tom'
};
```

可选属性的含义是该属性可以不存在

这时仍然不许新增一个接口没有定义的属性。

### 任意属性

有时候我们希望一个接口允许有任意的属性。可以使用 `[propName: string]:string `的形式来定义一个可以接收任意符合类型的属性。

第一个 string 的意思是 新增的未知 的属性的类型定义为 string，第二个 string 的意思是强制要求所有的属性的值都必须是 string 类型。（如果与已定义的属性的类型冲突，则会报错）

需要注意的是，**一旦定义了任意属性，那么确定属性和可选属性都必须是它的子属性**：

```typescript
interface Person {
    name: string;
    age?: number;
    [propName: string]: string;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};

// index.ts(3,5): error TS2411: Property 'age' of type 'number' is not assignable to string index type 'string'.
// index.ts(7,5): error TS2322: Type '{ [x: string]: string | number; name: string; age: number; gender: string; }' is not assignable to type 'Person'.
//   Index signatures are incompatible.
//     Type 'string | number' is not assignable to type 'string'.
//       Type 'number' is not assignable to type 'string'.
```

上例中，任意属性的值允许是 `string`，但是可选属性 `age` 的值却是 `number`，`number` 不是 `string` 的子属性，所以报错了。`propName` 只是一个代称，可以使用其他的来代替，使用一个 p 也是可以的。

#### 另一种任意类型

* 在定义 interface 的时候，如果什么都没写，则这个对象的属性可以是任何值

  ```typescript
  interface IType = {}
  
  const types: IType = {
    test1: '111',
    test2: 222
  }
  ```

  

### 只读属性

有时候我们希望一个属性在创建的时候被赋值，其他任何情况不能再改变该值，那么可以使用到`readonly`定义只读属性。类似于`const`.

```ts
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    id: 89757,
    name: 'Tom',
    gender: 'male'
};

tom.id = 9527;

// index.ts(14,5): error TS2540: Cannot assign to 'id' because it is a constant or a read-only property.
```

**注意，只读的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候**：

==只读属性和一般的属性一样是必须在对象赋值的时候赋值，只是不能在之后修改该赋值==

总结：

1. 接口的写法就是对象的写法，后面的值就是他属性的类型。
2. 在接口的书写中，后面可以使用分号来分割语句。
3. 只读属性存在于 对象 数组 元祖

### 在对象中使用定义属性的类型

```ts
const obj = {
  a: '1' as string,
  o: [] as any[],
}
```



## 数组的类型

在 TypeScript 中，数组类型有多种定义方式，比较灵活。（就是任何类型的数据在定义的时候一定要注意规定类型）

### 「类型 + 方括号」表示法

最简单的方法是使用「类型 + 方括号」来表示数组：

let arr: number[] = [1, 1, 1, 2]

==数组的方法在使用的过程中也必须按定义时的类型进行限制==

例子： 

```ts
let fibonacci: number[] = [1, 1, 2, 3, 5];
fibonacci.push('8');

// index.ts(2,16): error TS2345: Argument of type 'string' is not assignable to parameter of type 'number'.
```

上例中，`push` 方法只允许传入 `number` 类型的参数，但是却传了一个 `string` 类型的参数，所以报错了。

#### 数组的联合类型

```js
let arr: (string | number)[] = ['1', '2', '3', 5]
```
#### 数组的 readonly

```typescript
const arr1: readonly number[] = [1, 2];
```



#### 常量数组

[常量数组](## as const 问题)

<a href='## as const 问题'>常量数组</a>


#### 注意

* 数组定义类型之后，数据的方法也会限定类型，比如说数组的push方法

### 数组泛型

能够使用数组泛型(Array Generic) `Array<elemType>`来表示数组：

```js
let arr: Array<number> = [1,2,3,4]
```

### 用接口来表示数组

接口也可以用来描述数组：

```ts
interface NumberArray {
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```

写法就是数组的index用 `[index: number]` 来写。

数组中需要存放任意类型的时候，直接将类型定义为任意类型即可。

```ts
let list: any[] = ['Xcat Liu', 25, { website: 'http://xcatliu.com' }];
```

### 类数组

类数组（Array-like Object）不是数组类型，比如 `arguments`

```ts
function sum() {
    let args: number[] = arguments;
}

// index.ts(2,7): error TS2322: Type 'IArguments' is not assignable to type 'number[]'.
//   Property 'push' is missing in type 'IArguments'.
```

事实上常见的类数组都有自己的接口定义，如 `IArguments`, `NodeList`, `HTMLCollection` 等：

```ts
function sum() {
    let args: IArguments = arguments;
}
```

==伪数组可以使用内置的接口来实现==

不能把伪数组赋值成数组类型

## 函数的类型

函数声明类型：

```typescript
function sum(x: number, y: number): number {
    return x+y
}
```



注意，**输入多余的（或者少于要求的）参数，是不被允许的**：

函数的赋值类型：

注意：函数的表达式需要在赋值的左边来定义输入输出的数据类型，不能够只在右边进行函数输入输出的定义。因为函数本身就是一种类型

```typescript
let sum: (x: number, y: number) => number = function (x: number, y: number): number {
    return x + y
}
```

注意不要混淆了 TypeScript 中的 `=>` 和 ES6 中的 `=>`。

在 TypeScript 的类型定义中，`=>` 用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。

==在ts中写接口的时候，冒号右边的可以想象成实现这个接口的时候右边的值的类型（包括函数的返回值的类型。）==

ts 中冒号的右边是类型和实际的代码逻辑没有什么关系。

* 一个变量定义了函数类型之后，给它赋值的函数就不必要再定义类型了。
* 函数类型的定义使用的是 冒号，函数类型的赋值使用的等号，分清楚

### 函数参数的可选

可选参数的写法与接口的可可选属性的写法是一样的

```ts
function buildName(firstName: string, lastName?: string) {
    if (lastName) {
        return firstName + ' ' + lastName;
    } else {
        return firstName;
    }
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');
```

需要注意的是，==可选参数必须接在必需参数后面==。换句话说，**可选参数后面不允许再出现必须参数了**：

### 参数的默认值

```ts
function buildName(firstName: string = 'Tom', lastName: string) {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let cat = buildName(undefined, 'Cat');
```

默认值的参数不受可选参数后面不能跟必选参数的限制。

默认值的参数就相当于可选参数。

### 函数参数只读

```typescript
const fn = function(a: string, b: readonly number[]) {
    
}
```

### 定义函数的三种方式

1. 直接定义类型

```js
let add: (x: number, y: number) => number
```

2. 接口定义

```typescript
interface Add {
  (x: number, y: number): number
}
// 接口定义函数返回值用 冒号 和其他的都一样
```

3. 类型别名

```typescript
type Add = (x: number, y: number) => number
```

* 注意：上面三种方式的函数参数都不是强制限制的，他们只是定义了函数的形状，但是函数具体使用的时候到底长什么样，还是要具体的函数实现的时候来定义的，类似于下边的 fn4 才会强制限定函数使用规范。
* 说白了函数的上述三种定义只是函数的重载，下面函数真正张什么样，只要包含上边的函数就行了

```typescript
interface IFn {
    (name: string): string;
    // [property: string]: string;
}

const fn1: IFn = () => {
    return ''
}

type IFn1 = (name: string) => string

const fn2: IFn1 = () => {
    return '111'
}

const fn3: (name: string) => string = () => {
    return ''
}

function fn4(name: string): string {
    return '11'
}
fn4('1')
```

4. declare 一个函数声明，这种情况不需要写函数的实现

   ```typescript
   declare function PromiseAll(arg: string): void
   ```

   



### 重载

重载(overloading) 是在一个类里面，方法名字相同，而参数不同。返回类型可以相同也可以不同。

重载允许一个函数接受不同数量或类型的参数时，作出不同的处理。

好处就是不需要为了相似的功能选用不同的函数名称，增强了函数的可读性

就是接受number的参数的时候，return 一个number类型的数据。接受 string的参数，返回string类型的数据。

可以从上到下重复定义函数，函数的调用的时候从上到下匹配类型，从而表述清楚函数的返回类型。

上例中，我们重复定义了多次函数 `reverse`，前几次都是函数定义，最后一次是函数实现。在编辑器的代码提示中，可以正确的看到前两个提示。

注意，TypeScript 会优先从最前面的函数定义开始匹配，所以多个函数定义==如果有包含关系，需要优先把精确的定义写在前面。==也就是函数的主体肯定是类型最多的，也就是上述函数的联合类型。

```typescript
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string | void {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

1. 写几个函数不同参数的定义
2. ==在一个宽泛的函数中写出它的具体实现==（any 参数）



* class 内的重载

  ```typescript
  class Child {
      // private test() { // 不能修改父类的修饰符
      // public test(age: number): number {}
      /**
       * 1. 不能修改父类的修饰符
       * 2. 不能修改输入输出
       */
      test(name: number): number;
      test(name: unknown): unknown {
          return name
      }
  }
  ```

  



### 重写（override）

重写是子类对父类的允许访问的方法的实现过程进行重新编写, 返回值和形参都不能改变。**即外壳不变，核心重写！**

重写的好处在于子类可以根据需要，定义特定于自己的行为。 也就是说子类能够根据需要实现父类的方法。

重写方法不能抛出新的检查异常或者比被重写方法申明更加宽泛的异常



## 类型断言

类型断言（type assertion）可以用来手动指定一个值的类型。就是我们告诉编辑器我们知道这个类型就是指定的类型

#### 语法

```ts
<类型>值
```

或

```ts
值 as 类型
```

在 tsx 语法（React 的 jsx 语法的 ts 版）中必须用后一种。

类型断言就是在==联合类型==的情况下，对其中的一个类型进行if的赛选。

```ts
function getLength(something: string | number): number {
    if ((<string>something).length) {
        return (<string>something).length;
    } else {
        return something.toString().length;
    }
}
```

上面的例子中就不会出现 if 里面的语句报错的情况。

断言只能断言联合类型中的一个类型，也可以使用一种全部包含联合类型的类型，包含一个或多个。

==注意：在使用断言的时候，后续的具体使用都要<类型>值的形式来使用。类型的断言使用的interface所以跟着的类型是大写的==

## 声明文件

当使用第三方库时，我们需要引用它的声明文件，才能获得对应的代码补全、接口提示等功能。

在 ts 中使用第三方库的时候需要使用声明语句来对全局变量进行声明。

```ts
declare var jQuery: (selector: string) => any

jQuery("#foo")
```

这样写只是为了通过 ts 的编译，没有多余的作用。

## 什么是声明文件

通常我们会把声明语句放到一个单独的文件（`jQuery.d.ts`）中，这就是声明文件：

```ts
// src/jQuery.d.ts

declare var jQuery: (selector: string) => any;
```

声明文件必需以 `.d.ts` 为后缀。

一般来说，ts 会解析项目中所有的 `*.ts` 文件，当然也包含以 `.d.ts` 结尾的文件。所以当我们将 `jQuery.d.ts` 放到项目中时，其他所有 `*.ts` 文件就都可以获得 `jQuery` 的类型定义了。

### 第三方声明文件

当然，jQuery 的声明文件不需要我们定义了，社区已经帮我们定义好了：[jQuery in DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/jquery/index.d.ts)。

我们可以直接下载下来使用，但是更推荐的是使用 `@types` 统一管理第三方库的声明文件。

`@types` 的使用方式很简单，直接用 npm 安装对应的声明模块即可，以 jQuery 举例：

```bash
npm install @types/jquery --save-dev
```

可以在[这个页面](http://microsoft.github.io/TypeSearch/)搜索你需要的声明文件。  	

* 在使用第三方声明文件的时候，可以直接使用上面的方式进行安装即可，不需要自己再去定义了。

### 书写声明文件

具体的书写声明文件需要查看文档来实现。

https://ts.xcatliu.com/basics/declaration-files.html

全局变量的声明文件主要有以下几种语法：

- `declare var` 声明全局变量
- `declare function` 声明全局方法
- `declare class` 声明全局类
- `declare enum` 声明全局枚举类型
- `declare namespace` 声明全局对象（含有子属性）
- `interface` 和 `type` 声明全局类型

***缺失***

## 内置对象

内置对象是指根据标准在全局作用域（Global）上存在的对象。这里的标准是指 ECMAScript 和其他环境（比如 DOM）的标准。

ECMAScript 标准提供的内置对象有：

`Boolean`、`Error`、`Date`、`RegExp` 等。

我们可以在 TypeScript 中将变量定义为这些类型：

```ts
let b: Boolean = new Boolean(1);
let e: Error = new Error('Error occurred');
let d: Date = new Date();
let r: RegExp = /[a-z]/;
```

DOM 和 BOM 提供的内置对象有：

`Document`、`HTMLElement`、`Event`、`NodeList` 等。

TypeScript 中会经常用到这些类型：

```ts
let body: HTMLElement = document.body;
let allDiv: NodeList = document.querySelectorAll('div');
document.addEventListener('click', function(e: MouseEvent) {
  // Do something
});
```

它们的定义文件同样在 [TypeScript 核心库的定义文件](https://github.com/Microsoft/TypeScript/tree/master/src/lib)中。

### TypeScript 核心库的定义文件

typescript 定义了常用的对象和方法的shape，（采用接口的形式）

### typescript 写 node.js

Node.js 不是内置对象的一部分，如果想用 TypeScript 写 Node.js，则需要引入第三方声明文件：

```bash
npm install @types/node --save-dev
```

# 4. 进阶

## 类型别名

就是给类型起一个自定义的名字

常用作给联合类型起一个自定义的名字。

使用关键字 type 来实现，

```ts
type Name = string
```

应用：

```typescript
type PlusType = (a: number, b: number) => number;
const plues: PlusType = (a: number, b: number): number => {
    return a + b
}
```



## 字符串字面量类型

字符串字面量类型用来约束取值只能是某几个字符串中的一个，就是相当于自定义了几个字符串类型的类型。只能取自定义的字符串中的一个，也使用 type 关键字来实现。

## 元祖

数组合并了相同类型的对象，而元祖（tuple）合并了不同类型的对象.

### 举例

定义一对值分别为 `string` 和 `number` 的元组：

```ts
let xcatliu: [string, number] = ['Xcat Liu', 25];
```

就是在数组的定义过程中，为其中的每个元素规定类型。

### 越界的元素

当添加越界的元素时，它的类型会被限制为元组中每个类型的联合类型：

当超出规定类型的元素时，这个元素会被定义成之前数组定义过的类型的联合类型。

### 注意

* 元祖是特殊的数组，在定义的时候长度不能变，要和左侧的类型对齐。
* 元祖可以使用数组的方法来添加元素，比如说使用 push 方法。但是不能进行越界访问，实际开发的过程中不建议这样使用

## 枚举

枚举（enum）类型用于取值被限定在一定范围内的场景，比如一周只能有七天，颜色限定为红绿蓝等。

### 例子

枚举成员会被赋值为从 `0` 开始递增的数字，同时也会对枚举值到枚举名进行反向映射：

```ts
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};

console.log(Days["Sun"] === 0); // true
console.log(Days["Mon"] === 1); // true
console.log(Days["Tue"] === 2); // true
console.log(Days["Sat"] === 6); // true

console.log(Days[0] === "Sun"); // true
console.log(Days[1] === "Mon"); // true
console.log(Days[2] === "Tue"); // true
console.log(Days[6] === "Sat"); // true
```

### 手动赋值

* 我们也可以给枚举项手动赋值

```ts
enum Days {sun = 7, mon =1, tue, wed, thu}
```

枚举手动赋值的元素会赋值为赋值的元素，没有赋值的元素还是会默认的赋值为当前所在位置的值（0,1,2,3）

注意：手动赋值的值最好不要和后面的值重复，如果重复的话，后面的值会覆盖掉前面的值。

* 枚举的手动赋值也可以不为数字，需要使用断言来避免ts检测出错，ts枚举的实质就是后面的值等于前面的值＋1，当前面一个元素手动赋值成字符串类型的话，后面也需要手动赋值成为字符串类型的值。

### 计算值

* 枚举中的计算值会被保留在执行阶段才会执行，就是不会立即执行

### 常亮枚举

* 不能存在计算值，比如 `arr.length`
* 当我们不需要一个对象，只需要一个对象的值的话就会使用到常量枚举

```typescript
const enum Month {
    Jan = 1,
    Feb,
    Jun
}
let test2 = [Month.Feb, Month.Jun]
console.log(test2)
```



### 字符串枚举

```typescript
enum Message {
  Success: '成功',
  Fail: '失败'
}
```

* 注意字符串枚举不能够反向映射

### 异构枚举

* 就是数字 + 字符串枚举

```typescript
enum Answer {
  N,
  Y = 'yes'
}
```

### 枚举类型

* 枚举也可以作为一种类型来使用

```typescript
// 枚举类型
enum E {
    a,
    b
}
enum F {
    a = 1,
    b = 2
}
enum G {
    a = 'test1',
    b = 'test2'
}

let e1: E = 5
let e2: F = 5
let e3: G = G.a
let e4: G.a = G.a
```

* 数字类型只要是数字就可以，字符串枚举类型只能取固定的值

### 枚举思考

* 在 js 中能够使用如下的方式去赋值

```js
var obj = {}
var a
obj[a = 1] = 'test'
// obj[1] === 'test'
```



## 类

TypeScript 除了实现了所有 ES6 中的类的功能以外，还添加了一些新的用法。

typescript 类的使用规则：https://ts.xcatliu.com/advanced/class.html

### 修饰符

* 类上的方法和属性提供修饰符

* publicre

* private 子类也不能访问，甚至连实例对象也不能直接访问，只能在 class 构造类中内部使用

  如果 private 作用在 constructor 上，则这个类既不能被实例，也不能被继承

* Protected 外界不能访问，子类可以访问。不能在实例中访问。

  如果 constructor 定义为 protected ，则这么类能被继承但是不能被实例，能作为一个基类。

* readonly 只读的，不能修改

* 静态的 

  static + 上面的修饰符
  
  静态的属性可以被继承，不需要写多余的继承代码

### 面向对象

封装、继承、多态

### ==重要==

1. es6 和 typescript 中的非函数类型的属性都绑定在实例对象上边，不是绑定在实例对象的原型上面。

2. 于 es 不同的是属性需要有初始值或者在构造函数中被初始化。（es 中直接在class中写一个name属性，不需要被赋值默认值或者constructor中被赋值）

3. Es 中的属性不需要在外边定义，直接在 constructor 中定义也是可以的，但是ts 中的属性必须要在class 中定义。

4. constructor 的参数也可以使用修饰符，使用了修饰符就不用再在 class 中定义了，会自动定义为实例属性

5. 类的继承除了new 的时候初始化的实例属性需要写额外的代码（super()），其他的都能直接使用。

6. ts 中方法的定义必须使用省略的书写，因为适应变量定义方法哪种会识别成变量，一个变量就必须要赋值

   ```typescript
   abstract eat(food: string): void
   // 正确
   public test: () => void
   // 错误，提示 test 没有赋值
   ```

7. 所有方法都应该写到 constructor 后面，不然就识别不了

### 类使用 interface

* 两个抽象的类，有共同的方法（特性），但是没法抽象出一个共同的父类，可以抽象出一个interface

```typescript
interface Radio {
  switchRadio(): void;
}
interface Battery {
  checkBatteryStatus();
}
class car implements Radio {
  switchRadio() {
    
  }
}
class phone implements Radio, Battery {
  switchRadio() {
    
  }
  checkBatteryStatus() {
    
  }
}
```

* 可以实现两个接口，一个公共的，一个特有的

甚至接口也可以继承接口，实现组合，继承一个接口即可。

```typescript
interface RadioWithBattery extends Battery {
  
}
```

1. 接口只能约束类的公有成员，不能约束 构造函数

2. 接口也可以继承类，这个类中的成员就会添加到这个约束中

   ```typescript
   class A {
     name = 1
   }
   interface Ia extends A {
   	
   }
   class B implements Ia {
     name = 2
   }
   
   ```

3. 接口能够抽离出类的公有、私有、保护的成员，接口继承类的私有成员但是不能被实现，实现会提示私有成员保护。同理保护的成员也一样。

   ![class & interface](./picture/tsPic/class & interface.jpg)

   因此，接口在继承class时要考虑是否继承类的私有成员和保护成员。

### 抽象类

1. 抽象类 不能被实例，只能被继承
2. 抽象类中的抽象方法相当于指定了一个方法名，知道子类中有不同的实践，所有抽象类中的抽象方法不需要具体的方法实现，只有一个方法名

```typescript
abstract class Animal {
    constructor (name: string) {
        this.name = name
    }
    public name: string
    abstract eat(food: string): void
    // public test: () => void
}
console.log(Animal)
class Cat extends Animal {
    constructor (name: string) {
        super(name)
    }
    public eat(food: string) {

    }
}
```

### 小技巧（ts 的特殊类型 this）

1. 可以使用方法返回 this ，实现链式调用

### 接口表示类

```tsx
interface IConstruc {
    new(...args: any[]): {};
}
```



### constructor

* 如果有返回且是一个引用类型，则采用该类型作为实例

* 如果用 protected 修饰能作为基类

  ```typescript
  // 1. protected constructor作为基类
  class BasicClass {
      // public test: string;
  
      protected constructor(
          protected test: string, // 这样写直接能作为一个内部的参数，不需要显式定义了
      ) {
          this.test = test
      }
  }
  ```

* constructor 中的参数只要加了修饰符就可以作为一个已经被定义过的参数了

  换成 public、private、protected、readonly都是可以的

```typescript
class BasicClass {
    // public test: string;

    protected constructor(
        protected test: string, // 这样写直接能作为一个内部的参数，不需要显式定义了
    ) {
        this.test = test
    }
}
// 换成 public、private、protected、readonly都是可以的
```

如果继续在外层定义该属性，则会报错！！！报错信息：重复命名



### 类和接口的区别

* 类是数据和逻辑的集合，接口是类型的集合

### 基类和接口的区别

* 基类提供一些公共方法或数据的集合（等于类型约束+逻辑实现）；接口一般是类型的约束。



## 泛型（牛的）（generics）——类型的参数

泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

==可以把泛型想象成一个函数的参数，形参并不需要具体的值。泛型也可以叫做类型参数==

```js
function show<T>(arg: T): T {
  return arg
}
const result = show('111')
// 会做类型推断，推断出result的类型为 string
const result1 = show(true)
// 会报错

```

多个泛型

```js
function swap<T, U>(tuple: [T, U]): [U, T] {
  return [tuple[1], tuple[0]]
}
// 上边类型是元祖，不是数组
```

数组使用泛型

```js
function echoWithArr<T>(arr: T): T {
  return arr.length;
}
//报错，因为类型不确定，不一定有 length 属性
function echoWithArr<T>(arr: T[]): T[] {
  console.log(T.length)
  return arr
}
```

* 注意
  1. 函数的泛型只能在用函数定义的时候定义，不能在定义函数的引用的时候使用

### 泛型的继承

* 如果一个泛型有一些公共的属性，可以抽象成一个 interface

```typescript
interface Types {
    length: number;
}
function echoWithArr<T extends Types>(tuple: T): T {
    console.log(tuple.length);
    return tuple
}
```

### 类中使用泛型

```js
class Queue<T> {
  private data = []
  push(num: T) {
    this.data.push(num)
  }
	pop(): T {
    return this.data.shift()
  }
}

const queue = new Queue<number>()
```

==注意==

1. 泛型不能作用于类的静态成员
2. 类中的泛型如果没有指定类型，会当成一个任意值来执行（多个任意值？函数能不能是任意类型）

### interface 中使用泛型

```typescript
interface KeyPair<T, U> {
  k: T;
  v: U;
}
let key1: KeyPair<string, number> = {
  k: '111',
  v: 111
}
```

### 数组泛型

```typescript
const arr: Array<number> = [1, 2]
```

### 函数使用 interface 描述

```typescript
interface Iplus {
  (a: number, b:number): number
}
function plus(a: number, b: number): number {
  return a + b
}
const a: Iplus = plus
// 函数描述中使用泛型
interface Log<T> {
  (value: T): T
}
```

### 泛型中默认类型

* 默认类型用等号来表示

```typescript
class A<T = {}> {
    
}
interface Log<T = string> {
  (value: T): T
}

```
### 泛型的约束

* interface 也可以对泛型进行约束，因为泛型是一种类型，所以该约束是采用继承的方式

  ```typescript
  interface ILength {
      length: number;
  }
  class Log<T extends ILength> {
      
  }
  function fn<T extends ILength> (value: T) {
      console.log(value.length)
  }
  // 这样使用 T 就有了length 属性
  ```

### 泛型的好处

1. 函数和类可以轻松的支持多种类型，增强程序的可扩展性
2. 不必写多条函数重载，冗长的联合类型生命，增强代码可读性
3. 灵活控制类型之间的约束

### 匿名函数使用泛型

泛型的使用都在函数的后面跟着，匿名函数没有函数名称，因此泛型直接写到函数前边就可以

```typescript
let two2 : <T>(a : T[]) => T = function (a) {
    return a[0];
}
```





## 装饰器

若要启用实验性的装饰器特性，你必须在命令行或`tsconfig.json`里启用`experimentalDecorators`编译器选项：

### 简介

*装饰器*是一种特殊类型的声明，它能够被附加到[类声明](https://www.tslang.cn/docs/handbook/decorators.html#class-decorators)，[方法](https://www.tslang.cn/docs/handbook/decorators.html#method-decorators)， [访问符](https://www.tslang.cn/docs/handbook/decorators.html#accessor-decorators)，[属性](https://www.tslang.cn/docs/handbook/decorators.html#property-decorators)或[参数](https://www.tslang.cn/docs/handbook/decorators.html#parameter-decorators)上。 装饰器使用 `@expression`这种形式，`expression`求值后必须为一个函数，它会==在运行时被调用==，被装饰的声明信息做为参数传入。

### 装饰器工厂函数

* 也就是返回一个装饰器函数的函数，能够进行一些定制（比如说传递一些值）

### 装饰器组合

也就是多个装饰器使用。由下向上，像函数组合一样传递返回的函数

同样的，在TypeScript里，当多个装饰器应用在一个声明上时会进行如下步骤的操作：

1. 由上至下依次对装饰器表达式求值。
2. 求值的结果会被当作函数，由下至上依次调用。

### 类装饰器求值

类装饰器按一下规定的顺序应用：实例、静态成员、构造函数、类

类中不同声明上的装饰器将按以下规定的顺序应用：

1. *参数装饰器*，然后依次是*方法装饰器*，*访问符装饰器*，或*属性装饰器*应用到每个实例成员。
2. *参数装饰器*，然后依次是*方法装饰器*，*访问符装饰器*，或*属性装饰器*应用到每个静态成员。
3. *参数装饰器*应用到构造函数。
4. *类装饰器*应用到类。

### 执行顺序

* 会在类的模块执行时执行，也就是实例化之前执行

### 类装饰器

*类装饰器*在类声明之前被声明（紧靠着类声明）。 类装饰器应用于类构造函数，可以用来==监视，修改或替换类定义==。 类装饰器不能用在声明文件中( `.d.ts`)，也不能用在任何外部上下文中（比如`declare`的类）。

类装饰器表达式会在运行时当作函数被调用，类的构造函数作为其唯一的参数。

### 方法装饰器

 它会被应用到方法的 *属性描述符*上，可以用来监视，修改或者替换方法定义。

方法装饰器表达式会在运行时当作函数被调用，传入下列3个参数：

1. 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
2. 成员的名字。
3. 成员的*属性描述符*。

如下实现了两个方法装饰器，一个修改了定义第二个监视了方法的调用。

```typescript
// 方法装饰器是有副作用的，因为他返回的是属性描述符，所以需要修改它的引用
function fnBind<T extends Function>(
    target: any, // 目标函数挂载的对象。实例成员是原型对象，静态成员是构造函数
    propertyKey: string | symbol, // 成员名字
    descriptor: TypedPropertyDescriptor<T>,
): TypedPropertyDescriptor<T> {
    // console.log(descriptor);
    if (descriptor == null || (typeof descriptor.value !== 'function')) {
        throw new TypeError('only used by function');
    }
    // 返回一个属性描述符
    return {
        configurable: true,
        enumerable: true,
        // 实现bind
        get(...args) {
            // console.log(this, '----我是this', args);
            // 谁取值，那就绑定谁！这个是重点！
            // console.log(this, '---this222');
            const bound: T = descriptor.value!.bind(this)
            // Object.defineProperty(this, propertyKey, {
            //     value: bound,
            //     configurable: true,
            //     writable: true,
            // })
            return bound
        }

        // 实现代理
        // get() {
        //     return new Proxy(descriptor.value!, {
        //         apply(target, thisArg, argArray) {
        //             console.log('函数被调用了，做出处理！');
        //             return target.apply(thisArg, argArray)
        //         }
        //     })
        // }
    };

    // if (typeof propertyKey === 'string' && target[propertyKey] instanceof Function) {
    //     const origin = target[propertyKey]
    //     target[propertyKey] = origin.bind(target)
    //     console.log(target[propertyKey], '---', propertyKey, origin.bind(target), target);
    // }
}
// 一个proxy decorator。handle 由使用者传入
function proxyDecorator(handle: Function): MethodDecorator {
    return (
        target: Object,
        propertyKey: string
         | symbol,
        descriptor: PropertyDescriptor,
    ): TypedPropertyDescriptor<any> => {
        const value = (target as any)[propertyKey]
        if (descriptor == null || (typeof value !== 'function')) {
            throw new TypeError('only used by function');
        }
        // console.log(target, '----target---', value);
        return {
            configurable: true,
            enumerable: true,
            get() {
                return new Proxy(value, {
                    apply(target, thisArg, argArray) {
                        // console.log('函数被调用了，做出处理！');
                        handle(thisArg, argArray);
                        return target.apply(thisArg, argArray)
                    }
                })
            }
        }
    }
}
```

### 访问装饰器

* 和方法装饰器差不多，主要应用于 get 方法！

### 参数装饰器

参数装饰器表达式会在运行时当作函数被调用，传入下列3个参数：

1. 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
2. 成员的名字。
3. 参数在函数参数列表中的索引。

>  注意  参数装饰器只能用来监视一个方法的参数是否被传入。

## 元信息

https://www.ruanyifeng.com/blog/2007/03/metadata.html

> 元信息是关于信息的信息，元信息允许服务器提供所发送数据的信息，如**[HTTP](https://baike.baidu.com/item/HTTP?fromModule=lemma_inlink)**可以提高所发的对象语言和对象，也可以用元信息来实现有条件请求以及报告事务完成。收到数据的浏览器可以根据元信息确定服务器发来的是什么内容，预料有什么数据，确知是否接收完整的数据，以及过程中是否出错，这样客户就可以知道传输对象的类型
>
> * 比如说http的元信息，能够用来做判断。
> * 运行时

> 元信息是关于信息的信息，用于描述[信息](https://baike.baidu.com/item/信息?fromModule=lemma_inlink)的结构、语义、用途和用法等。在计算机科学中，**反射**是指[计算机程序](https://baike.baidu.com/item/计算机程序?fromModule=lemma_inlink)在[运行时](https://baike.baidu.com/item/运行时?fromModule=lemma_inlink)（Run time）可以访问、检测和修改它本身状态或行为的一种能力。**反射**利用元信息为管理、控制和使用复杂信息提供了一种高效的途径。 通过从元信息到信息的反射机制，用户可以以更简单、更灵活、更自动化的方式使用信息本身。 元信息和元信息建模可以用于软件体系结构的描述和使用过程中。
>
> 百度百科

* 信息的信息
* 反射——可以访问、检测和修改它本身状态或行为的一种能力。**反射**利用元信息为管理、控制和使用复杂信息提供了一种高效的途径
* 元数据最大的好处是，它使信息的描述和分类可以实现格式化，从而为机器处理创造了可能。

元信息伴随着反射的使用

### 元信息实现原理

https://juejin.cn/post/6876776311696654344#heading-2

* 就是一个全局的 weakMap，采用 iife 函数注入全局

> 本质是一个可以单独运作的库，通过IIFE（立即调用函数表达式）形式，往全局的`Reflect`对象**增加**了一些方法。并且与typescript有特别的配合，当启用typescript `emitDecoratorMetadata` 选项时，typescript会在生成的装饰器函数调用中额外添加一些代码(调用 `reflect-metadata` 相关api)
>
> 
>
> 作者：teriri
> 链接：https://juejin.cn/post/6876776311696654344
> 来源：稀土掘金
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

可以通过查看 Reflect.js 文件查看 metadata 实现源码，就是一个闭包中的一个 weakmap 存储的元数据，而不是保存到 target 上的。

## 应用——控制反转（IOC）和 依赖注入（DI）

阅读：https://zhuanlan.zhihu.com/p/33492169

https://segmentfault.com/a/1190000008626680

* 如果类 A 依赖 B，需要进行依赖解耦，交出控制权。——空

### 控制反转

Ioc 不是一种技术，只是一种思想，一个重要的面向对象编程法则，它能指导我们如何设计松耦合、更优良的系统。传统应用程序都是由我们在类内部主动创建依赖对象，从而导致类与类之间高耦合，难于测试；有了 IoC 容器后，把创建和查找依赖对象的控制权交给了容器，由容器注入组合对象，所以对象之间是松散耦合，这样也便于测试，利于功能复用，更重要的是使得程序的整个体系结构变得非常灵活。　　

其实 IoC 对编程带来的最大改变不是从代码上，而是思想上，发生了"主从换位"的变化。应用程序本来是老大，要获取什么资源都是主动出击，但在 IoC思想中，应用程序就变成被动了，被动的等待 IoC 容器来创建并注入它所需的资源了。　

### 依赖注入

DI - Dependency Injection，即"依赖注入"：组件之间的依赖关系由容器在运行期决定，形象的说，即由容器动态的将某个依赖关系注入到组件之中。

## Reject-metaData 使用

和元信息、反射有相关性。

因为添加的元信息既能是字符串 也能是实例或者方法。因此也可以作为获取实例或者方法函数的一种途径

```typescript
// 需要开启 emitDecoratorMetadata 配置
import "reflect-metadata";

function setFn(
    target: Object,
    propertyKey: string | symbol,
    descriptor: TypedPropertyDescriptor<any>,
) {
    console.log(Reflect.getMetadata('design:type', target, propertyKey), '---setFn');
    console.log(Reflect.getMetadata('design:paramtypes', target, propertyKey), '---setFn1');
    console.log(Reflect.getMetadata('design:returntype', target, propertyKey), '---setFn2');
}

@Reflect.metadata("meta:class", "metaData")
class MetaData {
  @Reflect.metadata("meta:type", "测试测试")
  public initStr: string = "";
  @setFn
  fn(arg: string[]): string {
    return ''
  }
}

let test = new MetaData();
// 属性元数据三个参数：metaKey, target, propertyKey
// Reflect.getMetadata('design:type', target, key) 获取属性类型
// 除能获取属性类型外，通过 Reflect.getMetadata("design:paramtypes", target, key) 和 Reflect.getMetadata("design:returntype", target, key) 可以分别获取函数参数类型和返回值类型。
console.log(Reflect.getMetadata("meta:class", MetaData));
```

* 官方定义了三种获取类型的方式：主要是函数类型、函数参数、return类型三种

* 也支持在装饰器中自己设置 Reflect.defineMetadata(metadataKey: any, **metadataValue: any**, target: Object) 方法

  或者直接使用 @Reflect.metadata("meta:class", "metaData") 的方式直接添加到对象上（方法或者对象）

### typescript 注入元数据

- `design:type`：成员类型
- `design:paramtypes`：成员所有参数类型
- `design:returntype`：成员返回类型

TypeScript 结合自身语言的特点，为使用了装饰器的代码声明注入了 3 组元数据：

### 使用场景

* todo 控制反转（ioc）和依赖注入的实现

控制反转实现，把控制权交给外界

```typescript

type Constructor<T> = new (...args: any[]) => T
function classDecorator<T>(
    target: Constructor<T>
) {
    // 获取的是constructor 的参数
    const providers = Reflect.getMetadata('design:paramtypes', target)
    // 控制权在这里！
    const args = providers.map((provider: any) => new provider());
    console.log(providers, '---providers---', ...args);
}

class Temp {}

// @Reflect.metadata("meta:class", "metaData")
@classDecorator
class MetaData {
  protected name: string;

  constructor(name: string, readonly temp: Temp) {
    this.name = name
  }
    
  @Reflect.metadata("meta:type", "测试测试")
  public initStr: string = "";
  @setFn
  fn(arg: string[]): string {
    return ''
  }
}

let test = new MetaData('www', new Temp());
// 属性元数据三个参数：metaKey, target, propertyKey
// Reflect.getMetadata('design:type', target, key) 获取属性类型
// 除能获取属性类型外，通过 Reflect.getMetadata("design:paramtypes", target, key) 和 Reflect.getMetadata("design:returntype", target, key) 可以分别获取函数参数类型和返回值类型。
console.log(Reflect.getMetadata("meta:class", MetaData));
// console.log(test.initStr);

```

* 如上所述，就能拿到 construct 函数的参数类型（比如说是一个具体的类），就可以实现依赖注入了，把这个类的实例传给该组件。



# ts 的类型检查机制

## 1. 类型推断

* 第一种情况看上班

* 第二种情况发生在函数定义默认参数的时候

  ```typescript
  let c = (x = 1) => x + 1
  // 全部推断成 number
  ```

  * 最佳通用类型推断

  ```typescript
  let a = [1, '2']
  // number 和 string 的联合类型 	
  ```

### 从左向右的类型推断

* 通常发生在事件处理中

  ```typescript
  window.onkeydown = (event) => {
      
  }
  // event 就被推断为 内部的类型
  ```

### 覆盖类型推断

* 有时候 ts 不逼你更了解你的代码，你可以使用类型断言来覆盖类型推断（不推荐使用），改变上下文

```typescript
interface IFn {
    bar: number;
}
let fn = {} as IFn
fn.bar // 不报错
```


* 使用默认类型之后，在实际使用过程中的泛型就不需要指定具体的类型

## 2. 类型的兼容性

* 当一个类型 Y 可以被赋值给另一个类型 X 时，我们就可以说类型 X 兼容类型 Y。说白了 还是 duck typeing，就是说一个变量被规定了类型并且被赋值了，还是能被赋值为其他的类型.==兼容的一定是变量之间的兼容，直接用function定义的函数变量不能兼容，函数也必须用三种定义方式定义的才能兼容==

  口诀：

  结构之间兼容：成员少的兼容成员多的

  函数之间兼容：参数多的兼容参数少的
  
  普通类型（联合类型和普通类型）：多的兼容少的
  
  * 固定参数能够兼容可选参数和剩余参数
  * 可选参数不能兼容其他的参数
  * 可以修改tsconfig 中`strictFunctionTypes: false`，就可以互相兼容
  * 对象参数也符合这个特性
  
* 函数返回值的类型兼容

  返回值得类型兼容符合鸭子类型的特性

* ==函数重载就是一个关于参数和返回值的很好的例子==

```typescript
let fn11 = function(name: string): string {
    return name
}
let fn22 = function(name: string, age: number): string {
    return name
}
fn22 = fn11
```



### 函数兼容

1. 参数个数
2. 参数类型
3. 返回参数

### 枚举类型的兼容性

* 枚举类型和number类型是完全兼容的（非自定义的枚举）
* 枚举类型和枚举类型是完全不兼容的

### class 类的兼容性（类的实例比较）

* 类的兼容性：构造函数和静态成员不参与兼容性比较，只有成员属性参与比较；一旦有私有成员，则不兼容（父类和子类可以完全兼容）

### 泛型的兼容性

* 接口泛型，在没有使用具体的属性时，互相兼容
* 函数泛型，定义的函数可以互相兼容，因为没有在调用额时候传入具体的类型

# ts 类型保护

* ts 能够在特定的区块保证变量属于某种特定的类型

  可以在此区块中放心的使用该类型的属性和方法

## 1. instanceof

* 判断实例是否属于某个类

```typescript
 if (lang instanceof Java) {
        lang.helloJava()
    }
```



## 2. in 关键字

* 判断某个属性是否在这个对象中

  ```typescript
  if ('java' in lang) {
      lang.java
  }
  ```


## 3. typeof

* 只是能做到，但是只能一些Java中存在的基本类型

## 4. 创建类型保护的函数

* 返回类型谓词

```typescript
enum Type { Strong, Week }

let aa: Type.Strong = 1
let bb: Type.Week = 2
// aa = bb

class Java {
    helloJava() {
        console.log('is java')
    }
}

class JavaScript {
    helloJavaScript() {
        console.log('is javascript')
    }
}

function isJava(lang: Java | JavaScript): lang is Java {
    return (lang as Java).helloJava !== undefined
}
// 上面函数返回类型谓词，参数+is+类型

function getLanguage(type: Type) {
    let lang = type === Type.Strong ? new Java() : new JavaScript()
    // if (lang as Java) {
    //     (lang as Java).helloJava()
    // }
    if (isJava(lang)) {
        lang.helloJava()
    }
}

```

# 高级类型

## 1. 交叉类型

* 两个 interface 可以结合成一个类型，和联合类型不一样的是这两个类型的并集，改变量拥有了两个类型所有的属性。
* 联合类型是改变量在赋值的时候只能取其中的一种类型，交叉类型是取所有的类型的属性。但是交叉类型在使用的时候要注意，两种类型不能存在共同的属性，要不然会交叉出 never 类型。
* 交叉类型的两个类型必须没有共同的属性

```typescript
interface Test1 {
  name: string;
}
interface Test2 {
  age: number;
}

let obj: Test1 & Test2 = {
  name: '111',
  age: 111
}
```

* 交叉类型规则：共同的属性是两者的并集，交叉类型，如果没有公共的类型，则是never

```typescript
interface IName2 {
    name: string;
    age: string | number;
}
interface IAge {
    age: number | boolean;
}

let a2: IName2 & IAge = {
    name: '111',
    age: 111
}
```



## 2. 联合类型

* 联合类型可以是具体的字符串和数字

  ```typescript
  let a: '1' | '2' = '1'
  let b: 1 | 2 = 1
  ```

*  联合类型规则：interface 的公共属性会是两个的联合类型，非公共的则是具体的类型

### 使用联合类型的某些共同属性创建类型保护区域

* 存在的问题，让匹配不到的类型报错，undefined 的在ts 中最好能报错。在"漏斗"中，没有匹配到的类型，会一直到最后，每次脱去一个类型

  ```typescript
  interface Square {
      kind: 'square';
      length: number;
  }
  interface Rectangle {
      kind: 'rectangle';
      width: number;
      height: number;
  }
  interface Circle {
      kind: 'circle';
      radius: number;
  }
  
  let sha: Square | Rectangle = {
      kind: 'square',
      length: 1,
      // width: 1
  }
  
  type Shape = Square | Rectangle | Circle
  function area(s: Shape) {
      switch (s.kind) {
          case 'square':
              return s.length * s.length
          case 'rectangle':
              return s.width * s.height
          case 'circle':
              return s.radius * s.radius
          default:
          // 此时类型已经拖完，就是never类型
              return ((e: never) => {throw new Error(e)})(s)
      }
  }
  ```

* 为什么要这样做，ts 中最好让没考虑到的情况直接在编译阶段就报错。也是为了类型约束

## 3. 索引类型

### 索引类型的查询操作符

* `keyof T`

```typescript
interface Obj {
  name: string;
  age: number;
}
let a: keyof Obj
// a 就是所有属性的联合类型
```

### 索引访问操作符

* `T[k]`

```typescript
interface IObject {
    name: number;
    age: number;
}

type Test1 = keyof IObject
type Test2 = IObject['age']
```

* 最强应用

```typescript
function getValues<T ,K extends keyof T>(obj: T, keys: K[]): T[K][] {
    return keys.map(key => obj[key])
}

let obj1 = {
    name: '111',
    age: 111
}
getValues(obj1, ['name', 'age'])
```



## 4. 映射类型

* 把一个旧的类型映射成一个新的类型

1. 把一个接口所有属性变成一个只读的类型

   ```typescript
   interface Obj {}
   type ReadonlyObj = Readonly<Obj>
   ```

2. 变成可选属性

   ```typescript
   type PartialObj = Partial<Obj>
   ```

3. 抽取一些子集

   ```typescript
   type PickObj = Pick<Obj, 'a' | 'b'>
   // 第二个参数就是要抽取的 key
   ```

* 以上三种是 同态，不会创建新的类型

4. 加一些新的类型

   非同态

   ```typescript
   type RecordObj = Record<'x' | 'y', Obj>
   // 左侧是key，右侧是 value。左侧的 key 必须全部出现
   ```

## 5. 条件类型

`T extends U ? X : Y`

```typescript
// (A | B) extends U ? X : Y
// A extends U ? X : Y | B extends U ? X : Y
```



T 如果可以赋值给 U 类型，就返回 X 类型

### ==官方实现的条件类型==

* `Exclude<T, U>` 排除后边的类型
* NonNullable<T> 不是 undefined 和null
* Extract<T, U> 从 T 中抽取出可以赋值给 U 的类型
* `RecturnType<T>` 获取函数返回值的类型

```typescript
type Diff<T, U> = T extends U ? never : T

let diffType: Diff<'a' | 'b', 'a' | 'c' | 'e'> = 'b'

type NotNull<T> = Diff<T, null | undefined>

let type2: NotNull<string | undefined | number> = 1

let type3: Exclude<'a' | 'b', 'a' | 'e' | 'f'>

let test4: Extract<'a' | 'b', 'a' | 'c' | 'e'>

let test5: NonNullable<'t' | null | 'a'>

let test6: ReturnType<() => number> = 1
```





## 6. 其他官方实现的类型-utility type

https://www.typescriptlang.org/docs/handbook/utility-types.html

* Parameters
* Uppercase 字符串大写

# 注意

* 所有的类型都是在定义的时候规定的，在赋值的右边是不需要去定义类型的
* 类型只能继承类型
* 所有类型和 never 的联合类型，类型不变

## 字面量和变量

* js 中常规的类型就是字面量，比如 object

## 一些小技巧

* 如果一个 undefined 的值需要赋值给一个 string 类型的数据，可以使用 `!` ——感叹号

  ```tsx
  let aa: string
  
  aa = undefined!
  ```

  * 避免严格模式中 undefined 不是指定类型的子集

  

# 工程篇



# tsconfig.json 文件

* ts 项目中 `tsconfig.json`文件能配置 ts检查的规则
* 在项目中只要配置了 ts-loader 规则，就会自动读取 tsconfig 文件的配置
* 顶层配置：`compilerOptions`，`files`，`include`，和`exclude`、`extends`

## 1. null 和 undefined 问题

* null 和 undefined 是所有类型的子类型，本来可以赋值给任何类型的变量，需要配置`strictNullChecks`为 false

  ```js
  {
      "compilerOptions": {
          "strictNullChecks": true,
          //...
      }
  }
  ```

  

## 2. strictFunctionTypes

可以修改tsconfig 中`strictFunctionTypes: false`

## 3. 基本配置

```js
{
  "target": "es5",  // 配置编译成的js 版本。可以用来配置编译的js版本
  "module": "commonjs", //编译成的模块系统
}
```

* tsc 命令行指定编译版本

  ```shell
  tsc ./test.ts -t es3
  # -t 代表 target
  ```

## 4. 模块化

* es6 和 commonjs 的区别，commonjs 没有顶级导出，module.exports 会覆盖所有，es6 则可以将 default 和普通的导出共存

* ts 做了处理，统一采用commonjs 导出，配置：`module`

  default 导出使用 `exports['default'] `的方式

  所有的模块都会加上 default 属性，因此在 node 模块中使用 es6 导出的数据的时候，可以使用 `obj.default`来获取——==tsconfig 的配置`module: commonjs`==，在实际的开发中，一般会配置成为 `ESNext`
  
  

### es module 和 commonjs 兼容

虽然 ts 支持两种模块的书写，但是在使用的时候使用 `a.default` 的方式，还是容易出错的。提供两种解决方案：

1. 不要混用
2. 必须混用的情况使用 ts 推荐的混用方式。



* ts 本身支持两种模块系统

* esm 允许有一个顶级的导出 default 和 一些次级的导出

  commonjs 只有一个顶级的导出

* 尽量避免 es6 和 commonjs  模块混用，如果要混用 ts 提供了混用的方案

导出：

```typescript
export = {
  name: 111
}
// 所有的都要合并成一个导出，类似于 commonjs
// 不能有其他的导出，否则报错。有且只有一个 export 导出对象。只有一个顶级对象？
```

引入：

```typescript
import test = require('./test.ts')
```

如果在 tsconfig 中配置了

```js
{
  esModuleInterop: true
}
```

就可以使用 es6 的方式导入

```js
import test from './test.js'
```



## 5.命名空间

js 中命名空间能够有效避免全局污染，es6 引入模块系统之后，就很少提及了。

但是在使用一些全局的类库的时候，命名空间仍然是一个好的解决方案。

* ts 命名空间使用 `namespace` 来写

```typescript
namespace Shape {
  const pi = Math.PI
  export function circle(r: number) {
    return pi*r**2
  }
  // 导出的数据全局可见的成员
}
  
// 使用
  Shape.circle(1)
```

*  **不同文件使用相同的命名空间，能共享数据? **
* ==不要在模块中使用命名空间，直接在全局中使用==
* 命名空间的使用，直接编译成 js 文件在html中用script 引入（推荐）
* 书写格式：类似于一个代码块，能够按行书写代码。

不同文件对命名空间的调用

```typescript
/// <reference path="a.ts" />
```

### 命名空间的拆分

* 使用 同名的命名空间，能够拆分命名空间。共享一个命名空间。

  如果需要使一个命名空间文件引入另一个命名空间文件在 ts 编译的时候不报错，需要执行

  ```tsx
  /// <reference path='b.ts'/>
  ```

* 如果需要两个命名空间在执行代码的时候不报错，需要在页面中同事引入这两个命名空间文件，因为他们是同一个全局变量，需要都绑定到全局变量上。



### 命名空间别名

```tsx
import a = Shape.circle
```





### 5.1 命名空间和模块补充

“内部模块”现在称做“命名空间”。 “外部模块”现在则简称为“模块”

### 命名空间编译

* 命名空间会编译成为一个全局对象

```tsx
export namespace Test {
    export const Kind = 'kind'
}

// 编译之后
export var Test;
(function (Test) {
    Test.Kind = 'kind';
})(Test || (Test = {}));
```



### 现代使用方式

* 命名空间就是一个对象，可以在一个模块内多次使用；如果一个文件内就一个命名空间，建议直接导出模块。使用者会重新命名的。

* 模块是对外的。

## 6. 声明合并

* 合并同名的声明变量，能够方便的使用全局变量，避免重复命名

接口表示函数

接口声明合并顺序

命名空间 + 命名空间 不能实现相同的属性

命名空间 + 函数

命名空间 + 类 相当于给类添加了一些静态的属性

命名空间 + 枚举   

命名空间 一定要放后边

1. 接口声明合并规则

   非函数成员，要求唯一性，要不就一模一样

   函数成员：每一个函数相当于函数重载，实现的时候需要一个更宽泛的类型

   顺序：接口内部从上到下，接口外部从下到上。如果函数的参数是一个字符串字面量（具体的值），就会被提升到最上边

## 如何编写声明文件

### 1. 声明文件编写

* 类库分为三类：全局类库、模块类库、umd 类库

* 在使用js 编写的类库的时候，一定要编写一个声明文件，为这个类库暴露api，比如说 jQuery

* 一般的类库声明文件都被写好了

  安装：

  ```shell
  npm i @types/jquery -D
  ```

#### 声明文件起作用

* 单独的模块，在同级目录下编写 d.ts 声明文件
* 工程化，配置declarationDir属性
* 一般情况下，自己写的模块用namespace来写声明文件（全局类库）；外部的模块用 module来写声明文件（模块类库）

### 2. 声明模块插件(添加约束)

1. 给引入的第三方模块添加方法

   ```typescript
   import m from 'moment'
   declare module 'moment' {
     export function myFn(): void
   }
   m.myFn = () => {}
   ```

2. 给全局定义插件(不推荐使用，避免全局污染)

   ```typescript
   declare global {
     export globalLib {
       function doSomething(): viod
     }
   } 
     // 调用
     globalLib.doSomething = () => {
       
     }
   ```

### 类库的声明文件

* 在 @types 中找到响应的类库的声明文件，下边的 package.json 文件中的 types 字段指定声明文件，一般都是 d.ts 文件

### 声明文件使用到的命名空间

* 在声明文件中`declare`的命名空间是约束的类型(而且需要在 .d.ts 文件中)，定义的命名空间必须使用 export 字段。



## 装饰器

总结

1. 属性是 readonly 不能直接修改

2. 先执行装饰器函数，后执行赋值操作。

   不能直接进行劫持工作，需要使用 defineProperty 来进行

### 类装饰器

* 能能够修改构造函数，重新返回 class 对原类进行改造。

  只能往下继承，意味着不能直接使用 target 去继承，但是可以返回一个 继承 target 的类，实现对target类的扩展。

```tsx
function classDecorator<T extends {new(...args:any[]):{}}>(constructor:T) {
    return class extends constructor {
        newProperty = "new property";
        hello = "override";
    }
}

@classDecorator
class Greeter {
    property = "property";
    hello: string;
    constructor(m: string) {
        this.hello = m;
    }
}

console.log(new Greeter("world"));
```





## tsconfig.json 文件

* 编辑器会编译 ts、tsx、d.ts 文件

```typescript
{
  "files": [
    "src/a.ts"
  ],
    // 需要编译的单个文件列表
   "include": [
     "src/*"
     // src 一级目录下的文件 
   ],
     // 编辑器需要编辑的目录
   "exclude": [
     "src/libs"
   ],
     // 需要排除的文件，默认会排除 node_moudles 文件 
   "compileOnSave": true
  // 保存自动编译，vscode不支持
}
```

* 配置文件是可以继承的，我们可以抽离出一些公共的配置 ，也可以在继承之后覆盖配置

比如：txconfig.base.json

```typescript
{
  extends: "./tsconfig.base.json"
}
```

```typescript
{
  "compilerOptions": {
      // "incremental": true,                // 增量编译，第一次编译生成一个编译信息的文件，在第二次编译的时候做增量编译，提高编译速度
      // "tsBuildInfoFile": "./buildFile",   // 增量编译文件的存储位置
      // "diagnostics": true,                // 打印诊断信息

      // "target": "es5",           // 目标语言的版本
      // "module": "commonjs",      // 生成代码的模块标准
      // "outFile": "./app.js",     // 将多个相互依赖的文件生成一个文件，可以用在 AMD 模块中

      // "lib": [],                 // TS 需要引用的库，即声明文件，es5 默认 "dom", "es5", "scripthost"，比如引入es的新特性，"ES2019.Array"，会有提示

      // "allowJs": true,           // 允许编译 JS 文件（js、jsx）
      // "checkJs": true,           // 允许在 JS 文件中报错，通常与 allowJS 一起使用
      // "outDir": "./out",         // 指定输出目录
      // "rootDir": "./",           // 指定输入文件目录（用于输出），一般情况下可以设置成 ./src，要不然会吧src同级的也输出

      // "declaration": true,         // 生成声明文件
      // "declarationDir": "./d",     // 声明文件的路径
      // "emitDeclarationOnly": true, // 只生成声明文件，不生成js文件
      // "sourceMap": true,           // 生成目标文件的 sourceMap
      // "inlineSourceMap": true,     // 生成目标文件的 inline sourceMap
      // "declarationMap": true,      // 生成声明文件的 sourceMap
      // "typeRoots": [],             // 声明文件目录，默认 node_modules/@types
      // "types": [],                 // 声明文件包

      // "removeComments": true,    // 删除注释

      // "noEmit": true,            // 不输出文件，只做类型检查
      // "noEmitOnError": true,     // 发生错误时不输出文件

      // "noEmitHelpers": true,     // 不生成 helper 函数，需额外安装 ts-helpers，下边这个打开就不需要安装这个插件了，生成helper函数会造成代码体积变大。
      // "importHelpers": true,     // 通过 tslib 引入 helper 函数，文件必须是模块

      // "downlevelIteration": true,    // 降级遍历器的实现（es3/5）

      // "strict": true,                        // 开启所有严格的类型检查，下边的属性都会变成 true 
      // "alwaysStrict": false,                 // 在代码中注入 "use strict";
      // "noImplicitAny": false,                // 不允许隐式的 any 类型
      // "strictNullChecks": false,             // 不允许把 null、undefined 赋值给其他类型变量
      // "strictFunctionTypes": false           // 不允许函数参数双向协变
      // "strictPropertyInitialization": false, // 类的实例属性必须初始化
      // "strictBindCallApply": false,          // 严格的 bind/call/apply 检查
      // "noImplicitThis": false,               // 不允许 this 有隐式的 any 类型

      // "noUnusedLocals": true,                // 检查只声明，未使用的局部变量
      // "noUnusedParameters": true,            // 检查未使用的函数参数
      // "noFallthroughCasesInSwitch": true,    // 防止 switch 语句贯穿
      // "noImplicitReturns": true,             // 每个分支都要有返回值, if else 都要有返回值

      // "esModuleInterop": true,               // 允许 export = 导出，由import from 导入
      // "allowUmdGlobalAccess": true,          // 允许在模块中访问 UMD 全局变量
      // "moduleResolution": "node",            // 模块解析策略
      // "baseUrl": "./",                       // 解析非相对模块的基地址，也就是node_modules文件
      // "paths": {                             // 路径映射，相对于 baseUrl，比如像要min版本文件
      //   "jquery": ["node_modules/jquery/dist/jquery.slim.min.js"]
      // },
      // "rootDirs": ["src", "out"],            // 将多个目录放在一个虚拟目录下，用于运行时。比如说有一个类库在 out 目录下，但是在src引用他，最后两个目录应该是同级的，而不是两个目录

      // "listEmittedFiles": true,        // 打印输出的文件
      // "listFiles": true,               // 打印编译的文件（包括引用的声明文件）
  }
}
```

* sourceMap 就是为了方便查找文件

### 工程引用

* 比如说前后端在一个项目中，不同的文件夹，每个项目配置自己的配置文件，三个项目文件夹，common，client、server
* 外层基础配置

```typescript
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "strict": true,
    "composite": true, // 工程能被引用，增量编译
    "declaretion": true //工程引用必备
  }
}
```

* 前端项目

```typescript
{
    "extends": "../../tsconfig.json",
    "compilerOptions": {
        "outDir": "../../dist/client",
    },
    "references": [
        { "path": "../common" }
    ]
}
```

* 公共模块

```typescript
{
    "extends": "../../tsconfig.json",
    "compilerOptions": {
        "outDir": "../../dist/common",
    }
}
```

* server端

```typescript
{
    "extends": "../../tsconfig.json",
    "compilerOptions": {
        "outDir": "../../dist/server",
    },
    "references": [
        { "path": "../common" }
    ]
}
```

* `references`配置了引用的工程

新增编译命令

```shell
tsc -d 
# 编译  并且有声明文件
tsc -b src/server --verbose --clean
# 编译 build，多个工程依赖，编译其中一个依赖项目自动编译，verbose编译信息，clean 清空构建文件
```

#### 全局配置声明文件（实战）

* 在 react中推荐 import 引入样式，但是ts中不识别引入，需要在 d.ts 中声明

```typescript
declare module '*.css'
```

在最外层定义一个 声明文件`declaration.d.ts`

然后在 tsconfig 文件中配置加载声明文件

```typescript
{
  "include": [
    "./src/",
    "./declaration.d.ts"
  ],
}
```

* ==需要在tsconfig中include配置项中引用该声明文件==



### 配置继承_extends

* 如果有多个项目有相同的ts配置，则可使用继承一个公共配置的方式来组织代码。不用每次都重新写。

`tsconfig.json`文件可以利用`extends`属性从另一个配置文件里继承配置。

`extends`是`tsconfig.json`文件里的==顶级属性==（与`compilerOptions`，`files`，`include`，和`exclude`一样）。 `extends`的值是一个字符串，包含指向另一个要继承文件的路径。

* 会覆盖原文件

  在原文件里的配置先被加载，然后被来至继承文件里的配置重写。 如果发现循环引用，则会报错。

#### 路径规则

* 同esmodule的规则，可以使用相对路径和第三方模块。



## 编译工具

### 从ts-loader到babel

* ts-loader滴啊用了 tsc 命令，共享了外层的 tsconfig 文件，ts-loader 还有一些自己的配置

```typescript
{
  use: [{
    loader: 'ts-loader',
    options: {
      transpileOnly: false, //开启之后只做编译，不做类型检查，加快编译速度
    }
  }]
}
```

* 需要一个插件，会把类型检查放在一个独立进程中去进行

```shell
npm i fork-ts-checker-webpack-plugin -D
```

使用：

```js
const ForkTsCheckerWebpackPlugin = require('fork-ts-checker-webpack-plugin')
// .....

{
  plugins: [
    new ForkTsCheckerWebpackPlugin()
  ]
}
```

#### awesome-typescript-loader

* 与 ts-loader 相比：

  * 更适合 babel 继承，使用 babel的转义和缓存
  * 不需要安装额外的插件，类型检查在独立的进程中进行

* 安装：

  `npm i awesome-typescript-loader -D `

* 使用，如果设置只做编译，需要打开自带的插件做类型检查(类型检查有却选，不建议使用)

```js
const { CheckerPlugin } = require('awesome-typescript-loader')

{
  use: [{
    loader: 'awesome-typescript-loader',
    options: {
      transpileOnly: false //开启之后只做编译，加快编译速度
    }
  }]
}

// ....

{
  plugins: [
    new CheckerPlugin()
  ]
}
```

### babel 处理

* babel7 之前不能处理 ts，新版本之后 使用babel的生态去做编译，typescript 只做类型检查，利用 babel的生态去做编译的事。构建系统的统一，可维护性

* 需要执行  tsc --init 生成配置文件，然后打开配置中的 `"noEmit": true, `只做编译，不产出编译文件

  需要单独一个命令 `tsc --watch`

#### babel 配置工程过程

* 首先 npm init 一个项目

1. 安装包

   `yarn add @babel/cli @babel/core @babel/plugin-proposal-class-properties @babel/plugin-proposal-object-rest-spread @babel/preset-env @babel/preset-typescript -D`

   ```js
   "devDependencies": {
       "@babel/cli": "^7.10.5", // babel 必须
       "@babel/core": "^7.11.4", // babel 必须
       "@babel/plugin-proposal-class-properties": "^7.10.4",
       "@babel/plugin-proposal-object-rest-spread": "^7.11.0", // 支持剩余扩展标识符 
       "@babel/preset-env": "^7.11.0", // babel 必须
       "@babel/preset-typescript": "^7.10.4" // 编译ts文件
     }
   ```

2. 新建`.babelrc`文件，一个json文件，里面的配置就是包名称

   ```js
   {
       "presets": [
           "@babel/env",
           "@babel/preset-typescript"
       ],
       "plugins": [
           "@babel/proposal-class-properties",
           "@babel/proposal-object-rest-spread"
       ]
   }
   ```

3. 新建 src 文件测试

4. 编译脚本

   ```js
   {
     "scripts": {
       "build": "babel src --out-dir dist --extensions \".ts,.tsx\"" // 指定扩展名
     },
   }
   ```

   * 配置完成，但是没有类型检查

5. 安装 ts 

   ```shell
   npm i typescript -D
   ```

   新建配置文件

   `tsc --init `

   开启 ts 配置

   `"noEmit": true` 只做类型检查，不做编译

6. 编写类型检查脚本

   `"type-check": "tsc --watch"`，开启类型检查，需要重新打开一个终端

注意事项

1. 命名空间在编译的时候会出错，不要使用
2. 类型断言用 as
3. 常量枚举会报错
4. export = s 也会报错

### 代码检查

* tslint 会向 eslint 迁移 （以下的不建议在 babel 项目中使用，babel项目中建议使用 babel-eslint）

![31d4a73a683061578a346943a9e75dad](./picture/tsPic/31d4a73a683061578a346943a9e75dad.png)

* 官方提供了 typescript-eslint 工具来讲 ts 树转换成 es 生成的树

1. 安装：

   `yarn add eslint @typescript-eslint/eslint-plugin  @typescript-eslint/parser -D`

   package.json:

   ```js
   {
     	"@typescript-eslint/eslint-plugin": "^3.9.1",
       "@typescript-eslint/parser": "^3.9.1",
       "eslint": "^7.7.0",
   }
   ```

2. 新建 eslint 配置

   新建文件 `.eslintrc.json`

   ```js
     
   {
     "parser": "@typescript-eslint/parser",
     "plugins": ["@typescript-eslint"],
     "parserOptions": {
         "project": "./tsconfig.json" //需要使用的类型信息
     },
     "extends": [
       "plugin:@typescript-eslint/recommended" // 官方默认指定的规则，可以不用，设置成 off 即可
     ],
     "rules": {
       "@typescript-eslint/no-inferrable-types": "off" // 关闭的规则
     }
   }
   ```

3. 脚本

   `"lint": "eslint src --ext .js,.ts"`

### 单元测试

* 单元测试也分为 babel 系和非babel
  * ts-jest
  * babel-jest
  
* babel-jest

  1. 安装

     `yarn add jest babel-jest @types/jest -D`

  2. 脚本

     ` "test": "jest"`

  3. 别忘了安装 node 声明文件，要不然不能使用 require 等语法

     `yarn add @types/node -D`



# ts项目

## 配置文件

参考：https://www.tslang.cn/docs/handbook/tsconfig-json.html

* tsconfig.json 查找规则

  不带任何输入文件的情况下调用`tsc`，编译器会从当前目录开始去查找`tsconfig.json`文件，逐级向上搜索父目录。

##

# 创建一个使用 typescript 的 node 项目

## node 识别 ts 文件

* 可以安装 ts-node 来实现

  ```shell
  yarn add ts-node -g
  yarn add @types/node -D
  ```

* 执行：

  ```shell
  ts-node ./test.ts
  ```

  

## 热更新

```shell
$ nodemon -e ts --exec ts-node --files src/main.ts
```

* 监听 ts 的改变



## 上线

* 需要用tsc编译成js文件

  https://www.zhihu.com/question/349707413/answer/850705288

# 创建一个 react 项目

## 1. 环境

1. 安装

   `yarn add react react-dom`

   `yarn add @types/react @types/react-dom`

2. ts 配置: 在 compilerOptions 中，要不然会出现 ts 报错！

   ```json
   {
     "jsx": "react", //'preserve'(jsx文件，保留jsx 语法), 'react-native'（.js 文件 jsx 语法）, or 'react'（js 文件 js 语法）.
   }
   ```

3. 打包优化

   库文件和业务文件拆分成两个文件，这样就能充分利用浏览器的缓存，webpack4提供了一个新的拆包方式，能快速解决这个问题

   webpack配置：就可以吧 npm 下载的包，打包成一个 vendors 文件，业务代码打包成 app 文件 ，在修改业务代码后，app的hash值改变了，venders的hash没有改变

   ```js
   {
     optimization: {
       splitChunks: {
         chunks: "all"
       }
     }
   }
   ```

   

## 2. 脚手架安装

* 使用 npx 避免全局安装

* `npx create-react-app ts-react-app --typescript`

* Webpack  都被封装到了 react-scripts 里面，如果想自定义，暴露了一个脚本

  ```js
  {
    "scripts": {
      "start": "react-scripts start",
      "build": "react-scripts build",
      "test": "react-scripts test",
      "eject": "react-scripts eject"
    },
  }
  ```

* 安装依赖

  ```json
  {
    "devDependencies": {
      "@types/react-router-dom": "^5.1.5",
      "babel-plugin-import": "^1.13.0", // 实现antd的按需加载
      "customize-cra": "^1.0.0", // 实现脚手架自定义
      "http-proxy-middleware": "^1.0.5",  // 实现脚手架自定义
      "http-server": "^0.12.3",  // 搭建mock server
      "react-app-rewired": "^2.1.6" // 同上
    }
  }
  ```

  

## 3. react 组件约束

![react-component](./picture/tsPic/react-component.png)

### hoc 约束

hoc 函数

```tsx
import React, { Component } from 'react'

interface ILoading {
    loading: boolean;
}
function HocFn<P> (ReactCom: React.ComponentType<P>) {
    // const { isLoading } = props
    return class extends Component<ILoading & P> {
        render() {
            const { loading, ...props } = this.props
            return loading
            ?
            <div>loading 中</div>
            :
            <ReactCom {...props as P} />
        }
    }
}

export default HocFn
```

使用：

```tsx
const CompNew = HocFn<{name: string}>(Hello) // 在此约束的要传递的类型

<CompNew loading={false} name={'我是生产的组件'}></CompNew>
```





# 类型体操

## 1. 链式调用的限制

```typescript

interface ITest1 {
    test1(): ITest2
}

interface ITest2 {
    test2(): void
}

// let test: ITest2 & ITest1 = {

// }

type NewType = ITest2 & ITest1
class Test implements NewType {
    test1() {
        return this
    }
    
    test2() {

    }
}

let test: ITest1 = new Test()

test.test1().test2()
// 能够约束每一个方法的类型
```



## typescript 集成

### Omit

* 不能是某个类型的某个值的类型

```tsx
let test2: Omit<Test, 'test1'> = test.test1
// 报错
```

## 文本类型的扩展——typescript 怎么处理const的

```typescript
const test = '111' // test就是'111'类型，因为不可修改
let test1 = test // test1就是string类型，可变的
```

* 如果不想让扩展，就不能使用ts的类型推断，直接规定类型就可



## as const 问题

```typescript
const Test = ['111']
// 此时，Test 被推断为一个 string[] 类型，不符合常量的要求
const Test1 = ['111'] as const
// 此时 Test1 被推断为一个 readonly 的常量符合要求。常量数组

```

* 类似于 只读数组。不可以对其进行扩展和修改的操作，可以作为基础类型。

```tsx
const arr1: readonly number[] = [1, 2];
```

## typescript 关键字

注意：所有的关键字不能够在interface使用，必须在type关键字中使用



### in 关键字

* 该类型是后面类型中的一个，只能在 object 的key中使用。

* 在写类型逻辑的时候使用，比如官方实现的 Pick 功能。

```tsx
type MyPick<T, U extends keyof T> = {
    [prop in U]: T[U]
}
```

### keyof

* 如上：某个 object 类型的 key。==一般情况只能在类型定义的左边使用，而且必须使用继承去承接==。
* ==只要使用了 extends 就代表类型已经知道了部分约束，因此一般只能写在左边==

### T[U] 

* 如上表示类型中的某些key，类型中的某个值怎么表示——T[U]

### extends

* 继承一个类型

* 如上：可以继承一个接口，也可以继承一个具体的类型。

```typescript
type Test2<T extends '1' | '2'> = T

let test1: Test2<'1'> = '1'
```

* 在函数中可以不传入



### is 关键字

* 一般能够在函数返回。返回一个Boolean值

```typescript
type ITest = '11'
function fn(obj): obj is ITest { // 约束之后必须返回一个 Boolean 值
  
}
```

### typeof

类型中的typeof是一个类型和js中的typeof不一样

* ==ts冒号的右侧一定是一个类型==。如果用在类型部分，就是当前的真实类型，如果用在逻辑部分，就是js的typeof出来的模糊值

```tsx

let test = '1'

let test1: typeof test = ''
// 这样是合理的，但是 test 的位置不能为一个常量
```

* 类型体操

  怎么样拿到object获取array的所有值类型

```typescript
const test = {test: 2} as const

let test1: (typeof test)[keyof (typeof test)] // 2

const arr = [1,2,3] as const;
let test2: (typeof arr)[number] // 1,2,3

// 如果不加const就会返回一个number取代2，和上班的文本类型扩展对应，只有const能够固定基础的类型
```



### ReturnType

* 如果定义了一个函数，想拿到函数返回的类型，可以使用这个关键字

```typescript
function fn() {
    return 1
}
type Test = ReturnType<typeof fn>
```

### readonly

* 不仅能在interface中使用，还能在 type 关键字中使用。不是class关键字。
* class属性关键字只能在 interface 中使用不能在type中使用



## 高级技巧

### 接口泛型

* 如果一个接口里的变量需要互相联动，可以使用接口泛型

```typescript
interface Props<T = string | Array<string>> { // 可能是string或者Array<string>
    value: T | undefined,
    onChange: (e: SelectChangeEvent<T>) => void, // 类型有问题
}
```

* 如上，value和onchange就可以联动



### 其他关键字

#### Function

* ts支持Function关键字，是所有函数的统称

  ```typescript
  type T2 = Exclude<string | number | (() => void), Function>
  ```



#### infer

* `infer` 最早出现在此 [PR](https://github.com/Microsoft/TypeScript/pull/21496) 中，表示在 `extends` 条件语句中待推断的类型变量。

  * 必须要extends条件中
  * 然后待推断

  ```typescript
  type AwaitedTest<T extends Promise<any>> = T extends Promise<infer P> ? P : never
  // infer后面的P待推断
  
  type Test4 = Promise<string>;
  
  let awaitedTest: AwaitedTest<Test4>
  ```




#### declare

* 如果一个变量暂时没有被赋值，然后又知道这个变量的类型，可以直接使用 declare 来声明，就可以使用该变量的属性了。可以声明各种类型

```typescript
declare const config: Chainable
// 如果用 const config: Chainable 就报错了，因为没有赋值
```

* ==所有declare 的类型都需要赋值实体==

```typescript
declare const test23: typeof PromiseAll // 需要实体

// 这种方式泛型不需要填
declare function PromiseAll<T extends Array<any>>(promises: readonly [...T]): Promise<{
    [P in keyof T]: T[P] extends Promise<infer S> ? S : T[P]
}>
```

#### 加可选&减可选 `+?` `-?`

一般用在给转录一个类型的时候控制其中的可选类型的。

```typescript
type IType = {
    name?: string;
    age: number;
}
// 把所有的子元素变为可选的
let o: {
    [K in keyof IType]+?: string;
}
// 把所有子元素变为不可选的
let o1: {
    [K in keyof IType]-?: string;
}
```

### 类型逻辑

#### 三元计算

* 在类型区域可以直接使用三元运算符

   ```typescript
   type First<T extends any[]> = T extends [] ? never : T[0]
   ```

* ==由于类型区域没有全等运算符，能够只用extends代替==



#### 单个类型和联合类型比较

* 在类型中怎么判断一个类型是不是一个联合类型中的一个？

  使用 extends

  ```typescript
  
  type isPillarMen = Includes<['Kars', 'Esidisi', 'Wamuu', 'Santana'], 'Dio'> // expected to be `false`
  
  type Includes<T extends Array<any>, U> = U extends T[number] ? true : false
  // U extends T[number]，U是联合类型中一个
  ```

  



#### 类型中的属性的使用

* ==可用属性来获取类型的值==，但是要确定该属性肯定存在。

* 一共有两种属性

  * 自定义的属性

    ```typescript
    interface IProp {
        test: string;
    }
    type IPropTest = IProp['test']
    ```

  * js自带属性，比如说数组自带length类型，可以直接使用

    ```typescript
    type TArray = Array<unknown>
    type TArrayTest = TArray['length']
    ```



#### 联合类型在逻辑中会一项一项执行

```typescript
type IExclude = MyExclude<'test' | 'test1', 'test'>

type MyExclude<T, U> = T extends U ? never : T // 会一项一项测试找到符合的
```



#### 数组的扩展运算符

* 在类型中可以使用数组的扩展运算符

```typescript
type Result = Concat<[1], [2]> // expected to be [1, 2]

type Concat<T extends Array<any>, U extends Array<any>> = [...T, ...U]
```

* ==经过测试对象的扩展运算符不能使用==



#### 函数在类型逻辑中的使用

* 函数一定要写输入输出的类型

  ```typescript
  type MyParameters<T> = T extends ((...args: infer P) => any) ? P : never
  
  // args需要写类型，还可以使用扩展运算符
  ```

  

#### 值在类型中的使用

* 一个具体的值想要在类型系统中使用，可以使用 typeof 字段

#### 递归的使用

* 在typescript类型系统中，可以自己调用自己（类似于函数中递归）

```typescript
type DeepReadonly<T> = {
    readonly [P in keyof T]: (T[P] extends object ? DeepReadonly<T[P]> : T[P])
}
```



#### 类型变量

```typescript
/**
 * 两个知识点：1. 类型的泛型可以作为一个变量保存当前的类型，需要给一个默认值
 * 2. 函数的泛型可以默认不填
 */

type Chainable<T = {}> = {
    option<Key extends string, Value>(key: Key, value: Value): Chainable<T & Record<Key, Value>>,
    get(): T,
}
```

* 可以给一个默认值，然后把其当成变量去使用
* 还有一种就是 infer，不过只能在 extends 中使用



#### 赋值影响类型

* 一般情况下赋值是不能影响类型的——依赖倒置，依赖于接口编程
* 如果类型中的类型确实需要填值的类型，目前只能使用infer



#### 遍历数组或对象

* 在类型中遍历数组和对象使用的方案是一样的

```typescript
interface PromiseAll<T extends Array<any> = any[]> {
    (promises: readonly [...T]): Promise<{ // 遍历数组
        [P in keyof T]: T[P] extends Promise<infer S> ? S : T[P]
    }>
}
```



#### 遍历字符串

* 字符串没有直接遍历的方式，但是可以用过递归的方式去查找，直到找到符合的要求

  ```typescript
  type TrimLeft<T extends string> = T extends `${" " | "\n" | "\t"}${infer R}` ? TrimLeft<R> : T
  // 自己调用自己知道去除完所有的空字符
  ```

* 只要写成两个infer，后面的infer一定是一个rest

  ```typescript
  type capitalized = MyCapitalize<'hello world'> // expected to be 'Hello world'
  
  type Test27 = Uppercase<'aaa'>
  
  type MyCapitalize<T> = T extends `${infer F}${infer R}` ? `${Uppercase<F>}${R}` : T
  ```


* 长度大于等于1的才能递归，小于1就会走else



#### 只有数组的长度是一个常量（真实值）

* 如题，string的长度是一个number类型

#### 表达式作为数组中的一员——三元

* 表达式作为数组中的一个元素，需要转换成这个元素

  ```typescript
  type Flatten<T extends Array<any>> = T extends [infer L, ...infer R]
      ? [...(L extends Array<any> ? Flatten<L> : [L]), ...Flatten<R>] : []
  // 三元作为数组中的一个元素
  ```



#### never类型在类型计算中的作用

* never类型的数组是所有类型数组的子类型，如果要判断请和never类型本身去判断。==只限数组哦==
* never类型和所有类型的联合类型都是该类型本身，不会修改合并的类型



#### 一个空对象在逻辑中怎么表示

* 如果一个空对象 {} 需要在extends中表示，需要使用 `{[prop: string]: never}` 才能正确表示

```typescript
type Sample1 = AnyOf<[1, "", false, [], {}]>; // expected to be true.
type Sample2 = AnyOf<[0, "", false, [], {}]>; // expected to be false.

type AnyOf<T extends Array<any>> = T[number] extends (0 | "" | false | [] | {[propName:string]:never})
    ? false
    : true
```



#### 常用操作符、运算符

* https://juejin.cn/post/7019914200273125407#heading-0

#### 限制对象key的取值范围

* 开发中常用限制对象的key为一个联合类型，但是有一个问题，那就是所有key都必须实现。这个不是我们所期望的

  可以使用 Partial 把它们都变成可选类型，这样就能限制他们的范围，并且不能超出范围。



### 类型逻辑中的问题

#### 两个infer

* 一般情况下两个 infer 不能都使用infer后的类型作为逻辑中的类型

```typescript
type Test28<T> = T extends `${infer L}${" "}${infer R}` ? L : R
// 报错
```

==更正：== 所有的infer操作，只能放到extends的前边，不能放到后边。infer的变量不能放到 : 后面。

#### 函数在逻辑中的问题

* 参数一定要有形参，不能直接写类型



### 从联合类型中选取一个

* 只能使用交叉类型实现，==联合类型和交叉类型可以互相成就==

```typescript
type MyPickTest<T, U> = U & T;
let testPick: MyPickTest<Action1, {method: 'resize-column'} | {method: 'hide-column'}> = {
    method: 'resize-column',
    id: '',
    width: 1,
}
```



### 服务端 ts 配置(node)

服务端和客户端 ts 配置是不一样的。

* ts-config

  这一条非常重要，需要解析成 commonjs。

  https://stackoverflow.com/questions/62096269/cant-run-my-node-js-typescript-project-typeerror-err-unknown-file-extension

```json
{
  "compilerOptions": {
    "module": "commonjs"
  }
}
```

* Package.json

  去掉 type: module



### node 端ts

* 全局对象时 global。官方定义为 globalThis

### ts-node配置

* tsconfig.json 配置

  ```typescript
  {
    "comment": "ts-node配置，需要这是files。参考：https://stackoverflow.com/questions/51610583/ts-node-ignores-d-ts-files-while-tsc-successfully-compiles-the-project",
    "ts-node": {
      "files": true,
      // It is faster to skip typechecking.运行时不进行类型检查，加快运行速度
      // Remove if you want ts-node to do typechecking.
      "transpileOnly": true,
    },
    "files": [
      "src/**/*.d.ts",
      "src/**/*.ts",
      "config/**/*.d.ts",
      "config/**/*.ts",
    ]
  }
  ```

### mocha等js库运行ts文件

```shell
npx mocha --require ts-node/register test/*.ts
```

### 指定模块声明文件查找位置——设置 paths（==还可以设置alias别名==）

* 在开发 webpack 的时候，webpack 本身有声明文件，但是写的有问题。想引用 @types/webpack 的声明文件

```ts
{
  "paths": { // 设置paths 可以指定声明文件查找位置
      "webpack": ["node_modules/@types/webpack"] // 这个能修改类型去哪里找，而不是默认去包下找 type 字段
    },  
}
```





## 类型默认值

* 可以使用 `= 和 extends`来给类型添加默认值

  *=相当于默认值，在使用该类型的时候不需要传递值也可执行*

  extends代表限定类型，使用的时候必须传值，传递的值必须符合继承关系

* 更准确的是 = 是默认值，extends是限定关键字

==默认值相当于函数的参数，有默认值可以不用传值，没有默认值一定要传值==

* 函数function的写法不需要写泛型就可以使用

  ```typescript
  function fn<T extends string>(arg: T): T {
      return arg
  }
  fn('111') // 不需要填泛型
  ```

* 类型别名和interface的方式必须要写泛型



## ==在类型中使用具体的值==

* 如果在类型中需要使用具体的值，需要加 typeof，和js typeof不一样

```typescript
// 拿到函数返回类型

type MyReturnType<T> = T extends (...arg: any) => infer P ? P : any

function fn() {
    return ''
}

let testFn: MyReturnType<typeof fn>
```



## any 和 unknown 类型

参考：https://www.zhihu.com/question/355283769/answer/2136229141

* any会抵消所有类型检查。更像一个超级子类型，继承了所有的类型，啥属性都有
* unknown是所有类型的父类型，啥属性都没有，原始的

使用as改变类型，只能在继承链上移动，比如 A extends B、C extends B

```typescript
// 指鹿为马
declare let test: A
let test1 = test as B as C // 能在继承链上上下移动

// 建议操作，如果A和C没有任何关系
let test1 = test as unknow as C

// 放弃这种操作
let test1 = test as any as C
```





## ts-node

* 没有整体说一下 ts-node 的使用

### 怎么样加node参数呢？

* ts-node 执行文件是不能直接添加node参数的

  比如：

  ```bash
  npx ts-node --throw-deprecation process-warning.ts
  ```

* 解决方法：

  ```bash
  node --loader ts-node/esm process-warning.ts
  ```

  或者：(推荐，上面那种方式官方不再推荐)

  ```bash
  node -r ts-node/register 文件名
  ```

​	或者使用 NODE_OPTIONS 的方式(写到node命令的前面，是环境变量)

```bash
NODE_OPTIONS="-r ts-node/register --no-warnings" node ./index.ts
```



参考：https://nodejs.org/api/cli.html#cli_node_options_options

https://github.com/TypeStrong/ts-node#node-flags-and-other-tools



## ts 配置

https://www.dengwb.com/typescript/configuration/file-options.html

使用过的总结：

```ts
{
    "compilerOptions": {
      "strictPropertyInitialization": false,             // 对象的属性可以不被初始化，在构建class定义而不是接口定义的时候有用
      "esModuleInterop": true, // 开启后，如果模块没有实现 export.default，也可以使用 import path 的语法，否则需要使用 import * as 的方式
      "allowSyntheticDefaultImports": true, // 这个只是影响编译，不影响代码生成。如果模块没有导出default，也不会报错！
    }
}
```

### 模块化

参考：https://segmentfault.com/a/1190000018249137

[esModuleInterop做了什么](https://zhuanlan.zhihu.com/p/148081795)

## 模块化相关

在配置文件中如果配置了 `"module": "ESNext", `，那么ts-node 会默认按照 esm 的方式对文件进行解析，相应的 package.json 中的 module 也需要配置为 `"type": "module"`，要不然会运行报错！

* tsconfig 中的 `"module": "ESNext", ` 和 package.json 中的 `type` 字段是相辅相成的，需要进行匹配！！！

## ts 编译选项

https://www.tslang.cn/docs/handbook/compiler-options.html

https://www.dengwb.com/typescript/configuration/compiler-options.html

## ts 异常提示问题

https://www.dengwb.com/typescript/configuration/vscode-compiler.html

## ts 不同文件命名冲突

> 在默认情况下，当你开始在一个新的 TypeScript 文件中写下代码时，它处于全局命名空间中，使用全局变量空间是危险的，因为它会与文件内的代码命名冲突。改成使用文件模块，文件中包含import或者export，就会在当前文件中创建一个本地作用域

## todo

* keyof
* Omit
* Exclude

# 单词

* Interop 互操作
  * 在配置 tsconfig 中 esmodule 和 commonjs 互相转换的时候使用。