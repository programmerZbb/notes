# 1. 组件之间通信

## 1.1 父子组件之间的传值

* 如果把一个子组件在父组件上的使用当做普通的标签的使用，一共有两个位置能够使用。一个是组件元素的属性，另一个是组件元素的内容。
* 组件的属性能够作为组件之间传值来使用，组件的内容能够作为组件的插槽来使用。

### 父组件向子组件传值

* 在使用子组件时能够定义一个组件属性，来实现父组件向子组件传值。

  父组件中子组件的使用：

  ```html
  <children :msg="info"></children>
  ```

  子组件接受数据：

  ```js
  props: ["msg"]
  ```

  注意：子组件需要使用  props 选项来对这个值注册的。如果使用了 prop 的验证功能，也可是接收到这个值。如果不使用该值将作为普通的html的属性绑定在子组件最外层的元素上。

  ```js
  props: {
      msg: {
          type: String
      }
  }
  ```

  props 这个选项是只读的不能在子组件中再次修改。使用 props 传值的形式是单向数据流，只能够实现父组件向子组件传值。

### 子组件向父组件传值

* 在使用子组件的时候，绑定一个方法，能够实现子组件向父组件传值。

  父组件：

  ```html
  <children @fn="test"></children>
  ```

  ```js
  methods: {
      test(arg) {
          this.msg = arg
      }
  }
  ```

  子组件：

  ```html
  <button @click="fn1">点击</button>
  ```

  ```js
  methods: {
      fn1() {
          this.$emit("fn", "子组件数据")
      }
  }
  ```

### v-model 的使用

* v-model 在组件中的使用能够实现父子组件数据的双向绑定，既能够实现父组件到子组件的传值，也能够实现子组件到父组件的传值。

  ```html
  <children v-model="msg"></children>
  ```

  相当于：

  ```html
  <children v-bind:value="msg" v-on:input="msg=$event"></children>
  ```

  如上，v-model 的使用结合了上述的两种数据传递的方式，既可以在子组件中使用 props 来接收 value，实现父组件向子组件传递数据，也可以在子组件中出发 input 事件实现子组件向父组件传递数据。

## 1.2 通用通信方案

### 1.2.1 eventBus(观察者模式)

新建一个 bus.js 

```js
import Vue from "vue"

export default new Vue()
```

在接收值的组件中定义事件：

```js
created() {
    vm.$on("fun1", (arg) => {
        this.msg = arg
    })
},
data() {
    return {
		msg: ""
    }
}
```

在传值组件中触发事件：

```js
created() {
    vm.$emit("fun1", "传值组件的数据")
},
```

### 1.2.2 vuex

* 使用 vuex 中的state 实现组件之间数据的共享。

# 2. 双向数据绑定原理及注意

## 2.1 双向数据绑定原理

* vue数据双向绑定是通过数据劫持结合发布者-订阅者模式的方式来实现的。
* vue 是通过 `Object.defineProperty()`来实现数据劫持的。（有点像vue的钩子）

简单的实现双向数据绑定：

```html
<input type="text" id="a">
<h1 id="b"></h1>
```

```js
var obj = {}
Object.defineProperty(obj, "txt", {
    get: function() {
        return ""
    },
    set: function(val) {
        document.getElementById("a").value = val
        document.getElementById("b").innerHTML = val
    }
})
document.getElementById("a").addEventListener("input", function(e) {
    obj.txt = e.target.value
})
```

效果：

![双向数据绑定](C:\workspace\work\vue share\双向数据绑定.gif)

## 2.2 注意事项

1. 使用`Object.defineProperty`实现双向数据绑定的时候，如果设置的属性为数组，除非改变数组的引用，否则使用所有数组赋值的方式都无法触发 set 方法。

2. vue 实现了变异方法触发双向数据绑定机制，包括如下的方法

   - `push()`
   - `pop()`
   - `shift()`
   - `unshift()`
   - `splice()`
   - `sort()`
   - `reverse()`

   由于JavaScript的限制，vue 不能够检测如下的数组变动：

   1. 索引赋值： `vm.arr[1]="新的值"`
   2. 修改数组长度：`vm.arr.length=10`

# 3. 插槽的使用

* 在不使用插槽的情况下，vue 子组件在父组件中使用的时候，任何传入它的内容都会包抛弃。

  插槽的使用：

  子组件的使用：

  ```html
  <children>test content</children>
  ```
  子组件：

  ```html
  <h1>子组件</h1>
  <slot></slot>
  <a>点击</a>
  ```

* 插槽的使用场景：

  插槽实现了一套内容分发的API。

  插槽相当于组件提供的一个接口，使用者不需要去了解该组件内部结构，和函数的参数相似。能够作为封装好的组件向外提供的一个接口（element-ui 常用），使用插槽能够使组件的使用结构清晰，轻松实现组件之间值的互用。另外，在父组件中也可以轻松定义子组件中的结构。

  父组件：

  ```html
  <children>
  	<h1>
          父组件规定的结构--父组件的数据{{ msg }}
      </h1>
  </children>
  ```

  ```js
  data() {
      return {
          msg: "父组件的数据"
      }
  }
  ```

  子组件：

  ```html
  <div>
      <header>--头部--</header>
      <slot></slot>
      <footer>--页脚--</footer>
  </div>
  ```

