# 操作ast？

* 一般情况下都需要结合一个 ast viewer 来实现

  ts-compiler 的 viewer: https://ts-ast-viewer.com/#code/N4dwlgdgJg9iB0AXApgZ0QCgOQvVglAL5A

  jscodeshift 的 viewer(也是通用的viewer)：https://astexplorer.net/

  * 选 recast -> jscodeshift，jscodeshift 编译 tsx 依赖于 recast 来实现行列的保持。

## 遍历节点

* 一般场景下，都需要提供一个 visitor 来实现节点的遍历。
  * ts、babel 都需要写一个 visitor 来实现节点的遍历
  * jscodeshift 提供了 find 函数，能够直接通过查找对应的节点类型来定位到该节点。
* 都是根据 parent 节点类型来判断当前类型的节点是否需要操作的。

## 归类

* acorn: webpack、Rollup等背后的ast解析工具
* Babylon：babel编译工具，基于 acorn
* Recast: 能保持精确行输出的编译工具
* Jscodeshfit: 更像是一个解决方案，编译 tsx 基于 recast。
* eslint: 提供了一个 util，暴漏了一个 `ESLintUtils.RuleCreator`，这个RuleCreator也是通过遍历节点来操作的。



# 思考&探索

* 前端当前阶段就处于编译探索阶段
  * 卷更快的编译器
  * 框架层面：在编译阶段做更多的事，比如说 react forget、tailwind、solidjs等。