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


### ==总结==

* react 中异步执行（包括订阅的方式）的方法都需要绑定this，同步执行的不需要绑定 this

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

### 5.3.1 直接在元素上写 样式

* 直接在元素上写样式，因为 style  是双引号的

  ```html
  <input style={{widht: ’100px‘}} />
  ```

  第一个引号是表示js 区域，第二个引号是对象。

  该对象里面的属性都将采用 驼峰民命

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

* 在新的 react 中推荐使用采用如下的方式修改 state 里面的变量。(提升性能)

  ```jsx
  this.setState((prevState) => ({ //prevState 是改变之前的 this.state
      value: prevState.value
  }))
  ```

  ==但是该方式是异步的，在获取 e.target 的时候e需要在执行 setState 之前做值得保存。==

  prevState 可以避免不小心改变 state的状态？（是不是浅拷贝，验证，不是就是 state）
  
  setState 提供了异步执行之后的回调。
  
  ```jsx
  this.setState((prevState) => ({ //prevState 是改变之前的 this.state
      value: prevState.value
  }), () => {
      console.log(111)
  })
  ```
  
  

# 6. react 高级

## 6.1 react 中 props、state、render函数

* state 和 props 中的数据发生变化的时候，render函数就会去执行。 	 

  父组件render执行子组件也会执行？？？
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
       index: PropTypes.number.isRequired, // 必须为number,必传
       str: PropTypes.oneOfType([PropTypes.number, PropTypes.string]),
       mixType: PropTypes.arrayOf(PropTypes.number, PropTypes.string) // 一个数组，元素的类型为number、string
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

# 7. 虚拟 DOM

## 7.1 react 中的虚拟 DOM

思路：

* state 数据
* JSX 模板
* 数据 +  模板 结合，生成 真实的 DOM，来显示
* state 发生改变
* 数据 + 模板 结合，生成真实的 DOM ,替换原始的 DOM

缺陷： 

1. 第一次生成了一个完整的 DOM 片段
2. 第二次生成了一个完整的 DOM 片段
3. 第二次的 DOM 替换第一次的DOM，非常消耗性能



解决：

- state 数据
- JSX 模板
- 数据 +  模板 结合，生成 真实的 DOM，来显示
- state 发生改变
- 数据 + 模板 结合，生成真实的 DOM ,并不直接替换原始的 DOM
- 新的 DOM（DoucumentFragment） 和原始的DOM做对比，找差异
- 找出新的 input 框发生了变化
- 只用新的 DOM 中的 input 元素，替换吊老 DOM 中的input 元素。


缺陷：

性能的提升并不明显



第三种方案： 虚拟的 DOM

- state 数据

- JSX 模板

- 数据 +  模板 结合，生成 真实的 DOM，来显示

- 数据+模板 生成虚拟的 DOM （==虚拟DOM就是一个JS对象，用它来描述真实DOM==）（增加了小部分性能的损耗）

  虚拟 DOM 举例：

  ```html
  <div id="a">
      <span>hello world</span>
  </div>
  ```

  ```js
  [
      "div",
      {id: "a"},
      [
          "span",
          {},
          "hello world"
      ]
  ]
  ```

- state 发生变化

- 生成新的虚拟 DOM （极大的提升了性能，不用操作DOM）（==比较js对象不怎么消耗性能，比较DOM 会极大的消耗性能==）

  ```js
  [
      "div",
      {id: "a"},
      [
          "span",
          {},
          "新的内容"
      ]
  ]
  ```

- 比较新的虚拟DOM和原始的区别，找到是内容发生了变化

- 直接操作 DOM， 改变 span 中的内容。

## 7.2 深入了解虚拟 DOM

react 真实实现：（3和4 反过来）

1. state 数据

2. JSX 模板（render里面的就是模板）

3. 数据+模板 生成虚拟的 DOM （==虚拟DOM就是一个JS对象，用它来描述真实DOM==）（增加了小部分性能的损耗）

    虚拟 DOM 举例：

    ```js
    [
        "div",
        {id: "a"},
        [
            "span",
            {},
            "hello world"
        ]
    ]
    ```

4. 用虚拟的 DOM 的结构生成真实的 DOM，来显示
	```html
    <div id="a">
        <span>hello world</span>
    </div>
  ```
  ```

  ```

  ```

  ```

  ```

  ```

  ```

  ```

  ```

  ```

	```

	```
	
5. state 发生变化

