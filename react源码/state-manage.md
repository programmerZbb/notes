# 说明

本文档是为了整理react状态管理的使用经验，以及讨论react精确渲染的问题。

# mobx

## 有了hooks是否应该选择mobx呢？

### 相关问题

[React有了hooks 还需要redux或mobx么?](https://www.zhihu.com/question/311822358/answer/2633795208)

### 个人理解

* 先说点个人理解吧，完全就是对历史债的tradeoff。

  如果你选择了 mobx ，会不会你其实一开始就不该选择 React？

  mobx 本身会极大的减轻程序员开发成本，降低心智负担。

  > 自动定位变更，确定渲染次数，Vue3 的 proxy 是否更合适？
  >
  > Angular 的事件驱动变更检测，完全无感的调度控制是不是更加”新潮“？
  >
  > 当你都已经需要这些功能支持的时候，在 React 体系下，是不是有些缘木求鱼了？我们在选用 React 的时候，不是为了jsx的视图编辑能力？不是为了控制每一处渲染次数的精确控制能力？不是为了其函数优先的封装形式和少即是多的开发思想？你确定你在采用了 mobx 之后，这些优势还在么？
  >
  > 同样的功能，完全不考虑状态变化的 Angular $[stream](https://www.zhihu.com/search?q=stream&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2633795208}) 和 $stream | async ，又或者 Vue 的同 mobx 但更加简洁的用法，会不会更让你心动？
  >
  > 把 React 改造成更加繁杂的 Vue 并不是聪明的做法
  >
  > 当然，你也可能会有更多考量的因素，不过，你总是需要有真正的原因去选择 mobx，在选择 mobx 的时候，也应该是因为其收益大过了其损失的成本
  >
  > 而不是因为有人说：你应该使用 mobx，你就直接去使用了
  >
  > * 知乎：https://www.zhihu.com/question/311822358/answer/2633795208

* 如上，让react项目变成一个更复杂的vue项目，是否值得呢？在我看来所有react需要使用 useMemo 来控制精确渲染的react项目，都是因为组件拆分不合理，为什么不让state的变化只限于子组件呢？毕竟子组件状态更新并不会影响父组件。
* mobx + react 约等于 更复杂的vue，为啥不直接使用vue呢？

### tradoff

技术没有好坏，选择一个技术本身就是一种妥协。

* 选用mobx，能够提升开发人员效率，快速迭代项目，性能损耗可以接受（不使用状态管理，可能有更大的损耗，取决于开发人员的素质）。
* 缺点：引入了一个复杂的库，运行时需要常驻一个发布订阅系统，react如果规划合理，完全能够性能更高（但是程序员的心智负担太大，效率较低）

## mobx 实现原理，以及与redux比较

MobX 是怎么实现的？ - 神说要有光的文章 - 知乎 https://zhuanlan.zhihu.com/p/494853306



# redux、mobx、rxjs

* redux、mobx、rxjs这三款数据流管理工具在你项目中是如何取舍的？ - 工业聚的回答 - 知乎 https://www.zhihu.com/question/277530559/answer/394209409