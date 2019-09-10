# 1. 说明

* 本笔记是慕课网《React16.4开发简书项目》笔记

# 2. 简介

* Facebook 推出
* 2013年开源
* 函数式编程
* 使用人数最多的前端框架
* 健全的文档与完善的社区（中文文档滞后）
* React Fiber （react16之后）

# 3. 快速启动

* 采用官方推荐的脚手架工具快速搭建react 项目

  安装：npm i -g create-react-app

  create-react-app  项目名称

  cd 项目

  yarn start

# 4. 项目结构分析

![react16-项目分析](.\pic\react16-项目分析.png)

1. yarn.lock 项目依赖锁定

2. package.json 

   能通过 yarn 加 scripts 里面的字段来执行响应的命令。（类似于 npm run ）

3. public 文件

   favicon.ico 文件就是网站上的页面图标

   index.html 文件就是编译之后的首页文件

4. 入口：

   src 文件夹下面的 index.js 文件

   react 设计理念： all in js 在js 文件只中，类似于 vue 里面引入 css 文件，只需要把css 文件 import 即可，不需要命名。

   app.test.js 文件是自动化测试文件。

   manifest.json 文件是定义快速访问的一些特性（比如说桌面图标，颜色等等）

## 小的知识点

在 index.js 文件中引入了 registerServiceWorker js文件，涉及到 PWA（progressive web application） 的概念，引入registerServiceWorker 就是帮助利用网页写手机app 的功能。服务器挂掉之后，还能看到之前访问的网页。

# 5. 开始 react

## 5.1 react 简单入门

1. 组件

   引入 

   `import React, { Component } from "react"`

   定义：(render 函数的return加`()` 是为了方便多行编写代码)

   ```jsx
   class App extends Component { // 继承 React.Component 即可定义一个组件
       render() {
           return (
           	<div>
               	hello react
               </div>
           )
       }
   }
   ```

   ==render 最外层必须有一个顶级元素==

   导出：
   
   `export default App`
   
2. 首页一般需要一个 app 组件，在 index.js 文件中 使用`render` 方法将 app 组件挂载到首页上。

   每个组件自定义的 render 方法返回 react 组件 的结构。

3. react 中的组件都是单闭合使用方式，和 vue 的使用方式不太一样。

4. 首页的书写：

   1. 使用js 中写结构的方式(jsx 语法)，一定要引入 react

      ```jsx
      import React from "react"
      ```

      因为不引入 react 没法识别 `JSX` 语法

   2. 首页渲染需要引入 ReactDOM 

      ```jsx
      import ReactDOM from "react-dom"
      ```

      然后渲染首页

      ```jsx
      ReactDOM.render(<App/>, document.getElementById("root"));
      ```

   

### 5.1.2 注意

* 因为 react 要求组件的最外层必须有一个顶级元素来包围 里面的结构，但是有的时候并不需要这个最外层的元素， react 16 中提供了一个 `Fragment` 的占位符取代最外层的元素。

  ```js
  import { Fragment } from "react"
  ```

  fragment : 碎片，片段



## 5.2 React 中的响应式设计思想和事件绑定（重点）

### 5.2.1 基础

1. 任何类都有一个 constructor 方法，这个方法会由于任何方法优先执行。

   在react中定义变量

   ```jsx
   constructor(props) {
       super(props) // super 是调用父类的 constructor 方法
       this.state = { // react 中的数据都存放在 state 状态中
           
       }
   }
   ```

2. 在react的html 元素中使用 定义的数据

```html
<input value={this.state.inputVal} />
```

​	在 jsx  中使用 变量语法如上 需要加 `{}`

3. 获取变量

   this.state. 获取自定义的数据

   this.方法名 获取自定义的方法

   ==react 中有一个 immutable(不可改变的) 的概念==

   * state  不予许我们做任何的改变，就是不能直接修改 state 的值，需要使用 this.setState() 方法，先修改 state 的副本，然后再赋值给 state。一旦直接修改 state 中的内容，在后期做react性能优化的时候就会出现问题

     ```jsx
     const list = [...this.state.list]
     ```

     

4. 页面改变

   react 中需要改变页面，就需要改变 数据。
   
5. 在jsx中写注释

   ```jsx
   <my>
   	{/* 我是第一个注释 */}
       {
           // 我是二个注释
       }
   </my>
   ```

   

### 5.2.2 react 事件绑定

