# taro 简介

* taro 是一套遵循 react 预发规范的 多端开发 解决方案

* 使用 taro，只书写一套代码，再通过taro 的编工具，将源代码分别编译出可以在不同端（微信、h5、RN等）运行的代码。

* 类 React 代码————Nerv

* 多端统一开发框架，支持用 React 的开发方式编写一次代码，生成能运行在微信/百度/字节跳动/支付宝小程序、H5、React Native 等平台的应用。

![taro核心转化图](.\picture\taro核心转化图.png)

taro 最主要的是中间的编译过程。 

# 起步

## 安装

* 安装 Taro开发工具 @tarojs/cli
* npm i -g @tarojs/cli

## 创建项目

使用命令创建模板项目

taro init myApp

开发期启动命令：

* npm run dev:h5   WEB
* npm run dev:weapp  微信小程序
* npm run dev:alipay   支付宝小程序
* npm run dev:swan  百度小程序
* npm run dev:rn  ReactNative

编译器的启动命令：

​	把上面的 dev 换成 build

注：

创建文件（编译的时候也会看到）时候的 index.html 文件是给 h5 端使用的，不是小程序使用的。

![taro项目结构](.\picture\taro项目结构.png)](.\picture\taro项目结构.png)

### 项目分解

* project.config.json

  该文件是小程序的配置文件

  appid 是小程序的id，默认是游客 id 不能真机预览，正式开发需要更换成你的 id ,才能在真机上进行预览。

项目采用类 react 语法，一切皆组件，没有页面和组件之分。

通过 src 下面的 app.js 文件的路由来控制页面，凡是配到路由里面的组件，我们就认为是页面。taro在编译的过程中，就会将路由中的组件配置成页面，不是路由中的页面配置成为组件。

```ts
pages: [
      'pages/index/index'
    ],
```

pages 下面的就是路由，没有在里面配置的就是组件。

## 使用

在 taro （react） 中只能够在 `""` 或者是`{}` 中写js代码。

## taro 框架学习

![taro生命周期](.\picture\taro生命周期.png)](.\picture\taro生命周期.png)

> 简单的概括一下 React 的语法，就是 class 是一层的代码块，里面相当于一层作用域，this 全部绑定到当前的构造函数上面，class里面的变量（或函数）不需要写this来定义，直接向最外层的变量一样直接绑定到window上面，写法和在顶层中的写法一样。在class中的方法调用class中的变量和方法，需要使用 this 来调用。

### state

taro 框架的数据结合存放在 state 中，就是 model（数据集合）

state 只需要在导出的组件中 `class Index extends Component ` 定义state来实现，直接在组件的使用过程中（render函数中的标签中），使用 `{this.state.name}` 来调用这个变量。生命周期只对state这个变量进行管理，其他的变量进不了管理。

### 生命周期

```ts
componentWillMount () {
    console.log("第一次渲染之前执行 只执行一次") //类似于 vue 里面的 beforeMount
}
```
然后执行 render 函数

```ts
componentDidMount () {
    console.log("第一次渲染之后执行 只执行一次") // 类似于 vue 中的 mounted
}
```

```ts
componentWillUnmount () {
    console.log("卸载时执行！ 只执行一次") // 类似于 vue 的beforeDestroy
}
```
```ts
componentWillUpdate() {
	console.log("数据将要更新的钩子")
}
```
```ts
componentWillUpdate() {
	console.log("数据更新之后的钩子")
}
```
```ts
shouldComponentUpdate(nextProps, nextState) {
	console.log("检查此次setState是否要进行render调用")
	return true
}
```
返回 true 和false 来判定是否需要调用 render 方法。true 调用 render 方法，false 不调用 render 方法。此方法可以用来对render方法的调用进行拦截，比如 nextState.txt = "对的值"  才能调用render 方法。一般用在多次调用 setState 时，提升 render 的性能。
```ts
componentWillReceiveProps(nextProps) {
	// 会在父组件传递给子组件的参数发生改变时触发
}
```

上面的使用的是 react 的生命周期，下面的是 taro 自定义的生命周期钩子,在react中不存在，是为了支持小程序需要的，在h5中也支持

```ts
componentDisShow() {
    console.log("页面显示时触发")
}
```

```ts
componentDisHide() {
    console.log("页面隐藏时触发")
}
```

### 设置 state

使用 this.setState({key: value}) 的形式来设置 state，一般在钩子函数中调用。

==taro 中状态更新一定是异步的，react 中的状态更新则不一定是异步的。==

如果想要实时获取更新的值，需要使用到 setState 的回调来实现。

例子：

```ts
this.setState({name: "test"}, () => {
    console.log(this.state.name+"最新的state")
})
```

### 新建组件

新建的组件必须要引入 taro

```js
import Taro, {Component} from '@tarojs/taro'
import {View, Text} from '@tarojs/components'
```

