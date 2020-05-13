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

- 不带任何输入文件的情况下调用`tsc`，编译器会从当前目录开始去查找`tsconfig.json`文件，逐级向上搜索父目录。
- 不带任何输入文件的情况下调用`tsc`，且使用命令行参数`--project`（或`-p`）指定一个包含`tsconfig.json`文件的目录。

当命令行上指定了输入文件时，`tsconfig.json`文件会被忽略。（直接使用 tsc index.ts 的形式）

* 如果要在报错的时候终止 js 文件的生成，可以在 `tsconfig.json` 中配置 `noEmitOnError` 即可。

# 3. 基础

## 0. JavaScript中的数据类型

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Data_structures

## 1. 原始数据类型

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

> 当typescript 不确定一个联合类型的变量到底是哪个类型的时候，我们只能访问次联合类型的所有类型里共有的属性或方法。

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

### 重载

重载允许一个函数接受不同数量或类型的参数时，作出不同的处理。

就是接受number的参数的时候，return 一个number类型的数据。接受 string的参数，返回string类型的数据。

可以从上到下重复定义函数，函数的调用的时候从上到下匹配类型，从而表述清楚函数的返回类型。

上例中，我们重复定义了多次函数 `reverse`，前几次都是函数定义，最后一次是函数实现。在编辑器的代码提示中，可以正确的看到前两个提示。

注意，TypeScript 会优先从最前面的函数定义开始匹配，所以多个函数定义==如果有包含关系，需要优先把精确的定义写在前面。==

## 类型断言

类型断言（type assertion）可以用来手动指定一个值的类型。

#### 语法

```ts
<类型>值
```

或

```ts
值 as 类型
```

在 tsx 语法（React 的 jsx 语法的 ts 版）中必须用后一种。

类型断言就是在联合类型的情况下，对其中的一个类型进行if的赛选。

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

==注意：在使用断言的时候，后续的具体使用都要<类型>值的形式来使用。==

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

* 元祖是特俗的数组，在定义的时候长度不能变，要和左侧的类型对齐。
* 元祖可以使用数组的方法来添加元素，比如说使用 push 方法。

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

### 常亮枚举

* 不能存在计算值，比如 `arr.length`

## 类

TypeScript 除了实现了所有 ES6 中的类的功能以外，还添加了一些新的用法。

typescript 类的使用规则：https://ts.xcatliu.com/advanced/class.html

### 修饰符

* 类上的方法和属性提供修饰符

* publicre

* private 子类也不能访问

* Protected 外界不能访问，子类可以s

* readonly 只读的，不能修改

* 静态的 

  static + 上面的修饰符

### 面向对象

封装、继承、多态

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

## 泛型（牛的）（generics）

泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

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
```



# 注意

* 所有的类型都是在定义的时候规定的，在赋值的右边是不需要去定义类型的