* ==react 事件绑定方法的this 不指向当前 class 的实例，需要在绑定事件的时候绑定 this==

* 不推荐在绑定事件的时候去绑定 this ，推荐直接在constructor中绑定，能减少一部分性能的损耗。

  ```js
  constructor (props) {
      this.handle = this.handle.bind(this)
  }
  ```

### 5.2.3 react 绑定事件传参（非常重要）

* react 在事件绑定的时候，不能够直接加`()`（和 vue 的区别），因为加上`()`之后，该事件绑定的方法就会立马执行。

* 解决方案：

  在绑定事件需要传参的时候，不能直接使用 `(参数)` 的形式来实现

  1. 通过 bind 来实现事件传参

     ```js
     <div onClick={this.handleClick.bind(this, arg1)}>
     ```

     但是该方案会造成性能损耗的情况，不推荐使用。

  2. 箭头函数的方式

     ```js
     <div onClick={() => {this.handleClick(arg1)}}>
     ```

     

## 5.3 引入样式文件

*  直接引入样式文件，class 用 className 替换

  ```js
  import "./my.css"
  ```

* 在 jsx 中如果想把字符串中的html元素展示出来（不转义），需要在元素中加 attribute `dangerouslySetInnerHTML`，不需要再去设置inner的值

  ```jsx
  <li dangerouslySetInnerHTML={{__html: item}}>
  	
  </li>
  ```
  
  ==实际上在jsx语法中返回一个结构时，需要在最外层有一个元素，不能同时存在两个最外层元素。==

## 5.4 拆分组件与组件之间的传值

### 5.4.1 组件的使用

* 组件在react 中的使用，直接单闭合标签，首字母大写。
* 在组件之间传值，是通过属性的方式来实现的（方法和变量），在子组件中能通过 `this.props`来调用传递的变量和方法（注意方法this的指向问题） 

### 5.4.2 组件中变量使用的问题

* 在开发的过程中，推荐使用解构赋值的形式来定义变量。

  比如在 render 函数中，

  ```jsx
  let { content } = this.props
  ```

 1. 注意
    ==标签太长的话，建议换行使用==
    
    jsx 返回太长的结构的话，建议封装成方法。

## 5.2 react 16 新特性

* 在新的 react 中推荐使用采用如下的方式修改 state 里面的变量。

  ```jsx
  this.setState((prevState) => ({ //prevState 是改变之前的 this.state
      value: prevState.value
  }))
  ```

  ==但是该方式是异步的，在获取 e.target 的时候e需要在执行 setState 之前做值得保存。==

  prevState 可以避免不小心改变 state的状态？（是不是浅拷贝，验证，不是浅拷贝，就是同一个对象）

## 5.6 react 父子组件之间的传值

1. 父组件传值给子组件

   直接在使用子组件的时候，传递属性

2. 子组件传值给父组件

   在子组件中触发父组件的方法，实现传参的传值。

## 5.7 PropsTypes 与 DefaultProps

1. propTypes 对子组件接受的父组件的数据做强校验

   ```js
   // 在子组件中
   import PropTypes from "prop-types"
   // 在导出组件之前
   Item.propTypes = {
       content: PropTypes.string, //content 接受的值要为string类型
       deleteItem: PropTypes.func, // 必须为 function
       index: PropTypes.number.isRequired // 必须为number,必传
   }
   // 具体参考 react 官方文档
   ```

2. defaultProps

   能给没传的属性一个默认值

   ```js
   Item.defaultProps = {
       test: "default" // 这种情况就不会触发上面的检测
   }
   ```

   

# 6. react 思考

1. 声明式开发

   react 就是声明式开发，不必关心都DOM操作

2. 可以与其他框架并存

   react 只操作页面 id 为 root 的节点，需要操作其他节点可以使用 jquery 来实现。

3. 组件化开发

4. 单向数据流

   父组件可以向子组件传递一个值，但是不能在子组件中直接改变这个值，`props` 是read-only 的。

   但是可以通过调用父组件方法的方式，实现对父组件数据的更改（实质是父组件操作父组件的数据。）

5. 视图层的框架

   数据之间的传递，需要其他数据层的框架来实现。

6. 函数式编程

   自动化测试，直接检验一个函数的输入输出即可，如果全是函数的话方便自动化测试。

7. 命令式开发

   类似于 jquery 就是命令式开发

* 父组件的 render 函数执行之后，子组件的render函数一定会执行。