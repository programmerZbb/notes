# Electron 通信机制

想想一下，把整个 electron 应用想象成一个浏览器，和直接开发前端页面不同的是我们需要自己去开发和维护不同渲染进程直接的调度和通信等。

> 进程间通信 (IPC) 是在 Electron 中构建功能丰富的桌面应用程序的关键部分之一。 由于主进程和渲染器进程在 Electron 的进程模型具有不同的职责，因此 IPC 是执行许多常见任务的唯一方法，例如从 UI 调用原生 API 或从原生菜单触发 Web 内容的更改。

* electron 本身提供了 IPC 通道以便在不同的渲染进程间进行通信（IPC通道是用什么实现的，未知）

## 前置知识——上下文隔离

参考：https://www.electronjs.org/zh/docs/latest/tutorial/context-isolation

electron 不推荐直接在 render 进程中直接使用 electron API，设置了 上下文隔离，而是通过操作 `contextBridge` 的方式通过预加载 js 文件的方式进行 electron api 注入。

## messageChannel

* electron 魔改了 浏览器的 messageChannel，提供了一个 MessageChannelMain 类，可以通过这个类建立进程间通信的通道。

## 1. 单向通信

