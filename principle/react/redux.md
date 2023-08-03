# 入门

## 动机

https://www.redux.org.cn/docs/introduction/Motivation.html

随着前端单页应用的发展，需要维护的状态越来越多。**state 在什么时候，由于什么原因，如何变化已然不受控制。**当系统变得错综复杂的时候，想重现问题或者添加新功能就会变得举步维艰。

* 说白了还是想降低复杂度，让系统的状态变化变得可预测。

### 问题的根源

**我们总是将两个难以理清的概念混淆在一起：变化和异步**，我们称之为[曼妥思和可乐](https://en.wikipedia.org/wiki/Diet_Coke_and_Mentos_eruption)现象（也就是两种物质在一起会出现爆炸的现象，变得不可控。如果把二者分开，能做的很好，但混到一起，就变得一团糟。）

* 感觉这个就是拆分的本质，多种东西混到一起就会爆炸。

### react 做了什么

react试图在视图层禁止异步和直接操作 DOM 来解决这个问题。中不足的是，React 依旧把处理 state 中数据的问题留给了你。

* redux 就是为了帮助你解决这个问题
* react做了：也就是隐藏掉mvc中的controller，你不需要手动的修改DOM，让react去做批处理，从而隔离了视图层和数据层，降低了认知复杂度。

### redux 要做什么

redux 试图让state的变化变得可预测，通过限制更新发生的时间和方式。

这些限制条件反映在 Redux 的[三大原则](https://www.redux.org.cn/docs/introduction/ThreePrinciples.html)中。

## 什么时候需要使用呢？

Redux 可帮助你处理共享状态的管理，但与任何工具一样，它也需要权衡利弊。使用 Redux 有更多的概念需要学习，还有更多的代码需要编写，需要添加了一些额外代码，并要求你遵循某些限制。这是短期和长期生产力之间的权衡。

* redux更像是一种编码规范的创新（主要是约束）

在以下情况下使用 Redux：

- 应用中有很多 state 在多个组件中需要使用
- 应用 state 会随着时间的推移而频繁更新
- 更新 state 的逻辑很复杂
- 中型和大型代码量的应用，很多人协同开发

## redux库和工具——为啥需要安装那么多库，有啥关系呢？

Redux 是一个小型的独立 JS 库。 但是，它通常与其他几个包一起使用：

### redux-react——结合react

Redux 可以集成到任何的 UI 框架中，其中最常见的是 React 。[**React-Redux**](https://react-redux.js.org/) 是我们的官方包，它可以让 React 组件访问 state 片段和 dispatch actions 更新 store，从而同 Redux 集成起来。

### Redux-toolkit

[**Redux Toolkit**](https://redux-toolkit.js.org/) 是我们推荐的编写 Redux 逻辑的方法。 它包含我们认为对于构建 Redux 应用程序必不可少的包和函数。 Redux Toolkit 构建是我们建议的最佳实践中，简化了大多数 Redux 任务，预防了常见错误，并使编写 Redux 应用程序变得更加容易

* Redux 的最佳实践

### Redux DevTools 扩展

[**Redux DevTools 扩展**](https://github.com/zalmoxisus/redux-devtools-extension) 可以显示 Redux 存储中状态随时间变化的历史记录。这允许你有效地调试应用程序，包括使用强大的技术，如“时间旅行调试”。

# redux 术语和概念

## 单向数据流

- 用 state 来描述应用程序在特定时间点的状况
- 基于 state 来渲染出 View
- 当发生某些事情时（例如用户单击按钮），state 会根据发生的事情进行更新，生成新的 state
- 基于新的 state 重新渲染 View

![单向数据流](https://cn.redux.js.org/assets/images/one-way-data-flow-04fe46332c1ccb3497ecb04b94e55b97.png)

## 多个state怎么共享数据

* 一般情况下就会提升state到共同的父组件

> 解决这个问题的一种方法是从组件中提取共享 state，并将其放入组件树之外的一个集中位置。这样，我们的组件树就变成了一个大“view”，任何组件都可以访问 state 或触发 action，无论它们在树中的哪个位置！
>
> 通过定义和分离 state 管理中涉及的概念并强制执行维护 view 和 state 之间独立性的规则，代码变得更结构化和易于维护。
>
> 这就是 Redux 背后的基本思想：应用中使用集中式的全局状态来管理，并明确更新状态的模式，以便让代码具有可预测性。

* redux的目标就是提取state，然后维护state和view之间的关系，这就是redux做的事！

## 不可变性

JavaScript的数组和对象都是可变的，引用不变但是内容可能会变化。

### 不可变性有啥好处

> 不变性可以为你的应用程序带来更高的性能，并导致更简单的编程和调试，因为从不可更改的数据比在整个应用程序中可以随意更改的数据更容易推理。
>
> 特别是，Web 应用程序上下文中的不变性使得复杂的变更检测技术能够简单而廉价地实现，确保仅在绝对必要时才发生计算昂贵的更新 DOM 过程（React 相对于其他库的性能改进的基石）.

* 不可变性带来更高的性能，导致更简单的编程和调试，更容易推理
* web应用程序上下文中的不可变性，更利于复杂的变更检测技术（不用做深度遍历了，只需要判断引用是否变化），避免频繁进行的昂贵的更新DOM操作
* 更利于GC，具体

### react的不可变性

如上：

> web应用程序上下文中的不可变性，更利于复杂的变更检测技术（不用做深度遍历了，只需要判断引用是否变化），避免频繁进行的昂贵的更新DOM操作

* 说白了就是提升react虚拟DOM的diff能力，提升性能。

### 为什么 Redux 需要不可变性？

* Redux 和 React-Redux 都使用 [浅相等检查](https://cn.redux.js.org/faq/immutable-data#how-do-shallow-and-deep-equality-checking-differ)。
* 不可变的数据管理最终使数据处理更安全。
* 时间旅行调试要求 reducer 是没有副作用的纯函数，这样你就可以正确地在不同的 state 之间跳转。

### 为什么reducer需要设计成纯函数

* 就是为了保证不可变性，防止数据来源不可追踪，不能有副作用。

### 为什么 Redux 使用浅相等检查需要不可变性

* 正是为了提高性能，Redux 使用了浅相等检查。

  为了提高性能，所以也需要不可变性

# 基础

## 术语

### action

**action** 是一个具有 `type` 字段的普通 JavaScript 对象。**你可以将 action 视为描述应用程序中发生了什么的事件**.

`type` 字段是一个字符串，给这个 action 一个描述性的名字，比如`"todos/todoAdded"`。我们通常把那个类型的字符串写成“域/事件名称”，其中第一部分是这个 action 所属的特征或类别，第二部分是发生的具体事情。

action 对象可以有其他字段，其中包含有关发生的事情的附加信息。按照惯例，我们将该信息放在名为 `payload` 的字段中。

* type
* payload

### Action Creator

**action creator** 是一个创建并返回一个 action 对象的函数。它的作用是让你不必每次都手动编写 action 对象：

### reducer

**reducer** 是一个函数，接收当前的 `state` 和一个 `action` 对象，必要时决定如何更新状态，并返回新状态。函数签名是：`(state, action) => newState`。 **你可以将 reducer 视为一个事件监听器，它根据接收到的 action（事件）类型处理事件。**

* 视为一个事件监听器，根据接受的action事件类型处理事件

> "Reducer" 函数的名字来源是因为它和 [`Array.reduce()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 函数使用的回调函数很类似。

#### reducer 遵循的原则

- 仅使用 `state` 和 `action` 参数计算新的状态值
- 禁止直接修改 `state`。必须通过复制现有的 `state` 并对复制的值进行更改的方式来做 *不可变更新（immutable updates）*。
- 禁止任何异步逻辑、依赖随机值或导致其他“副作用”的代码

#### reducer 步骤

- 检查 reducer 是否关心这个 action
  - 如果是，则复制 state，使用新值更新 state 副本，然后返回新 state
- 否则，返回原来的 state 不变

### store

当前 Redux 应用的 state 存在于一个名为 **store** 的对象中。

store 是通过传入一个 reducer 来创建的，并且有一个名为 `getState` 的方法，它返回当前状态值：

* 新版的 toolkit 是通过 useSelector 来获取state的

```typescript
useSelector(store => store.counter)
// 返回的值来确定具体的store
```

### dispatch

Redux store 有一个方法叫 `dispatch`。**更新 state 的唯一方法是调用 `store.dispatch()` 并传入一个 action 对象**。

> **dispatch 一个 action 可以形象的理解为 "触发一个事件"**。发生了一些事情，我们希望 store 知道这件事。 Reducer 就像事件监听器一样，当它们收到关注的 action 后，它就会更新 state 作为响应。

### selector

**Selector** 函数可以从 store 状态树中提取指定的片段。随着应用变得越来越大，会遇到应用程序的不同部分需要读取相同的数据，selector 可以避免重复这样的读取逻辑

* 用来选取片段的，靠返回值确定

### redux数据流

https://cn.redux.js.org/tutorials/essentials/part-1-overview-concepts#redux-%E6%95%B0%E6%8D%AE%E6%B5%81

### Redux Slice

* 按功能拆分的单个 reducer 逻辑（state包含在其中）和 action 的集合，就是把redux state拆分成多个state，便于管理。

**“slice” 是应用中单个功能的 Redux reducer 逻辑和 action 的集合**, 通常一起定义在一个文件中。该名称来自于将根 Redux 状态对象拆分为多个状态 “slice”。

* 建议命名为 **slice.ts 或者 \*\*reducer.ts

```typescript
import { configureStore } from '@reduxjs/toolkit'
import usersReducer from '../features/users/usersSlice'
import postsReducer from '../features/posts/postsSlice'
import commentsReducer from '../features/comments/commentsSlice'

export default configureStore({
  reducer: {
    users: usersReducer,
    posts: postsReducer,
    comments: commentsReducer
  }
})
```

## redux 三大原则

https://www.redux.org.cn/docs/introduction/ThreePrinciples.html

### 1.单一数据源

**整个应用的 [state](https://www.redux.org.cn/docs/Glossary.html#state) 被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 [store](https://www.redux.org.cn/docs/Glossary.html#store) 中。**

> 这让同构应用开发变得非常容易。来自服务端的 state 可以在无需编写更多代码的情况下被序列化并注入到客户端中。由于是单一的 state tree ，调试也变得非常容易。在开发中，你可以把应用的 state 保存在本地，从而加快开发速度。此外，受益于单一的 state tree ，以前难以实现的如“撤销/重做”这类功能也变得轻而易举。

* 只有一个store
* 关键词：同构、单一的state tree，redo/undo变得轻而易举

### 2. state是只读的

**唯一改变 state 的方法就是触发 [action](https://www.redux.org.cn/docs/Glossary.html#action)，action 是一个用于描述已发生事件的普通对象。**

> 这样确保了视图和网络请求都不能直接修改 state，相反它们只能表达想要修改的意图。因为所有的修改都被集中化处理，且严格按照一个接一个的顺序执行，因此不用担心 race condition 的出现。 Action 就是普通对象而已，因此它们可以被日志打印、序列化、储存、后期调试或测试时回放出来。

* 确保试图和网络请求不能直接修改state，只能表达修改的意图
* 不用担心竞态问题，严格按照一条一条的顺序执行，变得可预测（那么也就是说集中式的store是解决不可预测问题的关键方案）

### 3. 使用纯函数来修改state

> Reducer 只是一些纯函数，它接收先前的 state 和 action，并返回新的 state。刚开始你可以只有一个 reducer，随着应用变大，你可以把它拆成多个小的 reducers，分别独立地操作 state tree 的不同部分，因为 reducer 只是函数，你可以控制它们被调用的顺序，传入附加数据，甚至编写可复用的 reducer 来处理一些通用任务，如分页器。

* 也就是只能通过reducer来修改redux，并且reducer是一个纯函数！
* 拆分redux主要是拆分reducer，因为store是唯一的，修剪树枝的范围是可以拆分的！

## redux 拆分

* 主要就是拆分 reducer

## redux toolkit

[**Redux Toolkit**](https://redux-toolkit.js.org/) 是我们官方推荐的编写 Redux 逻辑的方法。它围绕 Redux 核心，并包含我们认为对于构建 Redux 应用必不可少的软件包和功能。Redux Toolkit 建立在我们建议的最佳实践中，简化了大多数 Redux 任务，防止了常见错误，并使编写 Redux 应用程序更加容易。

* redux 官方的最佳实践

> RTK 包含了有助于简化许多常见场景的工具，包括 [配置 Store](https://redux-toolkit.js.org/api/configureStore)， [创建 reducer 并编写 immutable 更新逻辑](https://redux-toolkit.js.org/api/createreducer)， 甚至还包含 [一次性创建整个 State 的 “Slice”](https://redux-toolkit.js.org/api/createslice)。



## action

> 强制使用 action 来描述所有变化可以让我们清晰地知道应用中到底发生了什么。如果一些东西改变了，就可以知道为什么变。actions 就像是描述所发生事情的面包屑导航。 最终，我们开发一个函数将 action 和 state 联系起来，这个函数就是 reducer。同样，这也没有使用什么魔法，reducer 只是一个接收 state 和 action作为其参数，并返回给应用新的 state 的函数。

* 说白了就是给每次一改动一个标记，方便溯源
* 对于大的应用来说，不大可能仅仅只写一个这样的函数，所以我们编写很多小函数来分别管理 state 的一部分：

## redux 是什么

**Redux 是一个使用叫做 “action” 的事件来管理和更新应用状态的模式和工具库** 它以集中式 Store（centralized store）的方式对整个应用中使用的状态进行集中管理，其规则确保状态只能以可预测的方式更新。

* 不要把redux当成react应用专属，它更像是一个类似于rxjs的库或者工具。

## 使用流程

### 1. 创建 Redux store

```typescript
import { configureStore } from '@reduxjs/toolkit'

export default configureStore({
  reducer: {}
})
```

上面代码创建了 Redux store ，并且自动配置了 Redux DevTools 扩展 ，这样你就可以在开发时调试 store。

### 2. 为react提供redux store

创建 store 后，便可以在 React 组件中使用它。 在 src/index.js 中引入我们刚刚创建的 store , 通过 React-Redux 的 `<Provider>`将 `<App>` 包裹起来,并将 store 作为 prop 传入。

```typescript
import React from 'react'
import ReactDOM from 'react-dom'
import './index.css'
import App from './App'
import store from './app/store'
import { Provider } from 'react-redux'

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
// 并没有直接传入 app 中，只是在外层提供了一个store，原理是什么呢？？？
```

### 3. 创建 redux state slice -state切片 

创建 slice 需要一个字符串名称来标识切片、一个初始 state 以及一个或多个定义了该如何更新 state 的 reducer 函数。slice 创建后 ，我们可以导出 slice 中生成的 Redux action creators 和 reducer 函数。

Redux 要求[我们通过创建数据副本和更新数据副本，来实现不可变地写入所有状态更新](https://redux.js.org/tutorials/fundamentals/part-2-concepts-data-flow#immutability)。不过 Redux Toolkit `createSlice` 和 `createReducer` 在内部使用 Immer 允许我们[编写“可变”的更新逻辑，变成正确的不可变更新](https://redux.js.org/tutorials/fundamentals/part-8-modern-redux#immutable-updates-with-immer)。

```typescript
import { createSlice } from '@reduxjs/toolkit'

export const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0
  },
  reducers: {
    increment: state => {
      // Redux Toolkit 允许我们在 reducers 写 "可变" 逻辑。它
      // 并不是真正的改变状态值，因为它使用了 Immer 库
      // 可以检测到“草稿状态“ 的变化并且基于这些变化生产全新的
      // 不可变的状态
      state.value += 1
    },
    decrement: state => {
      state.value -= 1
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload
    }
  }
})
// 每个 case reducer 函数会生成对应的 Action creators
export const { increment, decrement, incrementByAmount } = counterSlice.actions

export default counterSlice.reducer
```

> Redux Toolkit 允许我们在 reducers 写 "可变" 逻辑。它
>       // 并不是真正的改变状态值，因为它使用了 Immer 库
>       // 可以检测到“草稿状态“ 的变化并且基于这些变化生产全新的
>       // 不可变的状态

# 进阶

## 异步逻辑和数据请求

### redux-thunk

* redux store本身是不关注异步逻辑的，只是同步dispatch一个action，通过调用 root reducer 函数更新状态，并通知 UI 某些事情发生了变化。任何异步都必须发生在 store 之外。

Redux Toolkit 的 `configureStore` 功能[默认自动设置 thunk middleware](https://redux-toolkit.js.org/api/getDefaultMiddleware#included-default-middleware)，[我们推荐使用 thunk 作为 Redux 开发异步逻辑的标准方式](https://cn.redux.js.org/style-guide/#use-thunks-for-async-logic)。

RTK集成redux-thunk特点之一就是可以dispatch一个函数，函数可以传入两个参数dispatch、getState，这样就可以在dispatch的参数函数中书写异步逻辑，在真正需要dispatch的时候再同步的dispatchaction。

```typescript
const store = configureStore({ reducer: counterReducer })

const exampleThunkFunction = (dispatch, getState) => {
  const stateBefore = getState()
  console.log(`Counter before: ${stateBefore.counter}`)
  // await 一个异步逻辑
  dispatch(increment())
  const stateAfter = getState()
  console.log(`Counter after: ${stateAfter.counter}`)
}

store.dispatch(exampleThunkFunction)
```

### 书写规范

> Thunk 通常写在 “slice” 文件中。`createSlice` 本身对定义 thunk 没有任何特殊支持，因此你应该将它们作为单独的函数编写在同一个 slice 文件中。这样，他们就可以访问该 slice 的普通 action creator，并且很容易找到 thunk 的位置。

* 不需要引入就可以很方便的访问普通的 action creator

### thunk函数

“thunk” 这个词是一个编程术语，意思是 ["一段做延迟工作的代码"](https://en.wikipedia.org/wiki/Thunk). 如何使用 thunk 的详细信息，请参阅 thunk 使用指南页面:

## 使用 createAsyncThunk 请求数据

### 为什么

因为请求中的pending、rejected的状态需要人工去创建这个状态，使用 createAsyncThunk 后，这些逻辑就不需要手动去书写了（主要是action creator的逻辑）

### 使用 createAsyncThunk

```typescript
// 只是生产除了异步thunk，还需要实现具体的reducer能力（修改state），因此需要添加到extraReducers中
export const getTitleData = createAsyncThunk('async/getTitle', async () => {
  console.log('---res1')
  const res = await query()
  console.log(res, '---res')
  return res
});

// 由组件内部控制请求的状态
export const getTitleDataNoStatus = createAsyncThunk('async/getTitle2', async () => {
  const res = await query()
  return res
});

// state 必须是引用类型
const asyncSlice = createSlice({
  name: 'async',
  initialState,
  reducers: {
    setTitle(state, action: PayloadAction<string>) {
      state.title = action.payload
    },
  },
  // 拼接外部的reducer，还可以拼接其他 slice的reducer
  extraReducers(builder) {
    builder.addCase(getTitleData.pending, (state, action) => {
      state.status = 'loading'
    }).addCase(getTitleData.fulfilled, (state, action) => {
      state.status = 'success'
      state.title = action.payload
    }).addCase(getTitleData.rejected, (state, action) => {
      state.status = 'failed'
      state.error = action.error.message!
    }).addCase(getTitleDataNoStatus.fulfilled, (state, action) => {
      state.title = action.payload
    })
  },
})
```

* 如上代码，外界直接 dipatch getTitleData即可触发这个action。action 的处理有 builder 捕获。

### reducer只处理成功状态，pending交给组件处理

* redux也可以只处理成功的逻辑（修改state），然后让组件自己处理pending和error的状态

  只需要在 addCase 中添加 fulfilled 的状态

```typescript
// 把loading交给组件
const changeStatus = async () => {
  setLoading(true)
  try {
    const res = await dispatch(getTitleDataNoStatus()).unwrap();
    console.log(res, '----getTitleDataNoStatus');
  } catch (err) {
    console.log(err, '---err')
  }
  setLoading(false)
}
```

* 如上直接使用 unwrap 函数（不使用，则由rtk捕获错误），组件内部处理中间逻辑

### 返回值的方式修改state

* 由于redux tk已经使用了immer实现了mutate的逻辑，我们一般采用直接修改state的方式修改原state

  Immer 让我们以两种方式更新状态：要么 *mutate* 现有状态值，要么 *return* 一个新结果。如果我们返回一个新值，它将用我们返回的任何内容完全替换现有状态。

也就是说我们也可以直接返回一个新结果来修改state，适合于简单的slice，一般情况下只有一个值，直接返回一个值进行修改。

## middleware

- dispatch action 时执行额外的逻辑（例如打印 action 的日志和状态）
- 暂停、修改、延迟、替换或停止 dispatch 的 action
- 编写可以访问 `dispatch` 和 `getState` 的额外代码
- 教 `dispatch` 如何接受除普通 action 对象之外的其他值，例如函数和 promise，通过拦截它们并 dispatch 实际 action 对象来代替

### 实现原理——一步一步实现middleware

参考：https://cn.redux.js.org/understanding/history-and-design/middleware#%E5%B0%9D%E8%AF%95-6%E7%AE%80%E5%8D%95%E7%B2%97%E6%9A%B4%E7%9A%84-middleware-%E8%B0%83%E7%94%A8%E6%96%B9%E6%B3%95

代码实现：参考 code -> my-middleware.ts

koa middleware的实现参考：code -> koa-middleware.ts

### 使用



### thunk-middleware

* 实现：主要方案是把dispatch作为一个参数传入，等待异步完成之后执行这个dispatch。

```typescript
import { Middleware, PayloadAction } from '@reduxjs/toolkit'

export const MyThunkMiddleware: Middleware = store => next => (action: (params: any) => any | PayloadAction<any>) => {
  return typeof action === 'function' ? action(next) : next(action)
}
```

## 性能-

https://cn.redux.js.org/tutorials/essentials/part-6-performance-normalization#%E6%8F%90%E5%8D%87%E6%B8%B2%E6%9F%93%E6%80%A7%E8%83%BD

### 缓存 selector

* 

### 范式化数据



# 原理分析

* 参考：https://cloud.tencent.com/developer/article/1591283

## redux 没有魔法

* Redux 没有使用更多的方法去帮你实现状态的可预测性
  * 更多的是行为上的约束，比如说reducer三大原则。基本上属于一种模式的创新。

> Redux 没有“魔法”——你应该能够理解应用程序中发生了什么，因为**所有 Redux 逻辑都遵循相同的基本模式，即通过 reducers 调度操作和更新状态**。这确实意味着有时你必须编写更多代码才能使事情发生，但权衡是应该非常清楚数据流和行为是什么。



# 与其他库的对比

## 与 mobx

* redux是集中式的，只有多个组件使用state的时候才需要使用
* mobx是分布式的，天然的数据和视图分离

# 参考

* 官网

  https://cn.redux.js.org/introduction/getting-started