6. 生成新的虚拟 DOM （极大的提升了性能，不用操作DOM）（==比较js对象不怎么消耗性能，比较DOM 会极大的消耗性能==）

    ```js
    [
        "div",
        {id: "a"},
        [
            "span",
            {},
            "新的内容"
        ]
    ]
    ```
  ```


7.  比较新的虚拟DOM和原始的区别，找到是内容发生了变化
8.  直接操作 DOM， 改变 span 中的内容。



JSX -> JS 对象 -> 真实的 DOM：

JSX:

​```jsx
render() {
    return <div>我是内容</div>
}
  ```

JS 对象 -> 真实的 DOM：

```jsx
// render 中也可以这样写
render() {
    return React.createElement("div", {}, "我是内容")
}
// 例2
render() {
    return React.createElement("div", {}, React.createElement("span", {}, "span里面的内容"))
}
```

虚拟DOM的有点：

1. 性能提升了
2. 它是的跨端应用得以实现， React Native

# 8. react diff 算法

上面的 第7步，比较了俩个 DOM 的区别，diff 算法就是如何去比对

![diff 算法](./pic/diff 算法.jpg)

==setState 设计成异步的，是react为了节省性能，同时好几个state发生变化，最后做统一的虚拟DOM操作==

第一层节点不一样，就删掉当前节点下面所有的DOM，重新生成 虚拟 DOM。

1. react虚拟 DOM 对比是同层对比。（算法设计比较简单）
2. 虚拟 DOM 的对比要对比循环的 key 值（key值比对），删除或增加之后key值会发生变化，因此最好别使用 index 值作为 key值，可以使用 item 的内容来作为 key值。

# 9. ref 的使用

* 在react中我们使用 ref 来操作 DOM

* 使用：

  ```jsx
  // 在HTML属性中添加
  <input ref={(input) => {
  	this.input = input
  }}>
  ```

  不推荐使用 ref 尽量别直接操作 DOM，使用数据驱动的方式。

# react 的生命周期

* 在某一时刻会自动执行的函数

![react 生命周期](.\picture\react 生命周期.png)

1. constructor 在组件创建的时候会执行（但不是 react 独有的钩子）



1. componentWillMount 在组件将要被挂载的页面时候执行

2. 页面渲染的时候执行，render 中不能执行ajax请求，会执行多次
   1. state 和 props 发生变化的时候，就会执行 render 函数（在之后数据发生变化的时候，render 函数还会执行，但是 component 相关的钩子不会再执行了）
   2. 就算 props 不改变，只要父组件render执行，子组件render也会执行。（前提是子组件已经render过一次了，父组件再次渲染）
   
3. componentDidMount 组件被挂载到页面之上后执行，一般请求写在这个位置（只执行一次）

4. shouldComponentUpdate 在 state 和 props 发生变化的时候，组件被更新之前会执行，要求返回一个Boolean类型的值，根据这个值来判断是否组件是否需要更新。可以做性能的优化

   ```js
   shouldComponentUpdate(nextProps, nextState) {}
   ```

   

5. componentWillUpdate 在组件将要更新的时候执行（更新之前 ）

6. componentDidUpdate 组件更新完成之后

7. componentWillReceiveProps 1. 一个组件接受父组件传递的参数；2、父组件的 render ==重新==函数执行；符合这俩就会执行这个钩子（因为第一次可以执行 componentWillUpdate）

8. componentWillUnmount 组件将要去除的时候执行

* 所有的生命周期函数中只有render 函数必须手写，因为react内置了所有的生命周期函数，唯独没有内置render函数。

# 使用 charles 模拟请求数据（mock 数据）

![charles本地mock](./picture/charles本地mock.png)

* 在 tools 中的map local 中打开上图的步骤。
* Charles 抓不到 localhost 的包的问题，需要把 localhost 统一改成localhost.charlesproxy.com
* 抓包y一定要关闭电脑的科学上网代理

# 使用react-transition-group 实现动画

* 一般的 css 动画只能实现 css 动画，不能实现js 动画，需要借助第三方工具来实现。


## 1. 重点使用 css-transition <https://reactcommunity.org/react-transition-group/css-transition>

```jsx
return (
    <div>
      <CSSTransition in={inProp} timeout={200} classNames="my-node">
        <div>
          {"I'll receive my-node-* classes"}
        </div>
      </CSSTransition>
      <button type="button" onClick={() => setInProp(true)}>
        Click to Enter
      </button>
    </div>
  );
