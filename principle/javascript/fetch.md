fetch 使用，参考：https://www.ruanyifeng.com/blog/2020/12/fetch-tutorial.html

# 简介

`fetch()`的功能与 XMLHttpRequest 基本相同，但有三个主要的差异。

（1）`fetch()`使用 Promise，不使用回调函数，因此大大简化了写法，写起来更简洁。

（2）`fetch()`采用模块化设计，API 分散在多个对象上（Response 对象、Request 对象、Headers 对象），更合理一些；相比之下，XMLHttpRequest 的 API 设计并不是很好，输入、输出、状态都在同一个接口管理，容易写出非常混乱的代码。

（3）`fetch()`通过数据流（Stream 对象）处理数据，可以分块读取，有利于提高网站性能表现，减少内存占用，==对于请求大文件或者网速慢的场景相当有用==。XMLHTTPRequest 对象不支持数据流，所有的数据必须放在缓存里，不支持分块读取，必须等待全部拿到后，再一次性吐出来。

* 使用 promise 设计，写法简单
* 模块化设计，api分散到多个对象上
* 通过 stream 数据流处理数据，可以分块读取，提高网站性能表现，减少内存占用

# api

## response 对象：处理http响应

fetch 请求成功（后面定义什么叫成功）以后，会返回一个 response 对象。对应服务器的http响应。

response 对象包含一些静态属性，如下：

### response.ok

`Response.ok`属性返回一个布尔值，表示请求是否成功，`true`对应 HTTP 请求的状态码 200 到 299，`false`对应其他的状态码。

* 只判断是否成功

### response.status

* http 状态码

### response.statusText

* 返回 http  状态码对应的文案，比如 200：'OK'，404：'Not Found'等

### response.redirected

* 一个布尔值，是否重定向

### response.url

`Response.url`属性返回请求的 URL。如果 URL 存在跳转，该属性返回的是最终 URL。

* 如果进行了重定向，则返回重定向之后的url

### response.type

`Response.type`属性返回请求的类型。可能的值如下：

> - `basic`：普通请求，即同源请求。
> - `cors`：跨域请求。
> - `error`：网络错误，主要用于 Service Worker。
> - `opaque`：如果`fetch()`请求的`type`属性设为`no-cors`，就会返回这个值，详见请求部分。表示发出的是简单的跨域请求，类似`<form>`表单的那种跨域请求。
> - `opaqueredirect`：如果`fetch()`请求的`redirect`属性设为`manual`，就会返回这个值，详见请求部分。

* 主要是请求的类型。这个比较重要！需要结合http记忆

### 判断请求是否成功

`fetch()`发出请求以后，有一个很重要的注意点：只有网络错误，或者无法连接时，`fetch()`才会报错，其他情况都不会报错，而是认为请求成功。

这就是说，即使服务器返回的状态码是 4xx 或 5xx，`fetch()`也不会报错（即 Promise 不会变为 `rejected`状态）。

* 主要是根据 fetch api是否会变为 rejected 的状态
* 跨域和网络错误都会请求失败，但是跨域不会告诉js失败的原因。

只有通过`Response.status`属性，得到 HTTP 回应的真实状态码，才能判断请求是否成功。请看下面的例子。

* 只有网络错误或者无法连接的时候才会直接报错，其他情况需要开发者自己封装。
* 这也就是业务需要自己封装 http 请求的原因，每个业务定义的失败可能不太一样，可能需要再请求成功的相应中添加状态 code

第二种方案：另一种方法是判断`response.ok`是否为`true`。

## fetch 配置项

基本语法就是 `fetch(utl, option)`，第二个参数就是配置项

## fetch 流式请求

fetch 开创式的使用流式请求的方案

* 流只能被消费一次，一旦被消费就不能够再次被消费了！！

​	如下，一旦使用 body 消费 response，就不能够再使用 arrayBuffer 进行消费了

```typescript
let blob = new Blob(['test'])
let res = new Response(blob)
let reader2 = res.body.getReader()

// 报错
res.arrayBuffer()
```

### response body

Response 的 body 属性是一个可读流，利用这个可读流就能实现增量读取，比如chargpt的聊天

```typescript
// 实现一个流式读取数据
// fetch 提供了流式访问方式，只要响应头到达就开始接受数据了
const query = async () => {
  const result = await fetch('http://127.0.0.1:8090', {
    method: 'POST',
    // 文本类型的请求
    body: JSON.stringify({
      content: 'test',
    }),
  })
  // const text = await result.text()
  // result.body 是一个可读流
  const reader = result.body?.getReader()
  // let done: boolean
  // let value: Uint8Array | undefined
  // do {
  //   let res = await reader!.read()
  //   done = res.done
  //   value = res.value
  // } while(done)
  while (1) {
    const { done, value } = await reader!.read()
    if (!done) {
      break
    }
  }
}
```

## post 发送 formdata 数据

### application/x-www-form-urlencoded

* 看标题为 urlencode，也就是querystring 的方式

content-type 为 application/x-www-form-urlencoded 时，为一般的 formdata 请求，不需要专门构建 formdata 对象去请求，只需要一般的 formdata 数据结构即可（test=1&test1=2的方式）

```typescript
fetch('http://127.0.0.1:3000/profile?test=222', {
        method: 'POST',
        // headers
        // body: formData,
        body: new URLSearchParams({
            test: '222',
            test1: '333'
        }),
        headers: {
            "Content-Type": 'application/x-www-form-urlencoded'
        },
    })
```

### multipart/form-data

* content-type 为 multipart/form-data 的方式，这种形式需要构建 formdata 对象才能正确请求
* 不用专门定制`content-type`，浏览器会自动拼接

```typescript
fetch('http://127.0.0.1:3000/profile?test=222', {
        method: 'POST',
        body: formData,
        headers: {
            // "Content-Type": 'application/x-www-form-urlencoded'
            // "Content-Type": 'application/json'
        },
    })
```

## fetch 错误处理

### 只有网络错误才会失败

> 当遇到网络错误时，[`fetch()`](https://developer.mozilla.org/zh-CN/docs/Web/API/fetch) 返回的 promise 会被 reject，并传回 [`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError)，虽然这也可能因为权限或其他问题导致。成功的 fetch() 检查不仅要包括 promise 被 resolve，还要包括 [`Response.ok`](https://developer.mozilla.org/zh-CN/docs/Web/API/Response/ok) 属性为 true。HTTP 404 状态并不被认为是网络错误。

* 只有网络错误的时候才会被 reject

### 其他错误类型



## 取消请求

可以使用 AbortController 取消请求，取消之后会（如果还没拿到http响应），会直接把请求 cancel 掉（http请求层面）。

```typescript
let contr = new AbortController
let signal = contr.signal
signal.addEventListener('abort',
  () => console.log('abort!')
)
setTimeout(() => {contr.abort()}, 1000)
let res = await fetch('http://127.0.0.1:3000/get', {
    signal: contr.signal
})
```

