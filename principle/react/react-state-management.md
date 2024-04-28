# redux

## 个人心得

* redux 老说自己 `no magic`，是通过一些概念强约束来达到目的的，比如说不可变数据流，action、reducer等
* redux 相比其他的状态管理库来说还是太复杂了



# zustand

* 使用简单，能够监听外部数据的修改

  参考https://docs.pmnd.rs/zustand/getting-started/introduction

## 对比

* 更简洁的API，不需要 provider 了
* 可以直接写到组件外层



# jotai

参考：https://juejin.cn/post/7356813407373328418

## 思想

* 源于人们不懈的对前端状态拆分的需求，比如说 context，对 context 进行拆分，这也是一个需求。

## 有什么不同

* 原子化状态，更像是一个更自由的 mobox
* 不需要 provider，直接写到组件外层即可
* 对比zustand: 原子化（atom），对于开发者来说比较友好，能够取最大公约数，避免集中的store导致页面重复渲染问题。



# immer

这个可缺不了，任何状态管理库都需要返回一个新状态，新状态的生成需要对原状态的复制，使用了 immer 之后就能简单的直接对原状态进行修改了，剩下的交给 immer 来做吧。

* 可以结合任何库
