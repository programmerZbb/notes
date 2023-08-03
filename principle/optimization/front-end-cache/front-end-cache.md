# 深入理解浏览器的缓存机制

## 前言

源自：https://www.jianshu.com/p/54cc04190252

缓存可以说是性能优化中简单高效的一种优化方式了

* 缩短网络延迟，减少延迟
* 减少宽带，降低网络负荷

把一个网络请求分为：网络请求、后端处理、浏览器响应三个步骤

浏览器缓存可以在第一和第三步骤中优化性能。浏览器缓存可以帮助我们在第一和第三步骤中优化性能。比如说直接使用缓存而不发起请求，或者发起了请求但后端存储的数据和前端一致，那么就没有必要再将数据回传回来，这样就减少了响应数据。

![img](https://user-images.githubusercontent.com/6149026/84606445-dfda0e00-aed8-11ea-8a84-fcdd960c001e.png)

## 缓存位置

从缓存的位置来说分为四种，并且存在优先级，当依次查找没有命中的时候，才会去请求网络。

缓存查找顺序：Service Worker-->Memory Cache-->Disk Cache-->Push Cache

- Service Worker
- Memory Cache
- Disk Cache
- Push Cache

### 1. service worker - 可设置

> Service Worker 是运行在浏览器背后的独立线程，一般可以用来实现缓存功能。使用 Service Worker的话，传输协议必须为 HTTPS。因为 Service Worker 中涉及到请求拦截，所以必须使用 HTTPS 协议来保障安全。**Service Worker 的缓存与浏览器其他内建的缓存机制不同，它可以让我们自由控制缓存哪些文件、如何匹配缓存、如何读取缓存，并且缓存是持续性的**。

* 浏览器的独立线程，必须使用https，能够靠编码的方式控制缓存哪些文件。

### 2. memory cache

> Memory Cache 也就是内存中的缓存，主要包含的是当前中页面中已经抓取到的资源,例如页面上已经下载的样式、脚本、图片等。读取内存中的数据肯定比磁盘快,内存缓存虽然读取高效，可是缓存持续性很短，会随着进程的释放而释放。 **一旦我们关闭 Tab 页面，内存中的缓存也就被释放了**。

* memory chache 随着进程的回收而释放。缓存一些下载的样式、脚本、图片等资源。

内存缓存中有一块重要的缓存资源是preloader相关指令（例如`<link rel="prefetch">`）下载的资源。总所周知preloader的相关指令已经是页面优化的常见手段之一，它可以一边解析js/css文件，一边网络请求下一个资源。

* 看起来像图片这些大的资源，一般会保存在memory中，属于是浏览器行为。

### 3. Disk cache - 可设置

> 在所有浏览器缓存中，Disk Cache 覆盖面基本是最大的。它会根据 HTTP Herder 中的字段判断哪些资源需要缓存，哪些资源可以不请求直接使用，哪些资源已经过期需要重新请求。并且即使在跨站点的情况下，相同地址的资源一旦被硬盘缓存下来，就不会再次去请求数据。绝大部分的缓存都来自 Disk Cache，关于 HTTP 的协议头中的缓存字段，我们会在下文进行详细介绍。

#### **浏览器会把哪些文件丢进内存中？哪些丢进硬盘中？**
关于这点，网上说法不一，不过以下观点比较靠得住：

- 对于大文件来说，大概率是不存储在内存中的，反之优先
- 当前系统内存使用率高的话，文件优先存储进硬盘

### 4.Push Cache

Http2 新功能，支持面不广。

### 总结

memory cache 是浏览器为了加快读取缓存速度而进行的自身的优化行为，不受开发者控制，也不受 HTTP 协议头的约束，算是一个黑盒。Service Worker 是由开发者编写的额外的脚本，且缓存位置独立，出现也较晚，使用还不算太广泛。所以我们平时最为熟悉的其实是 disk cache，也叫 HTTP cache (因为不像 memory cache，它遵守 HTTP 协议头中的字段)。平时所说的强制缓存，对比缓存，以及 `Cache-Control` 等，也都归于此类。

## http 缓存

* 参考 http 协议文档中 http 缓存



# 前端缓存

* 已 Chrome 为例

  Chrome 在许多抽象的层级上实现了缓存。核心是 http 缓存

普遍的缓存包括

* http cache

* Service worker caches

  浏览器显示 from service worker

* Blink cache

## from memory cache 和 disk cache

* 如果命中强缓存后，当前页面的静态资源（比如 script，或者css等，fetch也是一样的）发送的请求会返回200 code，然后size 显示disk cache。但是首页的直接同步访问的请求不能直接请求cache。

  这个完全是浏览器的缓存实现，不需要开发者手动控制

### 有什么不同

Webkit 资源分类主要分为两大类：主资源和派生资源

比如：如果命中强缓存，同一个图片如果使用 img src 的方式请求则命中 memory chache；如果使用 fetch 请求，则命中 disk cache。

* 图片一般都是 memory cache
* js 和 css 文件一般都是 disk cache

## 三级缓存

1. 先去内存看，如果有，直接加载
2. 如果内存没有，择取硬盘获取，如果有直接加载
3. 如果硬盘也没有，那么就进行网络请求
4. 加载到的资源缓存到硬盘和内存

## 实验

* 使用 express 的 static 进行实验

```typescript
// static
// 会把指定目录下所有的文件放到根目录下代理
// app.use(express.static('static'))
// 也可以加路由
app.use('/static', (req, res, next) => {
    // res.setHeader('cache-control', 'max-age=28800,must-revalidate');
    res.setHeader('date', 'Thu, 01 Dec 2022 14:39:00 GMT');
    res.setHeader('last-modified', 'Mon, 21 Nov 2022 05:53:09 GMT');
    next()
}, express.static('static', {
    dotfiles: 'ignore', // 忽略以 . 开头的文件
}))
```



# 参考

* https://stackoverflow.com/questions/44596937/chrome-memory-cache-vs-disk-cache
* webkit 缓存机制——https://www.zhihu.com/search?hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%22217831630%22%7D&hybrid_search_source=Entity&q=webkit%E7%BC%93%E5%AD%98%E6%9C%BA%E5%88%B6&search_source=Entity&type=content
* https://segmentfault.com/a/1190000011286027
* https://excaliburhan.com/post/things-you-should-know-about-browser-cache.html
* https://www.zhihu.com/search?type=content&q=from+memory+cache