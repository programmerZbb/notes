# ts 再学习

## 编程语言类型

* 动态类型语言（dynamically typed language）
* 静态类型语言（statically typed language）

## typescript 究竟是什么

* JavaScript that scales（官网）
* 静态类型风格的类型系统
* 从 es6 到 es10 甚至 esnext 的语法支持
* 兼容各种浏览器，各种系统，各种服务器，完全开源。

# 环境搭建

* 初始化项目

  ```shell
  npx create-react-app my-project --typescript
  ```

## npx 是什么

>  version > npm 5.2

* 避免安装全局模块

* 调用项目内部安装的模块

  有的情况安装了一个模块，执行该模块的命令，没有发现命令

  需要执行 `node_modules/.bin/mocha --version` （以mocha为例），来执行这个命令

  使用了 npx 就可以直接使用 `npx mocha --version`来执行

  而 npm run 会将scripts 中的命令(node_modules/.bin)加入 path 中，执行完成之后再删除

# 起步

## 1.小试牛刀

```tsx
import React from 'react';

interface IProps {
    message: string;
}

const Hello: React.FunctionComponent<IProps> = (props) => {
    console.log(props.children, '---测试')
    return <div>{ props.message }</div>
}

export default Hello
```

`React.FunctionComponent` 为react官方提供的一个 interface，可以简称 `React.FC`

## 什么是 react hook

* react 16.8 带来的全新特性，即将替代 class 组件的写法
* react 中的组件
  * class 类型的组件
  * function 类型的组件，不能使用生命周期，不能使用 state 

### 特点

* 没有破坏性改动

  * 完全可选
  * 百分之百向后兼容
  * 没有计划从 react 中移除 class

* 解决痛点

  * 组件很难复用状态逻辑

    相较于 HOC 和 render props ，前者会产生不必要的节点

  * 复杂组件难以理解尤其是生命周期函数

  * react 组件一直是函数，使用 hook 完全拥抱函数，不用再管复杂的 this

### what

* hook 是特殊的函数，可以让你钩入 react 的特性

## 2. useState (查看 react16 文档)

## 3. useEffect

* effect hook（副作用函数）

分为两类

1. 无需清楚的 effect

   * 比如说文档标题的跟新，在 didmount 和 didUpdate 中都需要修改

     ```js
     document.title = '新的'
     ```

2. 需要清除的

   * 比如说在初次渲染的时候 addEventListener 监听了一个事件，然后没有清除就会绑定的越来越多。

### 使用

* useEffect 中依赖为空，则初次渲染和更新都会执行
* 依赖为 空数组，则第一次执行
* 依赖为某个具体的值，则第一个和更新执行
* 第一个参数return 的函数，不会立即执行，在函数卸载或者，更新的时候执行。
  * 有依赖更新和卸载都执行，（任意一项发生变化）
  * 无依赖，只有卸载执行

## 自定义 hook

* 自定义的 hook 没有一般没有ui，不需要做 view 的操作，抽离出一些公共的逻辑，一般情况连 props 都不需要传入
* 自定义hook必须以 use 开头，比如 `useMousePosition`

## 没有对比没有伤害

### 高阶组件 hoc

* 高阶组件就是一个函数，接受一个组件作为参数，返回一个新的组件

* 思考

  空节点

  ```html
  <>
  </>
  ```

  typing 配置是干什么的
* 将组建逻辑提取到可复用的函数中
  * 为了增加和组件命名的区别，自定义 hook 也是一个函数，需要使用 use 开头的函数
  * 不通组件每次使用相同的 hook 不会共享同一个 state ，每次都是不同的

## 4. useRef

### 为什么

* useState 的数据每次渲染都是新的数据， 不会随着时间改变。比如说 onclick 绑定了一个常量，并不会随着 常量值在后期被设置而变化，会一直绑定的原来的值。每次渲染都是独立的。
* 因此得到结论，在任意一次渲染中，props 和 state 都是独立的。使用到他们的任何值也是独立的。
## useRef

* 每次渲染数据都是独立的，怎样让每次的渲染产生联系呢
* 使用 useRef 更改数据不会每次都更新组件

```tsx
const likeRef = useRef(0)

// 获取到，永远能获取到最新的数据
console.log(likeRef.current)
// 设置也是一样的
likeRef.current++
```

* 使用 useRef 能够获取真实的 DOM 节点

```jsx
<input type="text" ref={inputEle}/>
// 不是用current来获取的
// 但是能用 current 来设置
inputEle.current.focus()
```

## useContext

* 在 hook 中使用 context

  ```tsx
  // 公共部分，创建 context
  const ThemeContext = React.createContext('light')
  
  // 父组件
  <ThemeContext.Provider value="dark">
     <TestContext></TestContext>
  </ThemeContext.Provider>
  
      // 子组件
   // 绑定context
  import { useContext } from 'react'
  const theme = useContext(ThemeContext)
   
  <div>
      test
      <div>{this.context}</div>
  </div>
  ```


## 注意

1. 所有的 hook 都需要写到函数组件里面，不能写到函数外边

# 开始组建

## 1. 完成一个组建库需要考虑的问题

* 代码结构
* 样式解决方案
* 组件需求分析和编码
* 组件测试用例分析和编码
* 代码打包输出和发布

## 2. 样式解决方案

采用less sass

## 3.  主题

* 系统色板 - 基础色板 + 中性色板
* 产品色板 - 品牌色 + 功能色板





# 总结react内置的 类型（包括接口）

1.  React.FC 函数组件类型

2. MouseEvent 

   在接受鼠标事件的时候，传进的 e 的接口

3. HTMLInputElement 

   这种的很多