```

* in 触发方式
* timeout 时间
* className 动画的样式

* 添加之后会给该子元素添加如下的样式（example 就是 classNames 后面跟着的，如果不加 classNames,则为名字则为 fade）

  * 动画刚刚进入入场动画的时候 example-enter
  * example-enter-active  入场动画刚出现到完成之间出加上 该类
  * fade-enter-done 进场动画结束的时候，会加上该类
  * `fade-exit`, `fade-exit-active`, and `fade-exit-done`. 同样

  全部根据变量的 true 和 false 来判定

## 2. 能够使用 js 来实现动画

* css-transition 提供了一些钩子函数能够实现动画，接受 dom 对象改写样式

  1. onEnter 

     ```jsx
     <CSSTransition in={inProp} timeout={200} classNames="my-node" onEnter={(el) => { el.style.color = 'red' }}>
     ```

## 3. 元素第一次出现的时候也要动画效果

* 在 cssTransition 标签上添加 `appear={true}` 代表刚出现也要动画效果，然后在 css 里面修改样式

## 4. 多个元素使用动画

* 需要在最外层加上 `TransitionGroup` 标签

# redux 

* 数据层框架

  组件之间传值的问题

* redux = reducer + flux

redux 设计理念：

![redux 设计理念](./picture/redux 设计理念.png)

## 1. Redux 工作流程

![redux 工作流程](./picture/redux 工作流程.png)

1. React  Component 是一个借书者
2. action 是借书的动作
3. store 是图书管理员
4. Reducers 是记录本(store 在 reducers 里面查询)

## 2. Redux 使用

1. 创建 store 

   ```js
   import { createStore } from 'redux'
   
   // 创建笔记本，存储想要存储的数据
   const defaultState = {
     inputValue: "",
     list: []
   }
   const reducer = (state = defaultState, action) => {
     // state 默认就是整个 redux 里面存储的数据
     //  state 存储 store 着上次的数据
     // action 就是用户穿过的一段话
     return state
   }
   
   const store = createStore(reducer)
   // 创建管理员，管理员还没有记录本，什么都不知道
   
   export default store
   ```
   页面上使用 store 

   ```js
   import store form "./store"
   
   ```

2. 在组件中使用 store

   ```js
   constructor(props) {
     super(props)
     // store 提供了一个方法能获取到 store 里面的数据
     // store.getState()
     this.state = store.getState()
   }
   ```


* 使用 redux 开发者工具开发 redux

  安装 redux devtools 之后打开控制台，进行配置

  在store 里面加

  ```js
  const store = createStore(
     reducer, /* preloadedState, */
  +  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
   );
  ```

3. 页面调用 store里面的数据

   ```js
   // 页面的方法
   const action = {
       type: 'change_input_value',
       value: e.target.value
   }
   store.dispatch(action)
   // 调用 dispatch 吧 action 传递给 store
   ```

4. store 需要把当前的数据和action转发给 reducers

   ```js
   const reducer = (state = defaultState, action) => {
     // state 默认就是整个 redux 里面存储的数据
     //  state 存储 store 着上次的数据
     // action 就是用户穿过的一段话
     if (action.type === 'change_input_value') {
       const newState = JSON.parse(JSPN.stringify(state))
       newState.inputValue = action.value  // 吧原来的 inputValue  修改
       return newState
       // reducer 可以接受 state ，但是绝对不能修改 state
     }
     return state
     // store 拿到这个数据之后替换原来的数据 
   }
   ```

5. 页面订阅 store 里面数据的改变

   ```js
   constructor (props) {
     super(props)
     store.subscribe(this.handelStoreChange)
     // store 发生变化，调用 hangdelStoreChange 方法
   }
   ```
   
6. 使用 actionCreator 统一创建 action

   就是把 action 统一放到一个文件中用函数处理

   ```js
   import { CHANGE_INPUT_VALUE } from './actionTypes'
   
   export const getInputChangeAction = (value) => ({
       type: CHANGE_INPUT_VALUE,
       value
   })
   ```

   

## actionTypes 的拆分

* 在开发的过程中 action 里面传递和接受字符串，如果字符串写错的话，不容易发现错误，（因为传递什么type action都不会报错的）因此我们可以吧 actionType 单独放到一个文件中，用变量的形式来获取该 type的名称，这样就算拼写错误也会找不到变量对应的 type 而报错。
* 创建 actionTypes 文件来存放变量对应的字符串，然后导出

## redux 开发原则

1. store 是惟一的
2. 只有 store 能改变自己的内容
3. reducer 必须是纯函数（给定固定的输入，就一定有固定的输出。而且不会有副作用。就是说return 的数据只受输入的数据的影响，副作用是会对参数进行修改）

## redux 发送异步请求获取数据

### 1. Redux-thunk 中间件实现ajax请求

* 为了避免异步请求太多，钩子函数内逻辑复杂。便于做自动化测试，和代码的相应管理

参考文档

```js
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers/index';

// Note: this API requires redux@>=3.1.0
const store = createStore(rootReducer, applyMiddleware(thunk));
// applyMiddleware 里面是redux中间件
```


使用 thunk中间件，同时使用dev-tools(参考 dev-tools文档)
```js
import { createStore, applyMiddleware, compose } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers/index';

const composeEnhancers =
  typeof window === 'object' &&
  window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?   
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__() : compose;

