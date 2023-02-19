# workspaces(工作区)——代码组织方式

* todo 和submodule、yarn link 区别

https://cloud.tencent.com/developer/section/1477773

工作空间是一种新的方式来设置您的包体系结构，默认情况下可以从Yarn 1.0开始。它允许您以这种方式设置多个软件包，只需要运行`yarn install`一次即可将所有软件包安装在一个通道中。

* 适合多个项目组合的工程，比如说前后端在一个项目结构中

## 为什么选用workspace

- 您的依赖关系可以链接在一起，这意味着您的工作区可以相互依赖（workspace内也可以引用另一个workspace），同时始终使用最新的可用代码。这也是一个相对于`yarn link`更好的机制，因为它只影响你的工作空间树，而不是整个系统。
- 您所有的项目依赖关系都将被安装在一起，为Yarn提供更多的自由度来更好地优化它们。
- 对于每个项目，Yarn将使用一个单独的锁文件而不是为每个工程使用一个不同的锁文件，这意味着更少的冲突和更容易的审查。只有根目录下一个lock文件



## 怎么使用

在`package.json`文件中添加一下内容，当前工程目录就是 `workspace root`

Package.json

```json
{
    "private": true,
    "workspaces": [
        "workspace-a",
        "workspace-b"
    ]
}
```

* 请注意，`private: true`是必需的！工作区并不意味着要发布，所以我们增加了这个安全措施，以确保没有任何东西可以意外暴露它们。

* 如果在script中使用到了该 workspace，需要填写该工程的name，而不是目录，workspace的方式只是引入了该工程

  ```json
  {
    "scripts": {
      "install_clean_boundle": "yarn install && yarn clean && yarn workspace env_def build "
    }
  }
  ```

  * 如上有一个workspace的name设置为了env_def

## 有什么优势

* 只需要在根目录下安装一次，所有workspace共同依赖一个`node_modules`文件



## 能否和git submodule结合

* 如果submodule需要安装依赖（node_modules），则可以使用workspace进行代码组织。

和submodule的区别呢？

* submodule的工程一般不需要有自己的依赖

# yarn link

https://juejin.cn/post/6844904164468768776

* yarn link 更像是把本地的包转换成为了一个远端包的引用方式，会在node_modules里面出现。
* 使用的软连接的方式



## 使用

* 在被引用包使用 `yarn link`

  然后会打印出link的使用方式

* 在引用包中使用

  ```shell
  yarn link 'b'
  ```

* 在引用包的node_modules里就可以看到这个引用了



# yarn 命令行

* 建议使用 yarn --help 查看

## 忽略不同版本造成的引擎错误

--ignore-engines

https://blog.csdn.net/junjiahuang/article/details/120141239

