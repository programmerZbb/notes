# Webpack 核心原理

从三个部分讲：

1. **构建的核心流程**
2. **loader 的作用**
3. **plugin 架构与常用套路**



## 核心流程

**内容转换 + 资源合并**



## 最大的优势

* 引入了 JavaScript ，支持动态创建标签，之前的打包工具是移动。



## loader

* loader 用于对模块的源代码进行转换，将非 js 转换为 webpack 能识别的js模块。
* 本质上，webpack loader 将所有类型的文件，转换为应用程序的 依赖图 可以直接引用的模块。
* 在 module rules 中配置

```js
module.exports = {
  module: {
    rules: [
      { test: /\.css$/, use: 'css-loader' },
      { test: /\.ts$/, use: 'ts-loader' },
    ],
  },
};
```



## plugin

* 插件目的在于解决 [loader](https://webpack.docschina.org/concepts/loaders) 无法实现的**其他事**。Webpack 提供很多开箱即用的 [插件](https://webpack.docschina.org/plugins/)。
* webpack **插件**是一个具有 [`apply`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)aScript 对象。`apply` 方法会被 webpack compiler 调用，并且在 **整个** 编译生命周期都可以访问 compiler 对象。



扩展插件，webpack运行各个阶段，都会广播出对应的事件，插件去==监听对应的事件==。（开闭原则）



## compiler

对象，包含了webpack环境的所有配置信息，包含了 option，loader，plugins

Webpack 启动的时候实例化，它在全局是唯一的，可以把他理解为 webpack 实例。



## compliation

包含了当前的模块资源，编译生成资源。

webpack 在开发模式下运行的时候，每当检测到一个文件变化，就会创建一次新的 compliation



## resolve

* 这些选项能==设置模块如何被解析==。webpack 提供==合理的默认值==，但是还是可能会==修改一些解析的细节==。关于 resolver 具体如何工作的更多解释说明，请查看[模块解析](https://webpack.docschina.org/concepts/module-resolution)。

### alias

* 别名

### extensions

* 尝试按顺序解析这些后缀名。如果有多个文件有相同的名字，但后缀名不同，webpack 会解析列在数组首位的后缀的文件 并跳过其余的后缀。

### modules

* 告诉 webpack 解析模块时应该搜索的目录。常用 node 的 path 模块去解析，path.resolve(__dirname, 'src')

```js
const path = require('path');

module.exports = {
  //...
  resolve: {
    modules: [path.resolve(__dirname, 'src'), 'node_modules'],
  },
};

```





### 原理

webpack **插件**是一个具有 [`apply`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)avaScript 对象。`apply` 方法会被 webpack compiler 调用，并且在 **整个** 编译生命周期都可以访问 compiler 对象。



## optimization

* 有一些压缩配置
* splitChunks 是否异步、大小、模块共享、最大异步请求数量。



## tree shaking

* 移除代码中未被引用的代码
* mode: production 时启用。

### 将文件标记为 side-effect-free

* 通过 package.json 的 `"sideEffects"` 属性，来实现这种方式。

  ```json
  {
    "name": "your-project",
    "sideEffects": false
  }
  // 下面这些文件不会被删除
  {
    "name": "your-project",
    "sideEffects": ["./src/some-side-effectful-file.js"]
  }
  ```

### 将函数调用标记为无副作用

```javascript
/*#__PURE__*/ double(55);
```



## 问题

https://juejin.cn/post/6844904094281236487

1. Loader和Plugin的区别

* loader 相当于一个翻译

  `Loader` 本质就是一个函数，在该函数中对接收到的内容进行转换，返回转换后的结果。 因为 Webpack 只认识 JavaScript，所以 Loader 就成了翻译官，==对其他类型的资源进行转译的预处理工作。==

* plugin 是一段逻辑

  `Plugin` 就是插件，基于事件流框架 `Tapable`，插件可以扩展 Webpack 的功能，在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

> `Loader` 在 module.rules 中配置，作为模块的解析规则，类型为数组。每一项都是一个 Object，内部包含了 test(类型文件)、loader、options (参数)等属性。
>
> `Plugin` 在 plugins 中单独配置，类型为数组，每一项是一个 Plugin 的实例，参数都通过构造函数传入。
>
> 

2. Webpack 的热更新原理吧



### 3. 简单描述一下webpack打包过程

1. 初始化参数 shell webpack.config.js 
2. 开始编译：初始化一个 compiler 对象，加载所有的配置，开始执行编译
3. 确定入口：根据entry 中的配置，找出所有的入口文件
4. 编译模块：从入口文件开始，调用所有的loader，再去递归找（图的遍历）
5. 完成模块编译：得到每个模块被翻译后的最终内容以及他们之间的依赖关系。
6. 输出资源：根据得到的依赖关系，组装成一个一个包含多个 module 的chunk
7. 输出完成：根据配置，确定要输出的文件名和文件路径。



# git 相关

* 使用 git hook 对代码进行检查
* 相关工具：husky。 参考：https://zhuanlan.zhihu.com/p/366786798
  * pre-commit： https://www.jianshu.com/p/072a96633479

在 package.json 中定义hook

```js
{
  "pre-commit": [
    "tslint:prod" // pre-commit 工具
  ],
   "husky": {
    "hooks": {
      "commit": "git-cz ", // 在commit之前先执行
      "commit-msg": "validate-commit-msg" // 校验commit时添加的备注信息是否符合我们要求的规范。工具 validate-commit-msg
    }
  },
  "config": {
    "validate-commit-msg": {
      "types": [
        "feat",
        "fix",
        "docs",
        "style",
        "refactor",
        "perf",
        "test",
        "build",
        "ci",
        "chore",
        "revert"
      ],
      "scope": {
        "required": false,
        "allowed": [
          "*"
        ],
        "validate": false,
        "multiple": false
      },
      "warnOnFail": false,
      "maxSubjectLength": 100,
      "subjectPattern": ".+",
      "subjectPatternErrorMsg": "subject does not match subject pattern!",
      "helpMessage": "",
      "autoFix": false
    }
  }
}
```



# typescript

## tslint 相关

```js
{
  "build": "npm run tslint:prod && npm run dist"  // 构建之前先检查ts
}
```

## ts 编译

* 一般采用 babel 去编译

  `@babel/preset-typescript`

* 或者使用 ts-loader 去编译



# babel

## babel-loader

https://segmentfault.com/a/1190000017898866?utm_source=tag-newest

* 默认会读取根目录下 `.babelrc` 文件的配置
* 还可以配置一些按需引入 antd 和 element-ui





# 样式相关

## less-loader

* 使用之后能够开启 css modules 语法

  * 全局

    ```less
    :global {
      .global-class-name {
        color: green;
      }
    }
    ```

  * Webpack 配置

    ```js
    {
      test: /\.less$/,
        use: [
          styleLoader,
          {
            loader: 'css-loader',
            options: {
              // minimize: true,
              modules: {
                localIdentName: '[name]__[local]___[hash:base64:5]',
              },
              importLoaders: 1,
            },
          },
          {
            loader: 'less-loader',
            options: {
              javascriptEnabled: true,
            },
          },
        ],
          include: [
            path.resolve(ENV.ROOT_PATH, 'src'),
          ],
    },
      ],
    ```

    



# webpack 构建性能

https://webpack.docschina.org/guides/build-performance/#multiple-compilations

## 通用环境方面

### 1. 更新到最新版本

* 更新 nodejs

* 更新 npm

* 更新 yarn

  较新的版本能够建立更高效的模块树以及提高解析速度。

### 2. loader

* 将 loader 应用于最少数量的必要模块

* 使用 include 字段实现精确转换

  ```js
  const path = require('path');
  
  module.exports = {
    //...
    module: {
      rules: [
        {
          test: /\.js$/,
          include: path.resolve(__dirname, 'src'),
          loader: 'babel-loader',
        },
      ],
    },
  };
  ```



### 3. 引导

* 每个额外的 loader/plugin 都有其启动时间。==尽量少地使用工具。==



### 4. 解析

* 减少 `resolve.modules`, `resolve.extensions`, `resolve.mainFiles`, `resolve.descriptionFiles` 中条目数量，因为他们会增加文件系统调用的次数。



### 5. dll

* 使用 `DllPlugin` 为更改不频繁的代码生成单独的编译结果。这可以提高应用程序的编译速度，尽管它增加了构建过程的复杂度。

* `DllPlugin` 和 `DllReferencePlugin` 用某种方法实现了拆分 bundles，同时还大幅度提升了构建的速度。"DLL" 一词代表微软最初引入的动态链接库。

  DllPlugin 插件



### 6. 小即是快(smaller = faster)

减少编译结果的整体大小，以提高构建性能。尽量保持 chunk 体积小。

- 使用数量更少/体积更小的 library。
- 在多页面应用程序中使用 `SplitChunksPlugin`。
- 在多页面应用程序中使用 `SplitChunksPlugin `，并开启 `async` 模式。
- 移除未引用代码。
- 只编译你当前正在开发的那些代码。



### 7. Worker 池(worker pool)

`thread-loader` 可以将非常消耗资源的 loader 分流给一个 worker pool。

> ###### Warning
>
> 不要使用太多的 worker，因为 Node.js 的 runtime 和 loader 都有启动开销。最小化 worker 和 main process(主进程) 之间的模块传输。进程间通讯(IPC, inter process communication)是非常消耗资源的。

#### thread-loader

使用时，需将此 loader 放置在其他 loader 之前。放置在此 loader 之后的 loader 会在一个独立的 worker 池中运行。

请仅在耗时的操作中使用此 loader！



#### HappyPack

* 使用 HappyPack 能够打开新的线程。

  webpack构建过程中的有两个部分是直接影响构建效率的，一个是文件的编译，另一个则是文件的分类打包。相较之下==文件的编译更为耗时(loader部分)，直接在 loader 处理的时候进行多线程处理==

  https://zhuanlan.zhihu.com/p/59328293

```js
const HappyPack = require('happypack')
const os = require('os')
const happyThreadPool = HappyPack.ThreadPool({size: os.cpus().length})

module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        // 将.js文件交给id为happyBabel的happypack实例来执行
        loader: 'happypack/loader?id=happyBabel',
        exclude: /node_modules/
      }
    ]
  },
  plugins: [
    new HappyPack({
      // id标识happypack处理那一类文件
      id: 'happyBabel',
      // 配置loader
      loaders: [{
        loader: 'babel-loader?cacheDirectory=true'
      }],
      // 共享进程池
      threadPool: happyThreadPool,
      // 日志输出
      verbose: true
    })
  ]
}

```





### 8. 持久化缓存

* 在 webpack  配置中使用 cache 选项。



### 9. 输出结果不携带路径信息

Webpack 会在输出的 bundle 中生成路径信息。然而，在打包数千个模块的项目中，这会导致造成垃圾回收性能压力。在 `options.output.pathinfo` 设置中关闭：

```js
module.exports = {
  // ...
  output: {
    pathinfo: false,
  },
};
```



## 开发环境

### 1. 增量编译?

使用 webpack 的 watch mode(监听模式)。而不使用其他工具来 watch 文件和调用 webpack 。内置的 watch mode 会记录时间戳并将此信息传递给 compilation 以使缓存失效。

```javascript
module.exports = {
  //...
  watch: true,
};
```

### 2. 在内存中编译

下面几个工具通过在内存中（而不是写入磁盘）编译和 serve 资源来提高性能：

- `webpack-dev-server`
- `webpack-hot-middleware`
- `webpack-dev-middleware`





## 重要的点

* cache
* 打包到内存中
* 增量编译
* 多线程
* 按需加载，代码分割



# 项目中实践

## 1. proxy 开发环境反向代理

* 使用的是 express

  * 本地服务的转发，webpack devserver 中配置 proxy

    ```js
    {
      proxy: {
        '/api': {
          target: '',
          {}
        }
      }
    }
    ```

    

  * 中间件使用的是 http-proxy-middleware

    具体使用方法：

    ```js
    app.use('/api/v1/control/', proxy({
        target: staticData.targetOrigin3, // target host
        // target: 'http://172.18.92.103:8080',
        // target: 'https://mock.corp.kuaishou.com/mock/3195',
        changeOrigin: true, // needed for virtual hosted sites
        onProxyReq: (proxyReq, req, res) => {
            // add custom header to request: 用于本地测试绕过 SSOs
            // proxyReq.setHeader('userName', 'zhangsan');
            // or log the req
            proxyReq.setHeader('Cookie', [`serveree_auth_token=${ cookieAtlasValue };`, `accessproxy_session=${ accessproxy_session };`]);
            // console.log('proxyReq', proxyReq);
        },
    }));
    ```



## 2. typescript 检查

* 脚本： npm run dev: `npm run tslint:dev && npm start`

  -p 指定配置文件 `"tslint:dev": "tslint -p . -c chore/config/tslint.dev.json"`

  webpack 的指定配置文件用的是 --config 指定文件



## 其他优化手段

### 1. HappyPack

```js
{
  rules: {
    	test: /\.js$/,
      // 将对.js文件的处理转交给id为babel的HappyPack的实列
      use: ['happypack/loader?id=babel'],
        // loader: 'babel-loader',
      exclude: path.resolve(__dirname, 'node_modules') // 排除文件
  }
}

const HappyPack =require (’happypack’);
// 构造出共享进程池，在进程池中包含 5 个子进程
const happyThreadPool = HappyPack.ThreadPool({ size : 5 )) ;

module.exports = {
  plugins: [
    new HappyPack({
      // 用唯一的标识符id来代表当前的HappyPack 处理一类特定的文件
      id: 'babel',
      // 如何处理.js文件，用法和Loader配置是一样的
      loaders: ['babel-loader'],
      // 使用共享进程池中的子进程去处理任务。还可以共享线程池。
      threadPool: happyThreadPool
    })
  ]
};
```



### 2. url-loader

* 限制图片大小，小于某个值的会被压缩成 base 64 位的

  ```js
  {
      test: /\.(png|jpg|jpeg|gif)$/,
      use: {
        loader: 'url-loader',
          options: {
            limit: 1024 * 100,
      },
      },
  }
  ```

  





## 默认优化

* mode： 代码压缩



