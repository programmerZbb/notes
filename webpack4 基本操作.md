# 创建基本的 webpack4.x 项目

1. 运行 npm init -y 快速初始化项目。

2. 在项目根目录新建一个 src 文件，用来存放项目的源代码。

3. 项目根目录下新建一个 dist 文件，发布时的代码。

   在 dist 中新建一个 index.html 文件

4. 在 src 文件夹下新建一个 index.html 文件

5. 在 src 下新建一个 main.js 文件，该文件是 webpack 的入口文件。

补充：npm 允许在 package.json 文件中，使用 scripts 字段定义脚本命令。

```javascript
* {  // ... "scripts": { "build": "node build.js" }}
```

上面代码是package.json文件的一个片段，里面的scripts字段是一个对象。它的每一个属性，对应一段脚本。比如，build命令对应的脚本是node build.js。命令行下使用npm run命令，就可以执行这段脚本。$ npm run build 等同于执行$ node build.js。

npm run 可以查看当前项目的所有脚本。

6. 使用 npm 安装webpack，运行 npm i webpack --save-dev

7. 安装 webpack 命令行工具， npm i webpack webpack-cli -D

8. 命令 webpack 打包（提供了命令行工具）

9. 在根目录下需要新建一个 webpack.config.js 的js文件，内容如下：

   ```javascript
   // 向外暴露一个打包的配置对象；webpack 是基于 node.js 构建的，支持所有 node  API 和 语法
   module.exports = {
       mode: 'development' // development 和 production
       //在 webpack4.x 中，有一个很大的特性，就是约定大于配置，默认的打包入口路径是 src -> index.js
   }
   
   ```

   约定大于配置就是为了减少配置文件的体积。

   默认约定了：

   * 打包的入口是 `src`->`index.js`文件
   * 打包的输出文件是：`dist`->`main.js`

   打包 webpack 命令

10. webpack 打包好后，会在 dist 目录下生成一个 main.js 文件，就是页面调用的入口文件。

```html
<script src='../dist/main.js'></script>
```

11. 如果将 mode 改成 production 则会对文件进行压缩处理。不需要想老版本一样装插件。

    mode 选项是必须的。

12. 用webpack 实现热加载，实现实时的打包编译

    安装： npm i webpack-dev-server -D

    ​	或者加 -g 全局安装

    配置： 在 package.json 文件中 scripts 对象中新加：

    ​		"dev": "webpack-dev-server"

    ​		会默认生成的 main.js 在本项目的根目录下。该文件看不见（在内存中 ，不在磁盘），托管到了内存中。内存  访问速度快。

    ​		后面可以加参数：（需要在package.json 中加）

    ​			--open 打包后自动打开浏览器

    ​			--port3000 端口

    ​			--hot

    ​			--compress  压缩

    ​			--host  127.0.0.1

    ```javascript
     "dev": "webpack-dev-server --open --port 3000 --hot"
    ```

    

    中间不能有空格

    执行： npm run dev

    ​	该命令会自动打包，会一直在后台执行，实时监控文件变化

    * scripts 中的命令 都需要 加一个 run 来运行。

13. webpack 将首页也放到内存中，方便快速更新。（这个插件就是能让html也被打包到该文件的插件）


安装： npm i html-webpack-plugin -D

使用：webpack.config.js 文件中

```javascript
const path = require('path')
const HtmlWebPackPlugin = require('html-webpack-plugin') //导入自动生成 index 页面的插件
//创建一个插件的实例对象
const htmlPlugin = new HtmlWebPackPlugin({
    template: path.join(__dirname, './src/index.html')//源文件
    filename: 'index.html'  // 生成的内存中首页的名称
})  
```

然后：

```javascript
module.exports = {
    mode: 'development' // development 和 production
    //在 webpack4.x 中，有一个很大的特性，就是约定大于配置，默认的打包入口路径是 src -> index.js
    plugins: [
    	htmlPlugin
    ]
}
```

该插件有一个能力，会动给首页添加 mian.js 的引入。


babel 的配置

在写 React jsx 语法时，不是直接调用显示到页面上的，需要 babel 的转换。

详见react基础。





## 注意：

拉下来的项目一定要先运行 npm install 安装所有的依赖项。

* webpack 的插件一个都是采用 - 链接的方式命名
* dev-server 打包生成的 js 文件没有放到实际的物理磁盘中，而是托管到电脑的内存中了。



 # webpack 3.x 属性配置

配置：

```javascript
const path = require("path")
module.exports = {
    //entry: { //main是默认入口，也可以是多入口
    //    mian: './src/main.js'
    //},
    entry: path.join(__dirname, "./src/main.js"), // 入口表示 webpack 打包哪个文件
    output: {
        filename: 'build.js', //指定js 文件
        path: path.join(__dirname, '/dist')                       //最好是绝对路径
    }
}
```

