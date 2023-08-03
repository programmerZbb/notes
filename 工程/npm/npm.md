# npm

## 官方网站

参考：[npm中文网](https://nodejs.cn/npm/cli/v7/commands/npm-dedupe/)

## npm scripts

* https://www.ruanyifeng.com/blog/2016/10/npm_scripts.html

* 定义在 package.json 里的脚本命令，就是npm脚本

1. 查看所有命令

   npm run



## 原理

npm 脚本的原理非常简单。每当执行`npm run`，就会自动新建一个 Shell，在这个 Shell 里面执行指定的脚本命令。因此，只要是 Shell（一般是 Bash）可以运行的命令，就可以写在 npm 脚本里面。

==比较特别的是，`npm run`新建的这个 Shell，会将当前目录的`node_modules/.bin`子目录加入`PATH`变量，执行结束后，再将`PATH`变量恢复原样。==

这意味着，==当前目录的`node_modules/.bin`子目录里面的所有脚本，都可以直接用脚本名调用，而不必加上路径==。比如，当前项目的依赖里面有 Mocha，只要直接写`mocha test`就可以了。

> ```javascript
> "test": "mocha test"
> ```

而不用写成下面这样。

> ```javascript
> "test": "./node_modules/.bin/mocha test"
> ```

==由于 npm 脚本的唯一要求就是可以在 Shell 执行，因此它不一定是 Node 脚本，任何可执行文件都可以写在里面。==

* linux 使用 sh 文件，windows 使用 cmd

npm 脚本的退出码，也遵守 Shell 脚本规则。如果退出码不是`0`，npm 就认为这个脚本执行失败。



## npm link

* Todo

## 变量

* 所有的变量获取都要加变量符 $ 开头

### 1. 获取package内任何一个字段

npm 脚本有一个非常强大的功能，就是可以使用 npm 的内部变量。

首先，通过`npm_package_`前缀，npm 脚本可以拿到`package.json`里面的字段。比如，下面是一个`package.json`。

> ```javascript
> {
> "name": "foo", 
> "version": "1.2.5",
> "scripts": {
>  "view": "node view.js"
> }
> }
> ```

那么，变量`npm_package_name`返回`foo`，变量`npm_package_version`返回`1.2.5`。

1. 在 node 代码中获取

   package.json 字段值已经挂载到了 process.env 上，可以直接通过 process.env 获取，比如获取项目名称：process.env.name

2. 在 bash 脚本中获取（同样在npm scripts 也能获取）

   通过 `npm_package_`前缀能够获取，支持嵌套。比如：npm_package_config_test。（一直用下划线查找下去）

### 2. npm 配置变量

* 一般情况下，npm 推荐使用 config 字段进行变量字段配置

然后，npm 脚本还可以通过`npm_config_`前缀，拿到 npm 的配置变量，即`npm config get xxx`命令返回的值。比如，当前模块的发行标签，可以通过`npm_config_tag`取到。

* package config 的变量需要通过 npm_package_config_test 方式去拿

例子：

```json
{
  "config": {
    "test1": "222222"
  },
  "scripts": {
    "view": "echo $npm_package_config_test1"
  }
}
```

* 执行 npm run view 输出 222222



### 获取当前环境变量

* 包括node版本，系统的基础信息

npm run env



## tag

https://juejin.cn/post/6844903870578032647#heading-38

* npm tag: 类似于git里分支的概念。

  例子：npm dist-tag ls react 查看react发布的tag

  ```tex
  beta: 18.0.0-beta-24dd07bd2-20211208
  experimental: 0.0.0-experimental-354772952-20220420
  latest: 18.0.0
  next: 18.1.0-next-354772952-20220420
  rc: 18.0.0-rc.3
  ```

  * 上面的每一个tag对应一个版本

那`tag`到底有什么用呢？**`tag`类似于`git`里面分支的概念，发布者可以在指定的`tag`上发布版本，而使用者可以选择指定的`tag`来安装包。不同的标签下的版本之间互不影响，这在发布者发布预发布版本包和使用者尝鲜预发布版本包的同时，不影响到正式版本**。

* 类似于给人打的tag，方便人们去辨认和使用

### npm tag

https://juejin.cn/post/6844903870578032647

在发布包的时候执行`npm publish`默认会打上`latest`这个`tag`，实际上是执行了`npm publish --tag latest`。而在安装包的时候执行`npm install xxx`则会默认下载`latest`这个`tag`下面的最新版本，实际上是执行了`npm install xxx@latest`。当然，我们也可以自定义`tag`：

* 发布默认打latest tag

* 拉取包可以指定tag（@latest），也可以指定版本（@1.2.2）

  啥也不加默认拉取latest包

```tex
# 当前版本为1.0.1
npm version prerelease  # 1.0.2-0
npm publish --tag beta
npm dist-tag ls xxx  # # beta: 1.0.2-0
npm install xxx@beta  # 下载beta版本 1.0.2-0
```

* npm version prerelease 能够查看当前版本



当`prerelease`版本已经稳定了，可以将`prerelease`版本设置为稳定版本：

```bash
npm dist-tag add xxx@1.0.2-0 latest
npm dist-tag ls xxx  # latest: 1.0.2-0
```

## 将所有 node 依赖包版本更新到最新版本

* 查看旧版本的依赖

  ```bash
  npm outdated
  ```

* 更新依赖版本（不能更新大版本）

  npm update



## npm 减少重复

命令：`npm dedupe` 删除重复数据npm



## npm 检测依赖 —— depcheck

* npm容易发生一些依赖没安装的情况，比如说一个第三方安装了某个依赖，而你也没有安装而是直接使用，那么再卸载这个第三方的时候，就会报错，这个时候就需要一个能够检测依赖安装的工具，那就是 depcheck

depcheck 能够检测哪些依赖没安装，那些没使用