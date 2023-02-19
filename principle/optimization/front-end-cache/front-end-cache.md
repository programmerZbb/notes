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