path: path.join(__dirname, 'dist') 后面可以跟很多参数，比如说 

path: path.join(__dirname, 'dist', '..') 代表 dist 的上一层目录

 ## 在网页中常用的静态资源

* js

  .js   .jsx    .coffee    .ts

* css

  .css .less  .scss(sass)

* Images 

  .jpg   .png   .gif   .bmp   .svg

* 字体文件

  .svg   .ttf  .eot   .woff   .woff2

* 模板文件

  .ejs    .jade   .vue  (这是在webpack中定义组件的方式, 推荐这么用)

## 网页中引入静态资源多了以后有什么问题？？？

1. 网页加载速度慢，因为 我们要发起很多的二次请求。
2. 要处理错综复杂的依赖关系

### 解决上述问题

1. 合并、压缩、精灵图、图片的Base64编码
2. 使用 webpack 可以解决各个包之间的复杂依赖关系；

### 常用的 网页 性能优化

减少请求（合并请求），压缩代码，压缩图片，精灵图

webpack css模块化的处理也能够减少 ajax请求的发送

## webpack 

* 是前端项目自动化构建工具，可以完美实现资源的合并、打包、混淆等诸多功能。


### nrm

安装 npm i nrm -g，提供了安装包的地址

* 命令

  * nrm list

    查看所有安装包路径

  * nrm use npm

    切换到 npm 的官方地址

* nrm 是用来切换 npm 安装包的地址，npm还是装包工具

* 注意区分地址和安包工具的区别

  例如地址有一个cnpm， 地址也有一个 cnpm

# vue 中webpack babel 的配置

- 安装 babel 插件

  - 运行 npm i babel-core babel-loader babel-plugin-transform-runtime -D
  - 运行 npm i babel-preset-env babel-preset-stage-0 -D  （此处针对react）

  注意： preset 的包代表语法

- 安装能够识别转换 jsx 语法的包 babel-preset-react

  - 运行 npm i babel-preset-react -D

# webpack 4 中的基础

```javascript
const path = require('path')
const HtmlWebPackPlugin = require('html-webpack-plugin') //导入自动生成 index 页面的插件
//创建一个插件的实例对象
const htmlPlugin = new HtmlWebPackPlugin({
    template: path.join(__dirname, './src/index.html'),//源文件
    filename: 'index.html'  // 生成的内存中首页的名称
}) 

module.exports = {
    mode: "development",
    plugins: [
    	htmlPlugin
    ],
    module:{   
        rules: [ // 第三方匹配规则
            { test: /\.js$/,loader: 'babel-loader', exclude: /node_modules/, options:{
                presets:['es2015'],  // 关键字
                plugins:['transform-runtime']    // 函数
            } },//千万别忘记添加 exclude 排除项
            { test:/\.(jpg|svg|png|gif)$/, use: 'url-loader?limit=4096&name=[name].[ext]' },
            {test: /\.css$/,use: ['style-loader', 'css-loader']} 
        ],
    }
}
```

常见配置：

```javascript
// 配置快捷启动
"scripts": {
    "build": "webpack",
    "start": "webpack-dev-server"
}
```

开发的时候用 run start

上线的时候用 run build

注意：一定要用 loader

```javascript
{ test: /\.js$/,loader: 'babel-loader', exclude: /node_modules/, options:{
                presets:['es2015'],  // 关键字
                plugins:['transform-runtime']    // 函数
            } }
```

## 样式表的导入

import "./css/index.css"

直接导入样式表文件，直接写类。

### less 和 scss 文件的处理

* 处理less 文件，需要安装

  npm i less -D （less-loader 内部依赖的文件，不需要再 webpack 配置文件中定义）

  npm i less-loader -D

  webpack 配置

  ```js
  {test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader']}
  ```

  因为 less 是样式，肯定要依赖前面的配置文件，因此要设置

* 处理 .scss 文件，需要安装

  npm i sass-loader -D

  cnpm i node-sass -D 推荐使用cnpm

  ```js
  {test: /\.scss$/, use: ['style-loader', 'css-loader', 'sass-loader']}
  ```

### url-loader

* 默认的情况下，webpage 不能处理样式 css 文件中的 urL 地址，不管是图片还是字体库，只要是 url 地址都处理不了，需要第三方的loader 来处理。

  安装：

  npm i url-loader file-loader -D

  需要配置 url-loader

  配置：

  ```js
  {test: /\.(jpg|png|gif|bmp|jpeg)$/, use: "url-loader?limit=7631&name=[hash:8]-[name].[ext]"} // 处理图片路径的 loader
  ```

  然后就可以在样式中， background-iamge:url() 这样写图片了。

  background 处理之后的图片采用 base64 编码，减少 url 请求。可以在后面加参数来规定什么图片转化为 base64 的代码。 ?limit=7631 限制图片的大小（单位是 字节 byte），大于此限制的图片，不采用 base64 的编码，直接采用 图片的地址（hash 编码值，防止图片重复）。name=[name].[ext] 采用原来的命名和后缀名，一般不采用此配置。[hash:8] 最多 32.

  node_modules 中的 css 文件不需要相对路径，还是采用 node 中引入路径的方法，一定要引到  .css 文件。

  * 样式文件中的字体文件配置

    ```js
    {test: /\.(ttf|eot|svg|woff|woff2)$/, use: "url-loader"} // 处理字体文件的 loader
    ```

  json 文件中不能写注释！！！

