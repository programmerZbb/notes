# 参考

参考编写可维护的JavaScript书籍，查阅资料有感。

参考：

https://juejin.cn/post/6844903619385360397

# 编码规范

* 本章主要是从编码格式规范和注意事项方面提供思路

## UI层的松耦合

松耦合的原则：修改一个组件时尽可能不需要修改其他组件代码。

- 将JavaScript从CSS中抽离 避免使用[CSS表达式](https://link.juejin.cn?target=https%3A%2F%2Fblog.csdn.net%2Faidenliu%2Farticle%2Fdetails%2F5020018)
- 将CSS从JavaScript中抽离 避免直接修改dom的样式，通过类名去实现想要的效果。

```ini
// bad
element.style.cssText = "color: red; left: 10px";

// good
.reveal {
    color: red;
    left: 10px;
}

element.className += " reveal";
复制代码
```

- 将JavaScript从HTML中抽离 避免直接在HTML写事件处理程序和逻辑代码

主要思路是把js、html、css代码拆分开，UI组件功能单一