# node 默认模块规则

nodejs 默认进行commonjs执行文件，因为 package.json 中 type 字段默认为 `commonjs`，包含使用 ts-node 也是同样的规则。

那么，如果一个第三方包只能使用 esm 的方式呢？

* 也就是第三方包明确要求 `type=module` 在 package.json 文件中，那我们的整个项目就必须使用 esm 的方式进行

  例如：https://github.com/ai/nanoid/blob/main/package.json

* web 是怎么解决的呢？

  需要使用webpack解决



# 怎么样让打出的包技能支持 esm 又能支持 commonjs 呢？

参考：https://juejin.cn/post/7037386586899611684#heading-14

* 主要依赖 package.json 中的 `exports` 字段的配置