## babel 的配置

安装： npm i babel-core babel-loader babel-plugin-transform-runtime -D

​		npm i babel-preset-env babel-preset-stage-0 -D

配置： 

```js
{ test: /\.js$/,loader: 'babel-loader', exclude: /node_modules/}
```

新建一个 .babelrc 文件

```js
{
    "presets": ["env", "stage-0"], // 语法
    "plugins": ["transform-runtime"]
}
```

 env 是最新的，比es2015 的新



# http-server

* 安装：

  npm i http-server -S

* 启动 

  http-server start

  http://www.cnblogs.com/leee/p/5502727.html

# webpack 的一些基本知识

## 1. bundle（捆）和 chunks（块）

1. chunks就是代码块的意思，有name的chunk是在entry里配置了name的，那些1，2，3，4啥的应该是用了code splitting配置生成的，数字是chunk的id
2. ==多个chunk合在一起就是bundle==，一个bundle可以理解为一个大的js打包之后生成的文件，而多个bundle里可能有公共的部分，或者一个bundle里的东西并不需要一次性加载，需要按照路由按需加载，这个时候就需要按需加载，拆分成不同的chunk
3. 你的和官方的不一致，可能是因为你没有用到code splitting

![webpack bundle](.\picture\webpack bundle.jpg)

## 2. webpack的入口和出口的配置

* 查看官网

## 3. webpack 代码分离

* 在vue单页应用中，若不做任何处理，所有vue文件会打包为一个文件，这个文件非常的大，造成网页在首次进入时比较缓慢。做了代码分割后，会将代码分离到不同的bundle中，然后进行按需加载这些文件，能够提高页面首次进入的速度，网站性能也能够得到提升。（其实就相当于原生里面的异步加载）

### import() 方法在 webpack 中的使用

* import() 方法

  动态地加载模块。调用 `import()` 之处，被作为分离的模块起点，意思是，被请求的模块和它引用的所有子模块，会分离到一个单独的 chunk 中。参数为相对路径

  该方法内部调用一个 Promise 方法

* 因为 es6 中的import 只能放在顶层作用域的使用，如果想要在组件中使用 import ，需要使用插件`syntax-dynamic-import`

  * 安装：

    `npm i babel-plugin-syntax-dynamic-import -D`

  * .babelrc 配置

    ```js
    {
      "plugins": ["syntax-dynamic-import"]
    }
    ```

* import 方法可以使用如下的方式给 chunk 添加一个name

  ```js
  import(/* webpackChunkName: "name" */ "相对地址")
  ```

  也可以使用在 output 中配置的形式来实现对非入口 chunk 的命名

  ```js
  output: {
      filename: '[name].bundle.js',
      chunkFilename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
   },
  ```

  * filename 是入口文件的命名，chunkFilename 是非入口文件的命名

## 4. webpack 规定打包时的配置文件

> 默认情况下 webpack 会自动搜索项目下的 webpack.config.js 文件作为打包的配置文件，如果需要指定打包时的配置文件，需要在打包的时候加入指令 --config webpack.dev.config.js ，能够在不同的打包情况下配置不同的打包文件。

Webpack 在执行的时候，除了在命令行传入参数，还可以通过指定的配置文件来执行。默认情况下，会搜索当前目录的 `webpack.config.js` 文件，这个文件是一个 node.js 模块，向外暴露一个 json 格式的配置信息对象，或者通过 `--config` 选项来指定配置文件。

## 5. webpack loader

### 1. 编写一个 loader 

loader 是导出为一个函数的 node 模块。该函数在 loader 转换资源的时候调用。给定的函数将调用 [loader API](https://webpack.docschina.org/api/loaders/)，并通过 `this` 上下文访问。

## 6. webpack 编写一个全局变量吧

能够使用 webpack.DefinePlugin 来实现在webpack项目中定义一个全局的变量，不需要单独 require 引入插件。

在webpack 配置文件中使用：

```js
 plugins: [
    new webpack.DefinePlugin({
      'process.env': config.dev.env
    }),
 ]
```

单独添加：

```js
webpackConfig.plugins.push(new webpack.DefinePlugin({
    'CODE_ENV': JSON.stringify(codeEnv)
}))
```

