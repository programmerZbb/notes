# 调试配置——launch.json

## json vs jsonc

* jsonc 就是带 comment 的json

## 配置

* 参考：https://code.visualstudio.com/docs/editor/debugging#_launchjson-attributes

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "debug react attach",
            "port": 3000,
            "request": "attach",
            "type": "chrome",
            "webRoot": "${workspaceFolder}"
        },
        {
            "type": "chrome", // 类型，常用 chrome 和 node
            "request": "launch", // attach 附件；launch 启动
            "name": "debug react", // 配置名称，在run的时候能够筛选
            "url": "http://localhost:3000",
            "webRoot": "${workspaceFolder}",
            "userDataDir": true, // 每次都是一个临时的启动目录，不会保持登录状态；可以设置为false，保持登录状态（但是需要关闭之前所有打开的tab）
            "runtimeExecutable": "stable", // 选择启动时浏览器类型
            "runtimeArgs": [], // 参考react启动参数
        }
    ]
}
```



* next.js 的调试配置

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Next.js: debug server-side",
      "type": "node-terminal",
      "request": "launch",
      "command": "npm run dev"
    },
    {
      "name": "Next.js: debug client-side",
      "type": "chrome",
      "request": "launch",
      "url": "http://localhost:3000"
    },
    {
      "name": "Next.js: debug full stack",
      "type": "node-terminal",
      "request": "launch",
      "command": "npm run dev",
      "serverReadyAction": {
        "pattern": "started server on .+, url: (https?://.+)",
        "uriFormat": "%s",
        "action": "debugWithChrome"
      }
    }
  ]
}
```

## attach 和 launch 模式的区别

https://juejin.cn/post/6844904198702645262

* launch 能够在启动的时候添加一些启动配置项
* attach 适合已经启动的应用进行调试

两种模式都需要首先启动应用，只是在调试的时候有区别

## snippets

* 新建 snippets

  1. code -> preferences -> configure user snippets

  2. 选择语言
  3. 然后新建

### 语法

1. 光标位置&tab快速选择

   * 使用 $1 的方式预留光标选择位置

   * ${1:3000} 的方式给默认值
   * ${1:array}`, `${2:element} 的方式，使用tab能够快捷跳转设置

## snippet generator

* 在线工具：https://snippet-generator.app/



# 编译配置 tasks.json

