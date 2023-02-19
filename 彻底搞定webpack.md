# todo

1. webpack 常见配置
2. 写webpack



# 是什么——简介

* 本质上，**webpack** 是一个用于==现代 JavaScript 应用程序==的 ==*静态模块打包工具*==。当 webpack 处理应用程序时，它会在内部从一个或多个入口点构建一个 [依赖图(dependency graph)](https://webpack.docschina.org/concepts/dependency-graph/)，==然后将你项目中所需的每一个模块组合成一个或多个 *bundles*==，它们均为静态资源，用于展示你的内容。
* 从 v4.0.0 开始，**webpack 可以不用再引入一个配置文件**来打包项目，然而，它仍然有着 [高度可配置性](https://webpack.docschina.org/configuration)，可以很好满足你的需求。



# webpack 基础配置

* 本地安装 webpack 

* webpack  webpack-cli -D

  ```she
   yarn init -y
   yarn add webpack webpack-cli -D
  ```

新的打包方式：npx webpack

![webpack默认打包](.\picture\webpack默认打包.png)

从上图可以看出webpack 默认的打包方式，会打包生产环境，打包的代码也是经过最优压缩的

## 1. weback 可以进行0配置

打包工具 -> 输出后的结果（js 模块）

​	打包（支持js的模块化）

## 2. 手动配置webpack 

默认配置文件的名字： `webpack.config.js` 文件

```js
// webpack 是 node 写出来的 node 的写法
let path = require('path')

module.exports = {
    mode: "", //模式，默认有两种，production  development
    entry: "./src/index.js",  //入口，写相对路径
	output: {            // 出口配置
        filename: "bundel.js", // 打包后的文件名
        path: path.resolve(_dirname, "dist"), // 路劲必须是一个绝对路径，使用node.js的核心模块 path 模块,把相对路径解析成为绝对路径???尝试一下
    }
}
```

webpack 默认配置文件为 `webpack.config.js` 文件，该配置在 `node_modules/webpack-cli/bin/config/config-yargs.js` 文件中配置。

![webpack默认配置文件入口](.\picture\webpack默认配置文件入口.png)

## 3. 手动配置webpack的配置文件

可以通过打包时候的命令行来设置 webpack 的配置文件，设置webpack.my.config.js为默认配置文件，通过这种方式能够实现测试环境和生成环境的不同配置。

```shel
npx webpack --config webpack.my.config.js
```

### 3.1 `package.json`打包脚本配置

一般情况下，能够通过在 package.json 中配置 `scripts` 实现对命令行的简单操作。启动命令是 `npm run`+自定义的命令。

例子：

```js
{
    "scripts": {
        "build": "webpack --config webpack.my.config.js" //此处不需要再执行 npx 命令了。
    }
}
```

如果在 scripts 中没有配置后面的参数，需要在命令行的时候输入参数，

```shell
npm run dev -- --config webpack.my.config.js
```

webpack 规定 后面加参数需要写 `--`

## 4. webpack-dev-server

webpack-dev-server 通过express 来实现服务，只是在内存中打包，并没有打包出真正的 dist 的文件。

package 脚本配置

```js
"scripts": {
    "dev": "webpack-dev-server"
  }
```

dev-server 启动配置，在webpack.config.js 中：

```js
module.exports = {
    devServer: { //开发服务器配置
        port: 3000,
        progress: true, //进度条
        contentBase: './build', //打包到内存的目录
    	compress: true, //压缩
    }
}
```

用webpack 实现热加载，实现实时的打包编译

安装： npm i webpack-dev-server -D
    
或者加 -g 全局安装
    

配置： 在 package.json 文件中 scripts 对象中新加：
    
"dev": "webpack-dev-server"
    
会默认生成的 main.js 在本项目的根目录下。该文件看不见（在内存中 ，不在磁盘），托管到了内存中。内存  访问速度快。
    
后面可以加参数：（需要在package.json 中加）
    

    			--open 打包后自动打开浏览器
    
    			--port3000 端口
    
    			--hot
    
    			--compress  压缩
    
    			--host  127.0.0.1

```js
 "dev": "webpack-dev-server --open --port 3000 --hot"
```

中间不能有空格

4  04.51



# 总结

## 环境

* NODE_ENV（有时候也叫 BUILD_ENV） 关键字来代表表示环境

* `process.env.NODE_ENV`能设置node的环境，windows 下可能无法获取这个环境，因此需要搭配 cross-env 使用

  首先安装 npm i cross-env -D

  在 package.json 的 scripts 中

  ```js
  "dev": "cross-env NODE_ENV=development node build/build.js devBuild"
  ```

  