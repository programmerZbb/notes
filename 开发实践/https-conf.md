# 本地开发配置https

参考：https://juejin.cn/post/6847902224270491662

# https 加密过程

## 过程以及由来

https://www.zhihu.com/question/28617156/answer/2059783991

### 几个总结

* 非对称加密是为了加密双方通信加密的钥匙的，不是直接加密内容

* 非对称加密就是一个公钥A、一个私钥B，

  A加密的东西，只能用B解开；B加密的内容，只能用A解开。

* https 过程就是用CA机构公钥加密服务端的公钥，然后

### 加密

**公钥加密，[私钥解密](https://www.zhihu.com/search?q=私钥解密&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2059783991})，这个叫加密**，是为了保证内容安全，因为私钥只有自己知道，是为了保证这个信息不被中间人解开。

### 签名

**[私钥加密](https://www.zhihu.com/search?q=私钥加密&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2059783991})，公钥解密，这个叫签名**，是为了防止内容被篡改，因为公钥所有人都知道，所有人都能看到这个信息做验证。

* 签名是公开的，加密需要私钥解开

## 握手过程

客户端向服务器发送支持的SSL/TSL的协议版本号，以及客户端支持的加密方法，和一个客户端生成的随机数

服务器确认协议版本和加密方法，向客户端发送一个由服务器生成的随机数，以及数字证书

客户端验证证书是否有效，有效则从证书中取出公钥，生成一个随机数，然后用公钥加密这个随机数，发给服务器

服务器用私钥解密，获取发来的随机数

客户端和服务器根据约定好的加密方法，使用前面生成的三个随机数，生成对话密钥，用来加密接下来的整个对话过程

* https://juejin.cn/post/6844903892765900814

> 第一步，爱丽丝给出协议版本号、一个客户端生成的随机数（Client random），以及客户端支持的加密方法。
>
> 第二步，鲍勃确认双方使用的加密方法，并给出数字证书、以及一个服务器生成的随机数（Server random）。
>
> 第三步，爱丽丝确认数字证书有效，然后生成一个新的随机数（Premaster secret），并使用数字证书中的公钥，加密这个随机数，发给鲍勃。
>
> 第四步，鲍勃使用自己的私钥，获取爱丽丝发来的随机数（即Premaster secret）。
>
> 第五步，爱丽丝和鲍勃根据约定的加密方法，使用前面的三个随机数，生成"对话密钥"（session key），用来加密接下来的整个对话过程。

==握手过程主要是商定内容加密的方式，包括：加密方法、随机数==

# ssl/tls 

## 运行机制

https://www.ruanyifeng.com/blog/2014/02/ssl_tls.html

### 不使用 ssl/tls 风险

（1） **窃听风险**（eavesdropping）：第三方可以获知通信内容。

（2） **篡改风险**（tampering）：第三方可以修改通信内容。

（3） **冒充风险**（pretending）：第三方可以冒充他人身份参与通信。

SSL/TLS协议是为了解决这三大风险而设计的，希望达到：

> （1） 所有信息都是**加密传播**，第三方无法窃听。
>
> （2） 具有**校验机制**，一旦被篡改，通信双方会立刻发现。
>
> （3） 配备**身份证书**，防止身份被冒充。

### 名词

SSL 是指安全套接字层，安全套接字层(*SSL*) 

简而言之，它是一项标准技术，可确保互联网连接安全，保护两个系统之间发送的任何敏感数据，防止网络犯罪分子读取和修改任何传输信息，包括个人资料。两个系统可能是指服务器和客户端（例如，浏览器和购物网站），或两个服务器之间（例如，含个人身份信息或工资单信息的应用程序）。

此举可确保在用户和站点之间，或两个系统之间传输的数据无法被读取。它使用加密算法打乱传输中的数据，防止数据通过连接传输时被黑客读取。这里所说的数据是指任何敏感或个人信息，例如信用卡号和其他财务信息、个人姓名和住址等。 

TLS（传输层安全）传输层安全性（Transport Layer Security，*TLS*

是更为安全的升级版 SSL。由于 SSL 这一术语更为常用，因此我们仍然将我们的安全证书称作 SSL。但当您从DigiCert[购买 SSL](https://www.websecurity.digicert.com/zh/cn/ssl-certificate?inid=infoctr_buylink_sslhome) 时，您真正购买的是最新的 TLS 证书，有[ ECC、RSA 或 DSA 三种加密方式](https://www.websecurity.digicert.com/zh/cn/security-topics/how-ssl-works)可以选择。 

ssl -> tls

* 是升级的关系

### 历史

互联网加密通信协议的历史，几乎与互联网一样长。

> 1994年，NetScape公司设计了SSL协议（Secure Sockets Layer）的1.0版，但是未发布。
>
> 1995年，NetScape公司发布SSL 2.0版，很快发现有严重漏洞。
>
> 1996年，SSL 3.0版问世，得到大规模应用。
>
> 1999年，互联网标准化组织ISOC接替NetScape公司，发布了SSL的升级版[TLS](https://en.wikipedia.org/wiki/Secure_Sockets_Layer) 1.0版。
>
> 2006年和2008年，TLS进行了两次升级，分别为TLS 1.1版和TLS 1.2版。最新的变动是2011年TLS 1.2的[修订版](http://tools.ietf.org/html/rfc6176)。

目前，应用最广泛的是TLS 1.0，接下来是SSL 3.0。但是，主流浏览器都已经实现了TLS 1.2的支持。

TLS 1.0通常被标示为SSL 3.1，TLS 1.1为SSL 3.2，TLS 1.2为SSL 3.3。

* 看起来是一个东西

## 图解 

https://www.ruanyifeng.com/blog/2014/09/illustration-ssl.html

### ssl 协议握手过程

开始加密通信之前，客户端和服务端首先必须建立连接和交换参数，这个过程叫做握手。

### ssl 握手时长

### RSA算法

https://www.ruanyifeng.com/blog/2013/06/rsa_algorithm_part_one.html

* 也就是ssl握手阶段默认采用的算法

## Gatsby 实践

https://www.gatsbyjs.com/docs/local-https/

* Gatsby 推荐使用[devcert](https://github.com/davewasmer/devcert) 实现本地https 配置

  代码参考：some-practice -> devcert

```typescript
import express from 'express'
import https from 'https'
import http from 'http'
import fs from 'fs'

// console.log(express);
const app = express()

https.createServer({
  key: fs.readFileSync('./certs/tls.key'),
  cert: fs.readFileSync('./certs/tls.cert')
}, app).listen(8090)
http.createServer(app).listen(8091)

// app.listen(3000)
app.get('/', (req, res) => {
  res.json({
    test: '222'
  })
})
```

## 其他方式