* 此外，在使用多个插槽的时候还能在元素上使用 slot 特性，在子组件slot标签使用name特性，从而实现插槽内容和子组件中的占位slot一一对应。

  父组件：

  ```html
  <children>
  	<template v-slot:header>
      	<h1>
              第一个内容
          </h1>
      </template>
      <template v-slot:footer>
      	<h1>
              第二个内容
          </h1>
      </template>
  </children>
  ```

  子组件：

  ```html
  <div>
      <header>--头部--</header>
      <slot name="header"></slot>
      <div>
          <h1>
              ...............
          </h1>
      </div>
      <slot name="footer"></slot>
      <footer>--页脚--</footer>
  </div>
  ```

## slot中的内容

* slot 占位标签也是可以放内容的，该内容将最为插槽的==默认内容==。

  父组件：

  ```html
  <children>submit</children>
  ```

  子组件：

  ```html
  <button>
  	<slot>按钮</slot>
  </button>
  ```

## 插槽的样式

* 插槽的样式需要由父组件来提供，在样式使用了 scope 规定的样式作用域的情况下，插槽的样式只能在父组件中写，插槽默认值的样式由子组件来提供。

> ==注意：==
>
> 插槽默认值只能使用 template 标签的形式进行展示，一旦使用了普通的元素（甚至是字符串），slot 的内容（默认内容）将会被清空。

正确的使用：

```html
<template name="test"></template>
```

错误的使用：

```html
<h1 name="test"></h1>
```

## 应用案例--轻松实现dialog的关闭

```html
<Dialog v-if="visible">
  <button slot="button" @click="hidden" class="btn">关闭</button>
</Dialog>
```

* 插槽绑定的事件也是由父组件提供的。

## 插槽使用子组件的数据

* 插槽的使用过程中 slot 只是作为一个占位符来使用，结构和数据完全是父组件提供的。有时候，需要提供一个带有子组件数据的插槽。实现子组件的使用，需要使用到作用域插槽。

- 在子组件中需要使用 slot 标签来占位，并使用 v-bind 绑定一个子组件的数据。

  ```html
  <slot :items="data"></slot>
  ```

- 在父组件中，需要使用 template 标签并设置 slot-scope (值为一个对象)来接受数据。可以自定义用什么标签实现遍历。

  ```html
  <template slot-scope="slotProps">
  	<span>{{ slotProps.items.text }}</span>
  </template>
  ```

   `slotProps`是一个对象，slot 标签中绑定的数据都在这个对象中挂载着，也可以使用解构赋值的形式来接收传递的数据。

  接收到子组件的数据，在使用该插槽的时候，就能对子组件的数据进行个性化的处理。

## 3.1 插槽使用注意（vue 2.6.0 起使用）

==注意：== 具名插槽的使用

	1. `v-slot:` 的形式能够用来命名插槽的name
 	2. `v-slot:name="slotProps"` slotProps 为子组件绑定的数据。

# 4. 两个修饰符

## 在组将上绑定原生的事件

* 使用 `v-on:`的形式能够实现给普通元素绑定事件，给组件绑定原生的事件需要使用到`.native`修饰符。

  ```html
  <children @click="fn"></children>
  ```

## `.sync `修饰符

* 为了让子组件修改父组件的数据更有动机性，增加可维护性，vue 提供了 `.sync`修饰符。

写法:

```html
<children v-bind:title.sync="msg"></children>
```

展开：

```html
<text-document
  v-bind:title="doc.title"
  v-on:update:title="doc.title = $event"
></text-document>
```

`v-model`和`.sync`的区别：

* `v-model` 的使用修改父组件的值没有标记，看不出明显的动机。`.sync` 的使用更有动机性。
* `.sync`不需要prop来接收数据。

# 5. 优化相关

## mixins (混入)

- mixins 作为 vue 的一个选项，能够将一些公共的方法或者是data 之类的组件选项，封装起来实现复用。需要指定一个 vue 对象。mixins 对应的是一个数组。

- mixins 就相当于 vue 组件的函数封装，比如只会用到的一些公用的方法（如果公用一些template模板之类的，应该封装成公共的组件）

- 比如两个组件共同使用一些方法，就可以单独定义到一个 对象中。

  ```js
  const base = {
      methods: {},
      data: {}
  }
  
  new Vue {
      el: "#app",
      mixins: [base]
  }
  ```

  ***如果传入的对象包含钩子函数，Mixin 钩子按照传入顺序依次调用，并在调用组件自身的钩子之前被调用。***他们将在 `Vue.extend()` 里最终选择使用相同的选项合并逻辑合并。（就是如果说存在相同的方法）

## 路由懒加载的实现

* 当打包构建应用时，Javascript 包会变得非常大，影响页面加载。我们需要把不同的路由对应的组件分割成不同的代码块（chunk），然后当路由被访问的时候才加载对应的组件。
* vue 路由懒加载的实现需要结合 Vue 异步组件 和 webpack 的代码分割功能，使用es6 推荐的 import 语法。

组件的注册使用：

```js
components: {
    "children": () => import(/* webpackChunkName:"children" */ "./children.vue")
}
```

因为在es6中import只能够在顶级作用域使用，为了解决这个问题，需要使用 babel 插件[Syntax Dynamic Import Babel Plugin](https://babeljs.io/docs/plugins/syntax-dynamic-import/#installation)。

打包之后的如下：

![异步组件打包](C:\workspace\note\pic\异步组件打包.png)

js 文件被分成了两个，一个 bundle 中有两个 chunk，实现“按需加载”的功能。

* 实现路由懒加载只需要把异步加载的组件像往常一样使用。

