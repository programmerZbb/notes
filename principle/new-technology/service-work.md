# 是什么

> Service worker 是一个注册在指定源和路径下的事件驱动[worker](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker)。它采用 JavaScript 控制关联的页面或者网站，拦截并修改访问和资源请求，细粒度地缓存资源。你可以完全控制应用在特定情形（最常见的情形是网络不可用）下的表现。

pwa 的一个应用技术，用来做客户端的缓存。

> 一个服务器与浏览器之间的中间人角色，如果网站中注册了service worker那么它可以拦截当前网站所有的请求，进行判断（需要编写相应的判断程序），如果需要向服务器发起请求的就转给服务器，如果可以直接使用缓存的就直接返回缓存不再转给服务器。从而大大提高浏览体验。

## 特点

* 基于 web worker (会新建一个独立的js线程，一般执行消耗资源的任务)

* 在 web worker 基础上增加了离线缓存能力
* 本质上充当 web 应用程序（服务器）与浏览器之间的代理服务器（可以拦截全站的请求）
* 创建有效的离线体验（将一些不常更新的内容缓存再浏览器）
* 由事件驱动的，具有生命周期
* 可以访问 chche 和 indexDB
* 支持推送
* 并且可以让开发者自己控制管理缓存的内容和版本
* 大量使用 Promise

# 使用

## 1. 注册



## scope

* service worker 只能抓取在 service worker scope 里从客户端发出的请求。
* 最大的 scope 是 service worker 所在的地址
* 如果你的 service worker 被激活在一个有 `Service-Worker-Allowed` header 的客户端，你可以为 service worker 指定一个最大的 scope 的列表。

# 注意

* 不能使用DOM
* 它设计为完全异步，同步API（如XHR和localStorage）不能在service worker中使用
* 出于安全考量，Service workers只能由HTTPS承载
* 在Firefox浏览器的用户隐私模式，Service Worker不可用
* 其生命周期与页面无关（关联页面未关闭时，它也可以退出，没有关联页面时，它也可以启动

> Service worker 运行在 worker 上下文，因此它不能访问 DOM。相对于驱动应用的主 JavaScript 线程，它运行在其他线程中，所以不会造成阻塞。它设计为完全异步，同步 API（如[XHR](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)和[localStorage (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)）不能在 service worker 中使用。
>
> 出于安全考量，Service workers 只能由 HTTPS 承载，毕竟修改网络请求的能力暴露给中间人攻击会非常危险。在 Firefox 浏览器的[用户隐私模式](https://support.mozilla.org/zh-CN/kb/隐私浏览)，Service Worker 不可用。

# 参考

1. https://zhuanlan.zhihu.com/p/115243059
2. https://developer.mozilla.org/zh-CN/docs/Web/API/Service_Worker_API/Using_Service_Workers#%E7%8E%B0%E5%9C%A8%E6%9D%A5%E8%B0%88%E8%B0%88_service_workers
3. https://mp.weixin.qq.com/s/3Ep5pJULvP7WHJvVJNDV-g