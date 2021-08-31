# JSONP

* 原理：script 标签不受跨域限制。
* 服务端返回的数据能够直接执行，调用本地已有的函数。

## 客户端

```js
/**
 * 
 * @param {string} url 
 * @param {object} params 
 * @param {string} callback 
 */
function createJsonP(url, params, callback) {
    const script = document.createElement('script')

    let queryString = '?'

    Object.keys(params).forEach(item => {
        queryString += `${item}=${params[item]}&`
    })

    queryString += `callback=${callback}`

    script.src = `${url}${queryString}`

    document.body.append(script)

}

function test(val) {
    console.log(val, '---val')
}

createJsonP('http://127.0.0.1:9003', { test: 222 }, 'test')

```



## 服务端

```js
const http = require('http')
const queryString = require('querystring')

const server = http.createServer((req, res) => {
    // console.log(req.url)

    const str = req.url.split('?')[1]
    const queryObj = queryString.parse(str)

    console.log(queryObj.callback)

    res.setHeader('Access-Control-Allow-Origin', '*')

    // res.end(`${queryObj.callback}("测试的数据")`)
    res.end('test(1+1)')
})

server.listen(9003, () => {
    console.log('server is running')
})

```



