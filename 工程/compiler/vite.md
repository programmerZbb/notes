# 简介

来源官网

## 为啥选vite

* 写的真的好啊！！

# 功能

对于普通使用 vite 来说，和普通的http server使用一致。

> 对非常基础的使用来说，使用 Vite 开发和使用一个静态文件服务器并没有太大区别。然而，Vite 还通过原生 ESM 导入提供了许多主要用于打包场景的增强功能。

## 依赖项

### NPM 依赖解析和预构建

1. [预构建](https://cn.vitejs.dev/guide/dep-pre-bundling.html) 它们可以提高页面加载速度，并将 CommonJS / UMD 转换为 ESM 格式。预构建这一步由 [esbuild](http://esbuild.github.io/) 执行，这使得 Vite 的冷启动时间比任何基于 JavaScript 的打包器都要快得多。
2. 重写导入为合法的 URL，例如 `/node_modules/.vite/deps/my-dep.js?v=f3sf2ebd` 以便浏览器能够正确导入它们。

* 文件保存到 `/node_moudles/.vite` 中
* 根据请求后的参数做强缓存控制

### 强缓存

* 依赖项都是强缓存的

#### 怎么实现强缓存的呢？

* 每个静态文件后面都会跟一个参数 `?v=hash`，一旦这个hash不匹配，就代表缓存失效，比如说重新 build，后面的 hash 会变化。

  ==浏览器强缓存，需要完整匹配，只要请求参数变了也不能命中强缓存==

* 缓存信息保存在 `_metadata.json`

## 预构建

* 主要说的是对第三方依赖进行与构建，反向翻译成 ESM 模块。

## 模块热替换

### react

vite 内置了 [React Refresh fast](https://github.com/vitejs/vite-plugin-react/tree/main/packages/plugin-react)，你不需要手动设置这些 —— 当你通过 [`create-vite`](https://cn.vitejs.dev/guide/) 创建应用程序时，所选模板已经为你预先配置了这些。

### typescript

* 仅仅是编译，类型检查交给IDE或者lint。
* Vite 使用 [esbuild](https://github.com/evanw/esbuild) 将 TypeScript 转译到 JavaScript，约是 `tsc` 速度的 20~30 倍，同时 HMR 更新反映到浏览器的时间小于 50ms。

#### 注意点

使用 [仅含类型的导入和导出](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-8.html#type-only-imports-and-export) 形式的语法可以避免潜在的 “仅含类型的导入被不正确打包” 的问题，写法示例如下：

ts

```
import type { T } from 'only/types'
export type { T }
```

### jsx 

`.jsx` 和 `.tsx` 文件同样开箱即用。JSX 的转译同样是通过 [esbuild](https://esbuild.github.io/)。

### css

#### cass 预处理器

* sass 

  需要安装 vite 插件

  ```bash
  $ npm add -D sass
  ```

使用了 dart-sass，速度更快。

替换参考：https://zhuanlan.zhihu.com/p/269296061

### 其他静态资源

参考：https://cn.vitejs.dev/guide/features.html#static-assets

## 构建优化

* 全部是默认的，不需要单独设置

## 按请求编译

* vite 会启用一个编译服务器，对相关的请求进行实时编译。

  但是会预先解析html（parse5），不能直接引入tsx吧？

# 依赖预构建

## 干了啥

* 就是把第三方依赖进行了预构建，采用 esbuild

## 编译&打包

* 使用了 esbuild 做了 transform 和 打包的工作，要不然单个依赖项的请求就不可能是一个了，有可能是很多个！

## 缓存

### 打包缓存

Vite 将预构建的依赖项缓存到 `node_modules/.vite` 中。

### 浏览器缓存

已预构建的依赖请求使用 HTTP 头 `max-age=31536000, immutable` 进行强缓存，以提高开发期间页面重新加载的性能。一旦被缓存，这些请求将永远不会再次访问开发服务器。如果安装了不同版本的依赖项（这反映在包管理器的 lockfile 中），则会通过附加版本查询自动失效。



## 会不会造成生成环境和测试环境生产代码不一致呢？

参考：https://mp.weixin.qq.com/s/ejkfARh6hlOAUnw5Eadb6Q

* 不会，在生产环境使用 esbuild 构建的插件也能兼容生成环境使用 Rollup 构建，使得 transform 的代码是一致的。

# 普通文件构建

## 编译缓存

* vite怎么解决不同编译时，重复组件多个网络请求的问题呢？OK，用户源码一般会进行304协商缓存，如果没有变化的组件是再次请求是非常快的。

* 更新的场景：与预构建文件一致，拼接一个版本参数，使用的是文件修改日期的时间戳，timestamp `.js?t=1234`，只有热更新的场景会这样。



# 对比

## webpack

### 依赖预构建

* external

* dll: (更合适)，dll 其实也是缓存的范畴，需要先进行一次dll构建，然后dll引用。

  > `DllPlugin` 和 `DllReferencePlugin` 用某种方法实现了拆分 bundles，同时还大幅度提升了构建的速度。"DLL" 一词代表微软最初引入的动态链接库。

  dll已经过期了。参考：https://juejin.cn/post/6844903952140468232#heading-3

### 个人思考

* vite 这样是一个方向，就是在测试环境不打包源码（除了第三方依赖），按照需求来进行编译，这样速度确实快，但是会造成请求数量飙升。

  vite怎么解决不同编译时，重复组件多个网络请求的问题呢？OK，用户源码一般会进行304协商缓存，如果没有变化的组件是再次请求是非常快的。

* webpack 会一股脑打包完了，才可以通过web服务器进行访问，这样虽然把所有的打包都约束到一个大包运行时了，能做一些重复合并（splitchunk能力），但是弊端就是会造成冷启动非常臃肿，启动比较慢。

还能不能找到一个折中的方案呢？

* 这也是我追求的目标，不要一股脑打包，首先进行预编译，按需编译。

## turbopack

### 为什么选用它

#### vite 的困境

* 大量并发的网络请求

> Frameworks like Vite use a technique where they don’t bundle application source code in development mode. Instead, they rely on the browser’s native ES Modules system. This approach results in incredibly responsive updates since they only have to transform a single file.
>
> However, Vite can hit scaling issues with large applications made up of many modules. A flood of cascading network requests in the browser can lead to a relatively slow startup time. For the browser, it’s faster if it can receive the code it needs in as few network requests as possible - even on a local server.

#### esbuild 无缓存

* esbuild 本身无缓存
* HMR 问题

> esbuild’s code is hyper-optimized for one task - bundling fast. It doesn’t have HMR, which we don’t want to lose from our dev server.
>
> esbuild is an extremely fast bundler, but it doesn’t do much caching. This means you *will* end up doing the same work again and again, even if that work is at the speed of native.

#### 增量编译和按需打包

* 这个是一个通用优化，其他的打包工具也采用了相同的策略，并不能作为一个独一无二的优化项

#### swc

这个才是核心点。

#### webpack 子集

> Currently, Turbopack supports a subset of webpack's loader API, allowing you to use some webpack loaders to transform code in Turbopack.

* 完全支持webpack的loader api

### 官网和其他工具对比

参考：https://turbo.build/pack/docs/comparisons/vite

### 和vite争议

尤雨溪的回答：https://github.com/yyx990803/vite-vs-next-turbo-hmr/discussions/8

* 说了这么多最慌的webpack，webpack源码中有很多webpack6的todo项。
* vercel 开发人员对vite认知并不充分
* vercel的商业模式，主要是做云托管的，vercel 创业公司，next.js是其核心项目，基于 swc 的快速成功，想急于推广。
* swc 多少有点个人项目的意思。
  * Donny 个人主页：https://github.com/kdy1

### next

Next 并没有完全使用 turbo，而是在官网提示这是一个实验性功能，并且需要使用 `next --turbo` 命令开启。

## webpack 优化项

### 构建性能

https://www.webpackjs.com/guides/build-performance/

#### 不使用生产环境使用的工具

某些 utility, plugin 和 loader 都只用于生产环境。例如，在开发环境下使用 `TerserPlugin` 来 minify(压缩) 和 mangle(混淆破坏) 代码是没有意义的。通常在开发环境下，应该排除以下这些工具：

- `TerserPlugin`
- `[fullhash]`/`[chunkhash]`/`[contenthash]`
- `AggressiveSplittingPlugin`
- `AggressiveMergingPlugin`
- `ModuleConcatenationPlugin`

#### 推荐dev环境不使用splitchunk

> Webpack 通过执行额外的算法任务，来优化输出结果的体积和加载性能。这些优化适用于小型代码库，但是在大型代码库中却非常耗费性能：

### optimization

https://webpack.docschina.org/configuration/optimization/#optimizationsplitchunks

### loader相关

https://webpack.docschina.org/loaders/thread-loader/

#### babel 很慢？

https://webpack.docschina.org/loaders/babel-loader#babel-loader-is-slow

# 项目改造

## 问题记录

### 1. 代码不过范

JS 代码：

css 代码：

### 2. 第三方库



### 3. 浏览器运行问题

