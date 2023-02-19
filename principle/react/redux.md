# 入门

## redux toolkit

[**Redux Toolkit**](https://redux-toolkit.js.org/) 是我们官方推荐的编写 Redux 逻辑的方法。它围绕 Redux 核心，并包含我们认为对于构建 Redux 应用必不可少的软件包和功能。Redux Toolkit 建立在我们建议的最佳实践中，简化了大多数 Redux 任务，防止了常见错误，并使编写 Redux 应用程序更加容易。

* redux 官方的最佳实践

> RTK 包含了有助于简化许多常见场景的工具，包括 [配置 Store](https://redux-toolkit.js.org/api/configureStore)， [创建 reducer 并编写 immutable 更新逻辑](https://redux-toolkit.js.org/api/createreducer)， 甚至还包含 [一次性创建整个 State 的 “Slice”](https://redux-toolkit.js.org/api/createslice)。



## action

> 强制使用 action 来描述所有变化可以让我们清晰地知道应用中到底发生了什么。如果一些东西改变了，就可以知道为什么变。actions 就像是描述所发生事情的面包屑导航。 最终，我们开发一个函数将 action 和 state 联系起来，这个函数就是 reducer。同样，这也没有使用什么魔法，reducer 只是一个接收 state 和 action作为其参数，并返回给应用新的 state 的函数。





# 参考

* 官网

  https://cn.redux.js.org/introduction/getting-started