新的组件：

```ts
import Taro, {Component} from '@tarojs/taro'
import {View, Text} from '@tarojs/components'
class Child extends Component{
    render() {
        return ()
    }
}
export default Child
```

父组件中使用新的组件：

```tsx
import Child from "./Child"
```

import 进入的名称必须和子组件中 class 的名称保持一致，要不然在小程序的开发过程中会出错。

#### 注意

灵活使用shouldComponentUpdate钩子能够优化页面性能，在达到要求的时候再更新页面。组织页面频繁的调用render更新页面。

#### 传递默认的值

props 传递默认的值

> 如果父组件向子组件中传递的值中有一个变量的不确定值，可以先定义为 undefined （react只识别undefined，然后在子组件最外层（class的外面）中，使用Child.defaultProps 来定义子组件接受的变量的默认值）

```js
Child.defaultProps={
    obj:{key:[{name: "aaa"}]}
}
```

## ***使用super关键字调用父类的方法***

在子类中能够使用 super 关键字来调用父类的方法。

```js
super.test()
```

## 在组件上绑定事件

在react元素上绑定事件，如果需要调用组件内的方法，需要绑定 this，因为 this 直接调用是组件渲染完成的时候的 this，指向的是元素的 dom 对象，如果想调用组件内的方法，需要在定义的时候就绑定this

```jsx
retunr (<View onClick={this.test}></View>)
```

此时 test 方法的  this 指向的是绑定事件的dom对象，如果需要调用 class 里面的方法，需要在定义的时候就绑定 this。`onClick={this.test.bind(this)}` 

> 因为绑定事件是异步的，在执行的时候 this 已经换成当时的dom对象了

因为在小程序中绑定事件需要使用 on 给子组件传递（类似于 vue，h5则不需要加 on），因此在 taro 中向子组件传递函数，需要加 on  (不需要驼峰，和调用时候的使用方式一样即可)

```jsx
retunr (<View ontest={this.test}></View>)
```
> 相当于vue中的绑定事件加了一个 on 

## taro 的路由功能

* 在taro 中，路由功能是默认自带的，不需要开发者进行额外的路由配置

* taro 默认根据配置路径生成 Route

* 我们只需要在入口文件的config配置中指定好 pages，然后就可以在代码中通过 Taro 提供的API来跳转到目的页面

  ```ts
  config: Config = {
      pages: [
        'pages/index/index'
      ],
      window: {
        backgroundTextStyle: 'light',
        navigationBarBackgroundColor: '#fff',
        navigationBarTitleText: 'WeChat',
        navigationBarTextStyle: 'black'
      }
    }
  ```

只需要将首页的路径放在第一个位置即可访问首页。

### 文档

API -> 界面 -> 导航

* Taro.navigateTo({url: '/pages/question/index'}) 方法在微信小程序中的使用，==微信小程序最多支持打开五层webview，在开发的时候要注意==

* Taro.redirectTo() 方法能够将当前的webview 的路由更换成新的路由，不会打开新的 webview 窗口，能够规避小程序的五层限制

### 基础组件

在 taro 中提供了基础的组件，在使用的过程中需要单独的引入。（具体查看文档，为了区别一般使用首字母大写）

比如 在页面中引入 button 按钮组件

```js
import { View, Text, Button } from '@taro.js/components'
```

### 路由传参

1. 使用queryString的形式传参，直接在使用路由的时候再url 后面拼接请求字符串

   在生命周期`componentWillMount`中，使用 this.$router.params 来获取到传递的参数

   ```js
   componentWillMount () {
       let {name}=this.$router.params
   }
   ```

   目前只支持这一种传参方式 

## 静态资源引用

* 在taro中可以像使用 webpack 那样自由地引用静态资源，而且不需要安装任何loader
* 应用样式文件
* 可以直接通过 ES6 的import语法来引用样式文件

### js 文件的引用

* 使用 es6 的 import 方式来引入 js 文件中的方法 

## 引入图片、音频、字体等文件

* 可以直接通过 ES6 的 import 语法来引用此类文件，拿到文件引用后直接在 JSX 中进行使用。

  实例：

  ```jsx
  import  namedpng from "./named.png"
  ```

  使用：

  ```jsx
  <View>
  	<Image src={namedPng}>
      <Image src={require("./named.png")}>
  </View>
  ```

  也可以使用 require 来引用，值得注意的是只有本地文件才能这样做，如果是线上图片直接赋值即可。（就是说本地的图片不能直接使用 src 赋值的形式来引入。）

  注意：上面的 Image 标签是从 taro 组件中引入的。

* 引入样式

  注意：在样式中写小写的 px 直接会转换成 rem ，写大写的PX 会按px 来进行计算

  `import test from "./test.less"`

  然后 test.less 中的样式就能够直接在当前页面中使用。