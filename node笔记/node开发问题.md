# url 部分

## 1. 后端怎么构建url

* 一般情况会使用 URL 类来初始化一个url对象

```js
let url = new URL('test', 'http://host')
```

* 可以使用这个初始化的对象，获取一些url解析的一些property，常见的再url上携带的东西都可以解析
* 常用的prop