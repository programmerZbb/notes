# window.requestAnimationFrame(性能优化)

**`window.requestAnimationFrame()`** 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行。

* 搭配 window.cancelAnimationFrame 取消



# 前端路由 hash 模式 & history 模式

* 修改 hash 并且回车，并不会请求后端，只会用于页面定位。会被 window.hashchange 事件监控到。，HashHistory就是利用页面定位实现的不刷新跳转（传统的a标签默认get请求，target为当前tab），本质就是一个浏览器内置的发布订阅（只是能在url上用hash体现出来），改变hash会广播hashchange事件。

  你在JSX里使用Link组件，它本质是继承了原生a标签（接口继承），在onClick handler中将超链接默认事件阻止，避免了页面重定向。

* history 模式，利用 html 5 提供的 history 的 pushState 方法进行路由跳转的，不会刷新浏览器。前进后退能够监听 window.onpopstate ，来实现分发；其他情况的点击，类似于a标签，阻止默认事件后，调用内部的发布订阅系统，进行数据分发。



# readyState 

https://developer.mozilla.org/zh-CN/docs/Web/API/Document/readyState

* 能够使用 onreadyChange 模拟 DOMContentLoaded

## 状态

一个文档的 `**readyState**` 可以是以下之一：

- `loading`（正在加载）

  [`document`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document) 仍在加载。

- `interactive`（可交互）

  文档已被解析，"**正在加载**"状态结束，但是诸如图像，样式表和框架之类的子资源仍在加载。

- `complete`（完成）

  文档和所有子资源已完成加载。表示 `load (en-US)` 状态的事件即将被触发。



# 安全

* xss（跨站脚本攻击）Cross-Site Scripting

  https://juejin.cn/post/6844903685122703367

  方案：输入过滤

* csrf

  CSRF（Cross-site request forgery）跨站请求伪造

  https://juejin.cn/post/6844903689702866952

  * 方案：

    同源检测

  