const enhancer = composeEnhancers(
  applyMiddleware(...[thunk]),
  // other store enhancers if any
);

// Note: this API requires redux@>=3.1.0
const store = createStore(rootReducer, enhancer);
// applyMiddleware 里面是redux中间件
```

* 使用了 thunk 之后，action 就能是对象或者函数了

  dispatch 这个action 之后 action 这个函数就会自动的执行返回的函数

  ```js
  // 在 actionCreator 文件中创建函数的 action
  export const getTodoList = () => {
      return (dispath) => {
          // 这个 函数 action 的参数能接受到 store 的 dispatch  函数，可以调用本文件中的 action
        // 这里面写异步请求
      }
  }
  ```
  
* 在页面中使用该 方法 action

  ```js
  const action = getTodoList()
      store.dispatch(action)
  ```

  

### 2. redux 中间件

* ==一定要记住是 redux 的中间件，不是 react 的中间件==

![redux 执行流程](./picture/redux 执行流程.png)

* 中间件指的是 action 和 store 中间

  action 和 store 中间使用的 dispatch 方法，middleware 就是对 dispatch 的封装（升级）

### 3. Redux-saga 中间件的使用

* 可以替换 redux-thunk 来实现异步请求写到 action 中，常用拆分出一部的代码

```js
import { createStore, applyMiddleware, compose } from 'redux';
import createSagaMiddleware from 'redux-saga';
import rootReducer from './reducers/index';
import mySaga from './sagas'

const sagaMiddleware = createSagaMiddleware()
const composeEnhancers =
  typeof window === 'object' &&
  window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?   
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__() : compose;

const enhancer = composeEnhancers(
  applyMiddleware(...[sagaMiddleware]),
  // other store enhancers if any
);

// Note: this API requires redux@>=3.1.0
const store = createStore(rootReducer, enhancer);
sagaMiddleware.run(mySaga)
// applyMiddleware 里面是redux中间件
```

sagas.js 文件

```js
import { call, put, takeEvery, takeLatest } from 'redux-saga/effects'
// 使用 put 方法实现 store.dispatch 给 store传递action

function* mySaga() {
  yield takeEvery("USER_FETCH_REQUESTED", fetchUser);
    // action 类型，补货action，调用 fetchUser 方法
}

function* fetchUser(action) {
   try {
      const user = yield call(Api.fetchUser, action.payload.userId);
      yield put({type: "USER_FETCH_SUCCEEDED", user: user});
   } catch (e) {
      yield put({type: "USER_FETCH_FAILED", message: e.message});
   }
}

export default mySaga;
```

* 使用 saga action 里面还是写成对象，action 和 sagas.js 文件都能够接受到 action。一旦接受到这个action就执行后面的函数。
* generator 函数处理失败的情况可以使用 try catch 捕获

## react-redux 使用

* 安装

### 使用

1. 提供 provider api 

   index.js 文件

   ```jsx
   import { Provider } from 'react-redux'
   import store from './store'
   
   const App = (
   	<provider store='store'>
           <Todolist />
       </provider>
   )
   // provider 里面的组件都能获取到 store 里面的内容
   ```

   Todolist 文件

   ```js
   import { connect } from 'react-redux'
   
   // 将 store 里面state 的数据映射到 组件 props 里面的数据
   const mapStateToProps = (state) => {
     return {
    inputValue: state.inputValue
       // 直接在组件中使用 this.props.inputValue 来获取store 里面保存的值
     }
   }
   
   // store.dispatch, props 关联
   const mapDispatchToProps = (dipatch) => {
     // 直接使用 this.props.changeInputValue 的方式来调用该方法
     return {
       changeInputValue() {
         const action = {
           tyep: ""
         }
         dispatch(action)
       }
     }
   }
   
   // 在导出组件的时候，让组件和store 连接
   export default connect(mapStateToProps, mapDispatchToProps)(Todolist)
   ```
   
   采用如上方式，就不需要引入store了（props 里面的数据能够解构赋值定义）
   
   todilist 到处的是容器组件

# ui 组件和容器组件

* ui 组件（傻瓜组件）、容器组件（智能组件），逻辑和渲染分开。ui 组件专门做渲染，容器组件做逻辑

  就是页面的结构封装成一个组件

* 容器组件只使用了 ui 组件，ui组件的逻辑是 容器组件来实现的

# 无状态组件

* 当一个组件只有一个 render 函数的时候，我们就可以定义一个无状态组件（就是一个函数）

```jsx
const item = (props) => {
  // 返回一个 jsx
  return (
  	<div onClick={props.delete}></div>
  )
}
```

* 一般情况下 UI 组件能够通过 无状态组件来实现

# ant  design 

