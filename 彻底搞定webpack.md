# todo

1. webpack 常见配置

2. 写webpack

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

## 1. wenpack 可以进行0配置

打包工具 -> 输出后的结果（js 模块）

​	打包（支持js的模块化）

## 2. 手动配置webpack 

默认配置文件的名字： `webpack.config.js` 文件

```js
// webpack 是 node 写出来的 node 的写法
module.exports = {
    entry: "./src/index.js",  //入口，写相对路径
	output: {            // 出口配置
        
    }
}
```



