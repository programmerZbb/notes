# Webpack 核心原理

从三个部分讲：

1. **构建的核心流程**
2. **loader 的作用**
3. **plugin 架构与常用套路**



## 核心流程

**内容转换 + 资源合并**



## 问题

https://juejin.cn/post/6844904094281236487

1. Loader和Plugin的区别

* loader 相当于一个翻译

  `Loader` 本质就是一个函数，在该函数中对接收到的内容进行转换，返回转换后的结果。 因为 Webpack 只认识 JavaScript，所以 Loader 就成了翻译官，==对其他类型的资源进行转译的预处理工作。==

* plugin 是一段逻辑

  `Plugin` 就是插件，基于事件流框架 `Tapable`，插件可以扩展 Webpack 的功能，在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

> `Loader` 在 module.rules 中配置，作为模块的解析规则，类型为数组。每一项都是一个 Object，内部包含了 test(类型文件)、loader、options (参数)等属性。
>
> `Plugin` 在 plugins 中单独配置，类型为数组，每一项是一个 Plugin 的实例，参数都通过构造函数传入。
>
> 

2. Webpack 的热更新原理吧

   