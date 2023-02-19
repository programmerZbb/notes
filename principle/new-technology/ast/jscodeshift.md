# jscodeshift

`jscodeshift` 是一个工具包，用于在多个 `JavaScript` 或 `TypeScript` 文件上运行 codemods，它是：

- 一个运行器，它为传递给它的每个文件执行提供的转换。它还输出已（未）转换的文件数量统计信息；
- [recast](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fbenjamn%2Frecast) 的包装器，提供不同的 `API`。 `recast` 是一个 `AST` 到 `AST` 的尽量**保留原始代码的风格**转换工具。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/af5c5701080b426398d58f212c4ce7db~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)



## codemod

[codemod](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Ffacebookarchive%2Fcodemod) 是一个可用于**大规模重构**的**部分自动化** python 工具

React 官方、vue官方、ant-design 都提供了官方的 codemod 工具

* react codemod

* Vue codemod

* Ant-design codemod，提供一个工具快速升级 antd 版本。

  https://github.com/ant-design/codemod-v4

## recast

* 一个代码重构工具
* ast转换

参考：https://juejin.cn/post/6844903910960791566#heading-16

### 特点

* 最牛逼的特点：原代码格式输出

  如果 recast 无法还原代码格式，它会重新对代码进行格式化，因此最糟糕的就是重新格式化代码

### ast api

* 使用的ast工具是 ast-types。api需要参考这个库！

### 快速ast使用

* 参考：https://astexplorer.net/

  选中 recast

### SpiderMonkey

* 用c语言实现的 JavaScript 脚本引擎

### recast 保存代码时风格

https://github.com/benjamn/recast/blob/v0.20.4/lib/options.ts

## jscodeshift

jscodeshift 将 babel parser（ast 解析引擎）、[ast-types](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fbenjamn%2Fast-types)（用于快速创建新的 AST 节点，主要是提供了一些api能通过type的形式操作ast）和 [recast](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fbenjamn%2Frecast%2F)（维护生成代码的代码风格信息）三大工具整合在一起。

> jscodeshift 是一个基于 codemod 理念的 JavaScript/TypeScript 重构工具，其原理是将 JS/TS 代码解析为抽象语法树（Abstract Syntax Tree，AST），并提供一系列用于访问和修改 AST 的 API 以实现自动化的代码重构。jscodeshift 将 babel parser、[ast-types](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fbenjamn%2Fast-types)（用于快速创建新的 AST 节点）和 [recast](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fbenjamn%2Frecast%2F)（维护生成代码的代码风格信息）三大工具整合在一起，提供了简便快捷的操作接口；同时它还提供了多任务并行执行的功能，使其对于海量代码文件的重构操作可以并行运行，充分利用多核 CPU 算力，缩短重构任务执行时间。

* https://juejin.cn/post/6934911685220106253

### transform



### collection

> 鉴于 AST 的数据结构特征，一般的语法解析器 (parser) 以及 recast 提供的 API 都基于访问者模式来对 AST 进行遍历 (traversal)。jscodeshift [基于 Collection 的概念对 API 进行了进一步封装](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Ffacebook%2Fjscodeshift%23collections-and-traversal)，令使用者对 AST 节点的筛选及修改操作变得更加简单。

* 一般的 ast 解析器都提供一个 visitor



* collection 操作像jq一样链式调用，就是 jscodeshift 提供的一些查找、操作ast的api

​	https://github.com/facebook/jscodeshift/blob/main/src/Collection.js

基于 collection 对 recast api进行的转换

```typescript
// recast
var ast = recast.parse(src);
recast.visit(ast, {
  visitIdentifier: function(path) {
    // do something with path
    return false;
  }
});

// jscodeshift
jscodeshift(src)
  .find(jscodeshift.Identifier)
  .forEach(function(path) {
    // do something with path
  });
```



### Builder —— 创造节点

两一个重要的概念是`Builder`, 即ast构造器，有时我们不只要修改节点，还要创造节点，比如自动导入`foo`模块，也即是要在代码里插入：

```javascript
import foo from 'foo';
复制代码
```

此时就要构造这个节点

```js
j.importDeclaration(
  [j.importDefaultDeclaration(j.identifier('foo'))],
  j.literal('foo')
);
```





# 参考

* https://juejin.cn/post/6986078453917302815
* https://juejin.cn/post/6844904195624009736——这一篇写的不错