* 此文档主要是由mdn上关于web性能介绍而来：

  参考：https://developer.mozilla.org/zh-CN/docs/Web/Performance

# 前言

## 重要性

不多说了

## 指标

### Chrome lighthouse 指标

参考：https://juejin.cn/post/7220230543005253691#heading-3

| 指标                       | 说明                                                         |
| -------------------------- | ------------------------------------------------------------ |
| 性能指标（Performance）    | 页面的性能评分，包括：首次内容绘制（First Contentful Paint）、首次有效绘制（First Meaningful Paint）、首次 CPU 空闲（First CPU Idle）、可交互时间（Time to Interactive）、速度指标（Speed Index）、输入延迟估值（Estimated Input Latency）。 |
| 可访问性（Accessibility）  | 监测页面的可访问性与优化建议。                               |
| 最佳实践（Best Practice）  | 页面是否符合最佳实践。                                       |
| 搜索引擎优化（SEO）        | 页面是否针对搜索引擎结果排名进行了优化。                     |
| PWA（Progressive Web App） | 验证 PWA 的各个方面的性能情况。                              |

## 主线程

> **主线程**用于浏览器处理用户事件和页面绘制等。默认情况下，浏览器在一个线程中运行一个页面中的所有 JavaScript 脚本，以及呈现布局，回流，和垃圾回收。这意味着一个长时间运行的 JavaScript 会阻塞线程，导致页面无法响应，造成不佳的用户体验。

* 主线程用于处理浏览器用户事件（主要是回调）和页面绘制等，也就是我们常说的单线程

  * 包含运行一个页面所有的js脚本
  * 布局和回流（重排和重绘）
  * 垃圾回收

  这也就是我们在 Chrome perfomance 中看到的执行流程，完完全全是一个线性的流程。



# 概念

## FPS

*frames per second* 每秒渲染的帧，可评估当前页面渲染的压力，一般情况期望能够达到 60fps。

FPS 越小体验越差，若小于30，一般人都能感觉到动画的卡顿现象。

* 达到30就能看到明显的卡顿

## longtask

performanceObserver 能够监听到长任务的执行

```typescript
const observer = new PerformanceObserver((list) => {
  score -= list.length * 10

  if (score < 0) score = 0
});

observer.observe({ type: "longtask", buffered: true });
```

### longtask vs fps

1. LongTask 相对来说没有 FPS 灵敏
   - 主线程、UI线程阻塞超过 50ms 触发 LongTask，FPS 一般来说期望 16.6ms 一帧
2. FPS 在页面处于后台时不可用，无法统计到卡顿事件
   - FPS 原理是使用 `requestAnimationFrame` 计数，页面处于后台时该函数不会执行
3. FPS 计数本身有一定的性能损耗
   - 可用以下最简化代码在空白标签页的控制台中验证 CPU 消耗



# 相关指标和其测量工具

## 参考

参考：https://web.dev/articles/rail?hl=zh-cn#chrome-devtools

介绍了各种指标和工具的使用。

## 指标

参考：https://juejin.cn/post/7253828299939184697

## TTI

TTI 是一种非标准化的 web 性能进度指标。定义是最后一个长任务完成且随后有5秒网络和主线程不活动的时间点。

* time to interactive，一般用来表示进行一个长任务之后的阻塞时长。

> TTI 由 Web 孵化器社区组于 2018 年提出，旨在提供一种指标，描述页面或应用程序何时包含有用内容，并且主线程处于空闲状态并可以自由响应用户交互，包括注册事件处理程序。

* 主要基于主线程的空闲时间

TTI 并不是标准的web性能指标，是利用 [long task api](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceLongTaskTiming) 的信息而派生出来的。

* 注：这个指标已经被 FID 取代了

## RAIL模型

[使用 RAIL 模型衡量性能](https://web.dev/rail/#response)

>**RAIL** 是一种以**用户为中心**的性能模型，它提供了一种考虑性能的结构。该模型将用户体验分解到按键操作（例如，点击、滚动、加载）中，帮助您为每个操作定义性能目标。

* 以用户为中心的性能模型。

RAIL 分为四个部分：

* response：50ms响应处理时间
* Animation：10ms生成一帧，60fps
* idle: 尽可能长的空闲时间
* Loading：5s 内实现互动。

RAIL 代表 Web 应用生命周期的四个不同方面：响应、动画、空闲和加载。用户对这些上下文分别有不同的性能期望，因此，性能目标是根据上下文以及[用户如何感知延迟的用户体验研究](https://www.nngroup.com/articles/response-times-3-important-limits/)来定义的。



## lighthouse 工具

* lighthouse 就是按照 RAIL 标准（最佳实践）对当前页面性能进行测量的工具，提供了一种最佳实践。如果你的业务不认可这种最佳实践，可以用 performance进行自定义测量。



## memory 工具 —— 解决内存问题

参考：https://developer.chrome.com/docs/devtools/memory-problems?hl=zh-cn



# web vitals 

> “网页指标”是 Google 的一项计划，旨在针对网页质量信号提供统一指南，这些信号对于提供出色的网页用户体验至关重要。它的目标是简化各种可用的性能测量工具，并帮助网站所有者专注于最重要的指标，即**核心网页指标**。

* google 提供的一些列指标，相比 RAIL模型 更具象一些。

* 也就是核心网页指标，这其中还有 3 个核心的，叫做 web core vitals。

## core vitals

从一系列指标中选出了三个核心指标：LCP、FID、CLS，分别衡量加载性能、交互性能、视觉稳定性。

### LCP

Largest Contentful Paint，最大的内容（文字/图片）渲染的时间。

计算方式是从网页开始渲染到渲染完成，每次渲染内容都对比下大小，如果是更大的内容，就更新下 LCP 的值

* 主要衡量加载性能

### FMP

加一个FMP，主要用来衡量首页渲染性能的

### 首屏时间计算

FMP（First Meaningful Paint）是指主要内容出现在屏幕上所需的时间。

参考：https://juejin.cn/post/6950930422763094029?from=search-suggest

自己实现的一种通用的 FMP 测量工具，代码参考：calculate-FMP.ts

有个和我类似的实现参考：https://chenng.cn/posts/MutationObserver%E8%AE%A1%E7%AE%97FMP/

### 其他指标的计算

Chrome 提供了一个库：`web-vitals` 能够用来测量对应的标准指标。
