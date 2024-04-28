# 协议入门

https://www.ruanyifeng.com/blog/2016/08/http.html

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/Evolution_of_HTTP

## 万维网的发明

world-wide-web

在随后的 1990 年项目实施期间被更名为*万维网*（World Wide Web）。它在现有的 TCP 和 IP 协议基础之上建立，由四个部分组成：

- 一个用来表示超文本文档的文本格式，*[超文本标记语言](https://developer.mozilla.org/zh-CN/docs/Web/HTML)*（HTML）。
- 一个用来交换超文本文档的简单协议，超文本传输协议（HTTP）。
- 一个显示（以及编辑）超文本文档的客户端，即网络浏览器。第一个网络浏览器被称为 *WorldWideWeb。*
- 一个服务器用于提供可访问的文档，即 *httpd* 的前身。

## http 0.9

跟后来的版本不同，HTTP/0.9 的响应内容并不包含 HTTP 头。这意味着只有 HTML 文件可以传送，无法传输其他类型的文件。也没有状态码或错误代码。一旦出现问题，一个特殊的包含问题描述信息的 HTML 文件将被发回，供人们查看。

* 非常简单，只能响应一个html文件

# http 1.0

## 升级

- 协议版本信息现在会随着每个请求发送（`HTTP/1.0` 被追加到了 `GET` 行）。
- 状态码会在响应开始时发送，使浏览器能了解请求执行成功或失败，并相应调整行为（如更新或使用本地缓存）。
- 引入了 HTTP 标头的概念，无论是对于请求还是响应，允许传输元数据，使协议变得非常灵活，更具扩展性。
- 在新 HTTP 标头的帮助下，具备了传输除纯文本 HTML 文件以外其他类型文档的能力（凭借 [`Content-Type`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type) 标头）。

## Content-Type 字段

关于字符的编码，1.0版规定，==头信息必须是 ASCII 码==，后面的==数据可以是任何格式==。因此，服务器回应的时候，必须告诉客户端，数据是什么格式，这就是`Content-Type`字段的作用。

下面是一些常见的`Content-Type`字段的值。

> - text/plain
> - text/html
> - text/css
> - image/jpeg
> - image/png
> - image/svg+xml
> - audio/mp4
> - video/mp4
> - application/javascript
> - application/pdf
> - application/zip
> - application/atom+xml

这些数据类型总称为`MIME type`，每个值包括==一级类型和二级类型==，之间用斜杠分隔。

除了预定义的类型，厂商也可以自定义类型。

`MIME type`还可以在尾部使用分号，添加参数。

> ```http
> Content-Type: text/html; charset=utf-8
> ```

* 注意：末尾不能够再添加分号！

客户端请求的时候，可以使用`Accept`字段声明自己可以接受哪些数据格式。

> ```http
> Accept: */*
> ```



## Content-Encoding

由于发送的数据可以是任何格式，因此可以把数据压缩后再发送。`Content-Encoding`字段说明数据的压缩方法。

> ```http
> Content-Encoding: gzip
> Content-Encoding: compress
> Content-Encoding: deflate
> ```

客户端在请求时，用`Accept-Encoding`字段说明自己可以接受哪些压缩方法。

> ```http
> Accept-Encoding: gzip, deflate
> ```



## 缺点

HTTP/1.0 版的主要缺点是，每个TCP连接只能发送一个请求。发送数据完毕，连接就关闭，如果还要请求其他资源，就必须再新建一个连接。

TCP连接的新建成本很高，因为需要客户端和服务器三次握手，并且开始时==发送速率较慢（slow start）==。所以，HTTP 1.0版本的性能比较差。随着网页加载的外部资源越来越多，这个问题就愈发突出了。

为了解决这个问题，有些浏览器在请求时，用了一个非标准的`Connection`字段。

> ```http
> Connection: keep-alive
> ```

这个字段要求服务器不要关闭TCP连接，以便其他请求复用。服务器同样回应这个字段。

> ```http
> Connection: keep-alive
> ```

一个可以复用的TCP连接就建立了，直到客户端或服务器主动关闭连接。但是，这不是标准字段，不同实现的行为可能不一致，因此不是根本的解决办法。

* 这个说的是和服务端的连接不断，可以继续发送其他路由的请求。

# HTTP 1.1

## 改进

- 连接可以复用，节省了多次打开 TCP 连接加载网页文档资源的时间。
- 增加管线化技术，允许在第一个应答被完全发送之前就发送第二个请求，以降低通信延迟。
- 支持响应分块。
- 引入额外的缓存控制机制。
- 引入内容协商机制，包括语言、编码、类型等。并允许客户端和服务器之间约定以最合适的内容进行交换。
- 凭借 [`Host`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Host) 标头，能够使不同域名配置在同一个 IP 地址的服务器上。

## 持久链接

1.1 版的最大变化，就是引入了持久连接（persistent connection），==即TCP连接默认不关闭==，可以被多个请求复用，不用声明`Connection: keep-alive`。（默认的）

客户端和服务器发现对方一段时间没有活动，就可以主动关闭连接。不过，规范的做法是，客户端在最后一个请求时，发送`Connection: close`，明确要求服务器关闭TCP连接。

> ```http
> Connection: close
> ```

目前，对于同一个域名，大多数浏览器允许同时建立==6个持久连接。==

* 默认开启的，如果需要断开 Connection: close 请求

### 怎么样断开连接

* 查看tcp keep-alive 断开连接

## 管道机制——不需要按顺序处理

1.1 版还引入了管道机制（pipelining），即在同一个TCP连接里面，客户端可以同时发送多个请求。这样就进一步改进了HTTP协议的效率。

举例来说，客户端需要请求两个资源。以前的做法是，在同一个TCP连接里面，先发送A请求，然后等待服务器做出回应，收到后再发出B请求。管道机制则是允许浏览器同时发出A请求和B请求，但是服务器还是按照顺序，先回应A请求，完成后再回应B请求。

* 全双工通信

==发送请求的优化==

## Content-Length

一个TCP连接现在可以传送多个回应，势必就要有一种机制，区分数据包是属于哪一个回应的。（怎么样切片）这就是`Content-length`字段的作用，声明本次回应的数据长度。

> ```http
> Content-Length: 3495
> ```

上面代码告诉浏览器，本次回应的长度是3495个字节，后面的字节就属于下一个回应了。

在1.0版中，`Content-Length`字段不是必需的，因为浏览器发现服务器关闭了TCP连接，就表明收到的数据包已经全了。



## 分块传输编码——对于非静态的请求，动态内容

使用`Content-Length`字段的前提条件是，服务器发送回应之前，必须知道回应的数据长度。

对于一些很耗时的动态操作来说，这意味着，服务器要等到所有操作完成，才能发送数据，显然这样的效率不高。更好的处理方法是，产生一块数据，就发送一块，采用"流模式"（stream）取代"缓存模式"（buffer）。

因此，1.1版规定可以不使用`Content-Length`字段，而使用["分块传输编码"](https://zh.wikipedia.org/wiki/分块传输编码)（chunked transfer encoding）。只要请求或回应的头信息有`Transfer-Encoding`字段，就表明回应将由数量未定的数据块组成。

> ```http
> Transfer-Encoding: chunked
> ```

每个非空的数据块之前，会有一个16进制的数值，表示这个块的长度。最后是一个大小为0的块，就表示本次回应的数据发送完了。下面是一个例子。（利用这种机制来区分响应块）

## 其他

1.1版还新增了许多动词方法：`PUT`、`PATCH`、`HEAD`、 `OPTIONS`、`DELETE`。 restful api 说一下

另外，客户端请求的头信息新增了`Host`字段，用来指定服务器的域名。

> ```http
> Host: www.example.com
> ```

有了`Host`字段，就可以将请求发往同一台服务器上的不同网站，为虚拟主机的兴起打下了基础。



## 缺点

虽然1.1版允许复用TCP连接，但是同一个TCP连接里面，==所有的数据通信是按次序进行的==。服务器只有处理完一个回应，才会进行下一个回应。要是前面的回应特别慢，后面就会有许多请求排队等着。这称为["队头堵塞"](https://zh.wikipedia.org/wiki/队头阻塞)（Head-of-line blocking）。

为了避免这个问题，只有两种方法：一是减少请求数，二是同时多开持久连接。这导致了很多的网页优化技巧，比如合并脚本和样式表、将图片嵌入CSS代码、域名分片（domain sharding）等等。如果HTTP协议设计得更好一些，这些额外的工作是可以避免的。

* webpack 干的事



# HTTP 2

2015年，HTTP/2 发布。它不叫 HTTP/2.0，是因为标准委员会不打算再发布子版本了，下一个新版本将是 HTTP/3。

优化点：

头信息：

* 采用二进制
* 压缩（与服务端共同维护一张头信息表，发送id来 diff）

多路复用：

* 多工：双向实时的通信
* 数据流：数据切割，把每个包用 id 做切割，可取消，可指定优先级



http 本身就要求是一个简单的协议，如果需要实现多工通信，协议的复杂度和成本就会增大。

## 改进

- HTTP/2 是二进制协议而不是文本协议。不再可读，也不可无障碍的手动创建，改善的优化技术现在可被实施。
- 这是一个多路复用协议。并行的请求能在同一个链接中处理，移除了 HTTP/1.x 中顺序和阻塞的约束。
- 压缩了标头。因为标头在一系列请求中常常是相似的，其移除了重复和传输重复数据的成本。
- 其允许服务器在客户端缓存中填充数据，通过一个叫服务器推送的机制来提前请求。

## 二进制协议

HTTP/1.1 版的头信息肯定是文本（ASCII编码），数据体可以是文本，也可以是二进制。HTTP/2 则是一个彻底的二进制协议，==头信息和数据体都是二进制，并且统称为"帧"（frame）：头信息帧和数据帧。==

二进制协议的一个好处是，可以定义额外的帧。HTTP/2 定义了近十种帧，为将来的高级应用打好了基础。如果使用文本实现这种功能，解析数据将会变得非常麻烦，二进制解析则方便得多。

* 说白了就是二进制能够存储更复杂的数据结构

## 多工

HTTP/2 复用TCP连接，在一个连接里，客户端和浏览器都可以同时发送多个请求或回应，而且不用按照顺序一一对应，这样就避免了"队头堵塞"。

举例来说，在一个TCP连接里面，服务器同时收到了A请求和B请求，于是先回应A请求，结果发现处理过程非常耗时，于是就发送A请求已经处理好的部分， 接着回应B请求，完成后，再发送A请求剩下的部分。

这样双向的、实时的通信，就叫做多工（Multiplexing）。

* ? 问题：没有发现 http2 就是 tcp 的全双工通信，http1 就是半双工通信。和 node 实现的 RPC 有什么联系

## 数据流

因为 HTTP/2 的数据包是不按顺序发送的，同一个连接里面连续的数据包，可能属于不同的回应。因此，必须要对数据包做标记，指出它属于哪个回应。

==HTTP/2 将每个请求或回应的所有数据包，称为一个数据流（stream）。每个数据流都有一个独一无二的编号。数据包发送的时候，都必须标记数据流ID，用来区分它属于哪个数据流。另外还规定，客户端发出的数据流，ID一律为奇数，服务器发出的，ID为偶数。==

数据流发送到一半的时候，客户端和服务器都可以发送信号（`RST_STREAM`帧），取消这个数据流。1.1版取消数据流的唯一方法，就是关闭TCP连接。这就是说，HTTP/2 可以取消某一次请求，同时保证TCP连接还打开着，可以被其他请求使用。

客户端还可以指定数据流的优先级。优先级越高，服务器就会越早回应。

* Buffer 变为 stream

## 头信息压缩

HTTP 协议不带有状态，每次请求都必须附上所有信息。所以，请求的很多字段都是重复的，比如`Cookie`和`User Agent`，一模一样的内容，每次请求都必须附带，这会浪费很多带宽，也影响速度。

HTTP/2 对这一点做了优化，引入了==头信息压缩机制（header compression）==。一方面，头信息使用`gzip`或`compress`压缩后再发送；另一方面，客户端和服务器同时维护一张头信息表，所有字段都会存入这个表，生成一个索引号，以后就不发送同样字段了，只发送索引号，这样就提高速度了。

## 服务器推送

HTTP/2 允许服务器未经请求，主动向客户端发送资源，这叫做服务器推送（server push）。

常见场景是客户端请求一个网页，这个网页里面包含很多静态资源。正常情况下，客户端必须收到网页后，解析HTML源码，发现有静态资源，再发出静态资源请求。其实，服务器可以预期到客户端请求网页后，很可能会再请求静态资源，所以就主动把这些静态资源随着网页一起发给客户端了。



## 简介

https://www.ruanyifeng.com/blog/2016/08/migrate-from-http-to-https.html

上一篇文章我介绍了 [HTTP/2 协议](https://www.ruanyifeng.com/blog/2016/08/http.html) ，它只有在 HTTPS 环境才会生效。

为了升级到 HTTP/2 协议，必须先启用 HTTPS。如果你不了解 HTTPS 协议（学名 TLS 协议），可以参考我以前的文章。

> - [《HTTPS 协议概述》](https://www.ruanyifeng.com/blog/2014/02/ssl_tls.html)
> - [《图解 HTTPS 协议》](https://www.ruanyifeng.com/blog/2014/09/illustration-ssl.html)
> - [《HTTPS 协议的七个误解》](https://www.ruanyifeng.com/blog/2011/02/seven_myths_about_https.html)
> - [《HTTPS 协议的延迟有多大？》](https://www.ruanyifeng.com/blog/2014/09/ssl-latency.html)

本文介绍如何将一个 HTTP 网站升级到 HTTPS 。

## 访问

* 如果用户直接访问 http 协议的网站，服务端会在响应时，通过3xx重定向，从`HTTP`页面进入`HTTPS`页面。

  * 实测公司通过 307 ，location 重定向。

* 访问网站时，用户很少直接在地址栏输入`https://`，总是通过点击链接，或者3xx重定向，从`HTTP`页面进入`HTTPS`页面。攻击者完全可以在用户发出`HTTP`请求时，劫持并篡改该请求。

  另一种情况是恶意网站使用自签名证书，冒充另一个网站，这时浏览器会给出警告，但是许多用户会忽略警告继续访问。（这个就是Chrome点击继续访问）

* script 访问修改

  ```markup
  <script src="//foo.com/jquery.js"></script>
  ```

  * 让浏览器自动拼接

## 严格传输安全（HSTS）

=="HTTP严格传输安全"（简称 HSTS）的作用，就是强制浏览器只能发出`HTTPS`请求，并阻止用户接受不安全的证书。==

它在网站的响应头里面，加入一个强制性声明。以下例子摘自[维基百科](https://zh.wikipedia.org/wiki/HTTP严格传输安全)。

> ```http
> Strict-Transport-Security: max-age=31536000; includeSubDomains
> ```

上面这段头信息有两个作用。

> （1）在接下来的一年（即31536000秒）中，浏览器只要向`example.com`或其子域名发送HTTP请求时，必须采用HTTPS来发起连接。用户点击超链接或在地址栏输入`http://www.example.com/`，浏览器应当自动将`http`转写成`https`，然后直接向`https://www.example.com/`发送请求。
>
> （2）在接下来的一年中，如果`example.com`服务器发送的证书无效，用户不能忽略浏览器警告，将无法继续访问该网站。

HSTS 很大程度上解决了 SSL 剥离攻击。只要浏览器曾经与服务器建立过一次安全连接，之后浏览器会强制使用`HTTPS`，即使链接被换成了`HTTP`。

==该方法的主要不足是，用户首次访问网站发出HTTP请求时，是不受HSTS保护的。==



## HTTPS-cookie

另一个需要注意的地方是，确保浏览器只在使用 HTTPS 时，才发送Cookie。

网站响应头里面，`Set-Cookie`字段加上`Secure`标志即可。

> ```http
> Set-Cookie: LSID=DQAAAK...Eaem_vYg; Secure
> ```



## ssl-tls

https://www.ruanyifeng.com/blog/2014/02/ssl_tls.html

### http 的问题

* 对于服务端：**冒充风险**（pretending）：第三方可以冒充他人身份参与通信。
* 对于客户端：**篡改风险**（tampering）：第三方可以修改通信内容。
* 对于通信链路：**窃听风险**（eavesdropping）：第三方可以获知通信内容。

### ssl 期望达到

（1） 所有信息都是**加密传播**，第三方无法窃听。

（2） 具有**校验机制**，一旦被篡改，通信双方会立刻发现。

（3） 配备**身份证书**，防止身份被冒充。



### 非对称加密

所以一种新的加密算法被提出，这就是非对称加密算法。非对称加密使用两个密钥，一个是public key，一个是private key。通过一个特殊的数学算法，使得数据的加密和解密使用不同的密钥。因为用的是不同的密钥，所以称为**非对称加密**。

- 通过CA体系交换public key
- 通过非对称加密算法，交换用于对称加密的密钥
- 通过对称加密算法，加密正常的网络通信



### 思路

SSL/TLS协议的基本思路是采用[公钥加密法](https://en.wikipedia.org/wiki/Public-key_cryptography)，也就是说，客户端先向服务器端索要公钥，==然后用公钥加密信息==，服务器收到密文后，==用自己的私钥解密==。

总结：

* 公钥和私钥全是服务端保存。
* 公钥放在数字证书中，只要证书可信公钥就是可信的。



* 非对称加密时间长，如何减少耗时的时间

  加密对话密钥，用密钥加密信息。

  > 解决方法：每一次对话（session），客户端和服务器端都生成一个"对话密钥"（session key），用它来加密信息。由于"对话密钥"是对称加密，所以运算速度非常快，而服务器公钥只用于加密"对话密钥"本身，这样就减少了加密运算的消耗时间。

* 基本过程

  （1） 客户端向服务器端索要并验证公钥。

  （2） 双方协商生成"对话密钥"。

  （3） 双方采用"对话密钥"进行加密通信。

  上面过程的==前两步==，又称为"握手阶段"（handshake）。

"握手阶段"涉及四次通信，我们一个个来看。需要注意的是，"握手阶段"的所有通信都是明文的。（所以需要三个随机 key）

### 流程

1. **客户端发出请求（ClientHello）**

   （1） 支持的协议版本，比如TLS 1.0版。

   （2） ==一个客户端生成的随机数，稍后用于生成"对话密钥"。==

   （3） 支持的加密方法，比如RSA公钥加密。

   （4） 支持的压缩方法。

2. **服务器回应（SeverHello）**

   （1） 确认使用的加密通信协议版本，比如TLS 1.0版本。如果浏览器与服务器支持的版本不一致，服务器关闭加密通信。

   （2）==一个服务器生成的随机数，稍后用于生成"对话密钥"。==

   （3） 确认使用的加密方法，比如RSA公钥加密。

   （4） ==服务器证书。==

3. **客户端回应**

   （1） ==一个随机数。====该随机数用服务器公钥加密，防止被窃听。==（这个随即数是加密的）

   （2） 编码改变通知，表示随后的信息都将用双方商定的加密方法和密钥发送。

   （3）== 客户端握手结束通知==，表示客户端的握手阶段已经结束。这一项同时也是前面发送的所有内容的hash值，用来供服务器校验。

4. **服务器的最后回应**

   服务器收到客户端的第三个随机数pre-master key之后，==计算生成本次会话所用的"会话密钥"==。然后，向客户端最后发送下面信息。

   （1）编码改变通知，表示随后的信息都将用双方商定的加密方法和密钥发送。

   （2）==服务器握手结束通知，==表示服务器的握手阶段已经结束。这一项同时也是前面发送的所有内容的hash值，用来供客户端校验。

* 这就是为什么通常一台服务器只能有一张数字证书的原因。





# HTTP 应用



## header

* http header 是不区分大小写的
* 建议 开头字母大写



## 响应码

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status

HTTP 响应状态代码指示特定 [HTTP](https://developer.mozilla.org/zh-cn/HTTP) 请求是否已成功完成。响应分为五类：信息响应(`100`–`199`)，成功响应(`200`–`299`)，重定向(`300`–`399`)，客户端错误(`400`–`499`)和服务器错误 (`500`–`599`)。状态代码由 [section 10 of RFC 2616](https://tools.ietf.org/html/rfc2616#section-10)定义



### 1xx

* 101 切换协议



### 2xx

* 200

* 201: 

  > 在 HTTP 协议中，**`201 Created`** 是一个代表成功的应答状态码，表示请求已经被成功处理，并且创建了新的资源。新的资源在应答返回之前已经被创建。同时新增的资源会在应答消息体中返回，其地址或者是原始请求的路径，或者是 [`Location`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Location) 首部的值。

  * 状态码：201 Created
  * 这个状态码的常规使用场景是作为 [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST) 请求的返回值。注意：作为post请求的返回码！！

* 204：服务器成功处理了请求，但不需要返回任何实体内容

  常见于 options 预检的请求

* 206：返回部分内容



### 3xx

* 301 

  被请求的资源已永久移动到新位置，并且将来任何对此资源的引用都应该使用本响应返回的若干个 URI 之一。如果可能，拥有链接编辑功能的客户端应当==自动==把请求的地址修改为从服务器反馈回来的地址。除非额外指定，否则这个响应也是可缓存的。

* 302

  请求的资源现在临时从不同的 URI 响应请求。由于这样的重定向是临时的，客户端应当继续向原有地址发送以后的请求。只有在Cache-Control或Expires中进行了指定的情况下，这个响应才是可缓存的。

  > 1. 搜索引擎需要弄清楚是否保留旧页面和在新位置找到旧页面，如果设置错了重定向的类型，可能会导致网站的流量消失。
  > 2. 当整个网页或网站需要移动到新位置，例如，域名改变，添加扩展名等。301重定向会将浏览器访问的旧地址重定向到新地址。但是Google搜索引擎并不会按照这样的重定向策略，网站移动可能就会触发其老化延迟。导致搜索排名下降，SEO不友好。
  > 3. 当使用302进行重定向时，搜索引擎还是会为就旧的地址编制索引，它会将新的地址视为与其一样的重复的URL，从而导致排名的下降。
  >
  > > 创建302重定向似乎比301重定向容易。
  > > 您可以使用Javascript或meta标记创建302。
  > > 使用Apache服务器，则创建301重定向需要在.htaccess文件中使用特殊命令。
  >
  > 
  >
  > 作者：P13G13
  > 链接：https://zhuanlan.zhihu.com/p/267124206
  > 来源：知乎
  > 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

* 304 缓存

  如果客户端发送了一个带条件的 GET 请求且该请求已被允许，而文档的内容（自上次访问以来或者根据请求的条件）并没有改变，则服务器应当返回这个状态码。==304 响应禁止包含消息体==，因此始终以消息头后的第一个空行结尾。
  
  > HTTP `**304**`**` 未改变`**说明无需再次传输请求的内容，也就是说可以使用缓存的内容。这通常是在一些安全的方法（[safe](https://developer.mozilla.org/zh-CN/docs/Glossary/safe)），例如[`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) 或[`HEAD`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/HEAD) 或在请求中附带了头部信息： [`If-None-Match`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/If-None-Match) 或[`If-Modified-Since`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/If-Modified-Since)。
  >
  > 如果是 [`200`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/200) `OK` ，响应会带有头部 [`Cache-Control`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cache-Control), [`Content-Location`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Location), [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Date), [`ETag`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/ETag), [`Expires`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Expires)，和 [`Vary`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Vary).
  
  200 和 304 的缓存响应不太一样。



### 4xx

* 400:

  1、语义有误，当前请求无法被服务器理解。除非进行修改，否则客户端不应该重复提交这个请求。

  2、请求参数有误。

* 401：无权限

  当前请求需要用户验证。该响应必须包含一个适用于被请求资源的 WWW-Authenticate 信息头用以询问用户信息。客户端可以重复提交一个包含恰当的 Authorization 头信息的请求。如果当前请求已经包含了 Authorization 证书，那么401响应代表着服务器验证已经拒绝了那些证书。如果401响应包含了与前一个响应相同的身份验证询问，且浏览器已经至少尝试了一次验证，那么浏览器应当向用户展示响应中包含的实体信息，因为这个实体信息中可能包含了相关诊断信息

* 403：拒绝访问

  服务器已经理解请求，但是拒绝执行它。与 401 响应不同的是，身份验证并不能提供任何帮助，而且这个请求也不应该被重复提交。如果这不是一个 HEAD 请求，而且服务器希望能够讲清楚为何请求不能被执行，那么就应该在实体内描述拒绝的原因。当然服务器也可以返回一个 404 响应，假如它不希望让客户端获得任何信息。

* 404

* 405 Method Not Allowed

  客户端请求方式不被允许



### 5xx

* 500

  服务器遇到了不知道如何处理的情况。

* 501

  请求方法不被服务器支持，无法处理

* 502

  就是网关层错误，网关的下一层错误

  此错误响应表明服务器作为网关需要得到一个处理这个请求的响应，但是得到一个错误的响应。也就是真正的服务器出错了

* 503

  服务器没有准备好处理请求。 常见原因是服务器因维护或重载而停机。

* 504

  网关层本身返回的错误，响应时长超过网关设置的超时，网关超时。

  当服务器作为网关，不能及时得到响应时返回此错误代码。



## 缓存

缓存的种类有很多,其大致可归为两类：私有与共享缓存。共享缓存存储的响应能够被多个用户使用。私有缓存只能用于单独用户。本文将主要介绍浏览器与代理缓存，除此之外还有网关缓存、CDN、反向代理缓存和负载均衡器等部署在服务器上的缓存方式，为站点和 web 应用提供更好的稳定性、性能和扩展性。

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Caching

https://zhuanlan.zhihu.com/p/93163523

普遍的缓存案例:

- 一个检索请求的成功响应: 对于 [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET)请求，响应状态码为：[`200`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/200)，则表示为成功。一个包含例如HTML文档，图片，或者文件的响应。
- 永久重定向: 响应状态码：[`301`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/301)。
- 错误响应: 响应状态码：[`404`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/404) 的一个页面。
- 不完全的响应: 响应状态码 [`206`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/206)，只返回局部的信息。
- 除了 [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) 请求外，如果匹配到作为一个已被定义的cache键名的响应。



### cache-control 

` **Cache-Control**` 通用消息头字段，被用于在http请求和响应中，通过指定指令来实现缓存机制。缓存指令是==单向的==，这意味着在请求中设置的指令，不一定被包含在响应中。

指定 `no-cache` 或 `max-age=0, must-revalidate` 表示客户端可以缓存资源（客户端不做缓存检查），每次使用缓存资源前都必须重新验证其有效性。这意味着每次都会发起 HTTP 请求，==但当缓存内容仍有效时可以跳过 HTTP 响应体的下载。（因为是响应的时候收到的，浏览器只能取不下载）==

* 也就是不能使用强缓存了，使用协商缓存试试。

**注意**: 如果服务器关闭或失去连接，下面的指令可能会造成使用缓存。

```
Cache-Control: max-age=0
```

用来控制缓存：

```js
// 没有缓存
Cache-Control: no-store

// 需要请求验证，在发布缓存副本之前，强制要求缓存把请求提交给原始服务器进行验证(协商缓存验证)。304
// 如下头部定义，此方式下，每次有请求发出时，缓存会将此请求发到服务器（译者注：该请求应该会带有与本地缓存相关的验证字段），服务器端会验证请求中所描述的缓存是否过期，若未过期（注：实际就是返回304），则缓存才使用本地缓存副本。
Cache-Control: no-cache

// 私有和公共缓存，能否被中间代理缓存
Cache-Control: private
Cache-Control: public



```

* 过期

  过期机制中，最重要的指令是 "`max-age=<seconds>`"，表示资源能够被缓存（保持新鲜）的最大时间。==相对[Expires](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Expires)而言，max-age是距离请求发起的时间的秒数。==针对应用中那些不会改变的文件，通常可以手动设置一定的时长以保证缓存有效，例如图片、css、js等静态资源。

  ```js
  Cache-Control: max-age=31536000
  ```

* pragma

  用户请求时的 header ，类似于 cache-control



### 缓存控制字段

响应头里的缓存控制：

`Cache-control: max-age=N` > Expires(Expires 与 Date 比较) > Last-modified (与Date 比较)

> 比如`Cache-control: max-age=N`的头，相应的缓存的寿命就是`N`。通常情况下，对于不含这个属性的请求则会去查看是否包含[Expires](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Expires)属性，通过比较Expires的值和头里面[Date](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Date)属性的值来判断是否缓存还有效。如果max-age和expires属性都没有，找找头里的[Last-Modified](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Last-Modified)信息。如果有，缓存的寿命就等于头里面Date的值减去Last-Modified的值除以10（注：根据rfc2626其实也就是乘以10%

* 相对于请求时间，由浏览器去保存。

* 强缓存：`Cache-control: max-age=N` 、Expires。返回 200， 会跳过响应体的下载。

   ![Show how a proxy cache acts when a doc is not cache, in the cache and fresh, in the cache and stale.](https://mdn.mozillademos.org/files/13771/HTTPStaleness.png)

   * 如上图所示，如果 过了 max-age  的保质期，需要发送新的请求，服务端会返回 304 响应码，然后客户端会继续缓存。





* 协商缓存需要 设置 no-cache。协商缓存是在强缓存的基础上，去验证后期的缓存是否需要更新。

   If-None-Match 或 If-Modified-Since 与 [ETag](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/ETag) 去做diff

  `**ETag**`HTTP响应头是资源的特定版本的标识符

* 不论什么缓存都需要请求一下后端接口，做一下 diff ，唯一区别就是是否需要下载消息体。



### 缓存验证

作为缓存的一种强校验器，==[`ETag`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/ETag)是一个对用户代理(User Agent, 下面简称UA)不透明的值。==对于像浏览器这样的HTTP UA，不知道ETag代表什么，不能预测它的值是多少。如果资源请求的响应头里含有ETag, 客户端可以在后续的请求的头中带上 [`If-None-Match`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/If-None-Match)

[`Last-Modified`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Last-Modified)为它只能精确到一秒。如果响应头里含有这个信息，客户端可以在后续的请求中带上 [`If-Modified-Since`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/If-Modified-Since)

* 响应头有 Etag ，客户端才会在后续携带

* Etag（服务端） 对应 if-none-match（客户端）

  Last-modified（服务端） 对应 if-modified-since（客户端）



==当向服务端发起缓存校验的请求时，服务端会返回 [`200`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/200) ok表示返回正常的结果或者 [`304`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/304) Not Modified(不返回body)表示浏览器可以使用本地缓存文件。304的响应头也可以同时更新缓存文档的过期时间。==

* 304 能够更新过期时间



#### 注意：

1. cache-control 能够设置 max-age
2. Expires 是一个单独的响应头
3. 

#### vary 响应头

* 切换不同的版本。比如说PC和移动端使用不同的版本。
* 当缓存服务器收到一个请求，只有当前的请求和原始（缓存）的请求头跟缓存的响应头里的Vary都匹配，才能使用缓存的响应。

这便是 Vary 头字段的作用：让代理服务器的缓存命中更多的决定因子，而不仅仅是依据请求 URL 和请求方法来决定是否命中。

> `Vary` 响应头是用来指示缓存系统在缓存资源时应该考虑的请求头的列表。它告诉缓存系统，在决定是否使用缓存中的响应时，需要检查请求中的哪些头部字段。
>
> 
>
> 具体来说，当服务器返回带有 `Vary` 响应头的响应时，它告诉缓存系统对特定的请求头进行了区分。缓存系统会检查这些请求头，如果请求中的这些头部字段的值不匹配，缓存系统就不会使用缓存中的响应，而是会向源服务器发送新的请求。
>
> 
>
> 例如，如果服务器返回的响应头中包含 `Vary: Accept-Encoding`，这意味着服务器对请求中的 Accept-Encoding 头进行了区分。如果两个请求的 Accept-Encoding 头的值不同，那么缓存系统就不会使用缓存中的响应，而是会向服务器发送新的请求。
>
> 
>
> `Vary` 响应头通常用于缓存内容协商（Content Negotiation），比如根据不同的 Accept-Language、User-Agent 等头部字段提供不同的响应。它也可以用于根据客户端的特定请求头定制响应，例如根据请求中的 Cookie 头提供个性化的内容。



### 为什么浏览器地址栏输入的地址不能命中强缓存？

> 是因为在地址栏输入URL并按下回车后，浏览器会发起一个新的网络请求，这个请求会按照缓存策略来判断是否需要使用缓存。
>
> 
>
> HTTP缓存策略包括强缓存和协商缓存两种类型。强缓存是指浏览器在请求资源时，在不与服务器进行通信的情况下，直接从本地缓存中获取资源。这是通过检查缓存中的一些响应头来实现的，例如`Cache-Control`和`Expires`。
>
> 
>
> 而在浏览器导航栏输入地址时，浏览器会认为用户有意要获取最新的页面内容，因此会忽略强缓存，而直接向服务器发送请求，以获取最新的内容。这意味着即使之前已经在缓存中存储了相同的资源，并且缓存仍然有效，浏览器也会忽略缓存，而发起一个新的网络请求。
>
> 
>
> 协商缓存是另一种缓存策略，它是在浏览器向服务器发送请求时，通过比较资源的标识符来决定是否使用缓存。这种缓存策略可以允许服务器确定资源是否已经发生了变化，如果没有变化，服务器会返回一个状态码来告知浏览器可以使用缓存中的资源。
>
> 
>
> 总之，在浏览器导航栏输入地址时，浏览器会忽略强缓存，直接向服务器发起请求，以便获取最新的页面内容。这是为了确保用户能够获得最新的页面，而不是使用可能已经过期的缓存。

* 以上由 chatGpt 回答

### 协商缓存

* 协商缓存一般关掉强缓存，比如设置 max-age 为0

### webpack 强缓存

* 每次的文件名都是变化的（chunk hash），那么只要入口设置强缓存为0，js文件强缓存为永不过期就可以了。只要文件变化，路径就会不同！

#### 策略1

通过静态文件拼接 `?v=hash` 来控制。

#### 策略2

通过控制静态文件名来控制，`.hash.js` 



# header 字段解析

## http 头部列表

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers

## 0. 禁止修改的header

https://developer.mozilla.org/zh-CN/docs/Glossary/Forbidden_header_name

todo

## 1. origin

请求首部字段 **`Origin`** 指示了==请求来自于哪个站点==。该字段仅指示服务器名称，并不包含任何路径信息。该首部用于 [CORS](https://developer.mozilla.org/zh-CN/docs/Glossary/CORS) 请求或者 [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST)了不包含路径信息，该字段与 [`Referer`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Referer) 有一点区别，除了在携带场景的区别外，改字段不会暴露 URL 的path部分。

如下请求可能携带：

- [跨源](https://developer.mozilla.org/zh-CN/docs/Glossary/CORS)请求。
- 除 [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) 和 [`HEAD`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/HEAD) 以外的[同源](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)请求（即它会被添加到同源的 [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST)、[`OPTIONS`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/OPTIONS)、[`PUT`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/PUT)、[`PATCH`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/PATCH) 和 [`DELETE`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/DELETE) 请求中）。

## 2. referer

* `**Referer**` 请求头包含了==当前请求页面==的==来源页面==的地址，即表示当前页面是通过此来源页面里的链接进入的。服务端一般使用 `Referer` 请求头识别访问来源，可能会以此进行统计分析、日志记录以及缓存优化等。

### 场景

如下场景不会被发送：（也就是大部分场景会被发送）

- 来源页面采用的协议为表示本地文件的 "file" 或者 "data" URI；
- 当前请求页面采用的是非安全协议，而来源页面采用的是安全协议（HTTPS）。

### 用来判断跳转的来源

* 可以用来做跨域请求伪造的封禁

### 详细教程

参考：https://www.ruanyifeng.com/blog/2019/06/http-referer.html

## 3. Content-Type

* 常见content-type 参考：https://zhuanlan.zhihu.com/p/127800327



## 4. Content-Disposition

* 能够控制内容是否下载

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Disposition

Content-disposition 在http应答中能够指示回复的内容该以何种形式展示

* 内联的形式（网页和页面的一部分）
* 附件形式下载并保存到本地

> ```
> Content-Disposition: inline
> Content-Disposition: attachment
> Content-Disposition: attachment; filename="filename.jpg"
> ```

### 作为content-type: multipart/form-data上传时标头

> 当使用 `multipart/form-data` 格式提交表单数据时，每个子部分（例如每个表单字段和任何与字段数据相关的文件）都需要提供一个 `Content-Disposition` 标头，以提供相关信息。标头的第一个指令始终为 `form-data`，并且还*必须*包含一个 `name` 参数来标识相关字段。额外的指令不区分大小写，并使用带引号的字符串语法在 `=` 号后面指定参数。多个参数之间使用分号（`;`）分隔。

```tex
------WebKitFormBoundarymMprzx6JFPPz2xdE Content-Disposition: form-data; name="file"; filename="WechatIMG106155.jpeg" Content-Type: image/jpeg  ------WebKitFormBoundarymMprzx6JFPPz2xdE Content-Disposition: form-data; name="filename" test.png ------WebKitFormBoundarymMprzx6JFPPz2xdE--
```

## 5. Accept-Ranges

> 服务器使用 HTTP 响应头 **`Accept-Ranges`** 标识自身支持范围请求 (partial requests)。字段的具体值用于定义范围请求的单位。
>
> 当浏览器发现`Accept-Ranges`头时，可以尝试*继续*中断了的下载，而不是重新开始。

* 值 bytes & none 

## 6. referer

> **`Referer`** 请求头包含了当前请求页面的来源页面的地址，即表示当前页面是通过此来源页面里的链接进入的。服务端一般使用 `Referer` 请求头识别访问来源，可能会以此进行统计分析、日志记录以及缓存优化等。

* 当前请求的页面的来源页面地址；服务端一般使用 Referer 请求头来识别访问来源（也可作为图片防盗策略）
* 主要是来源

### Referer 不会被发送的场景

是一种浏览器行为；

- 来源页面采用的协议为表示本地文件的 "file" 或者 "data" URI；
- 当前请求页面采用的是非安全协议，而来源页面采用的是安全协议（HTTPS）。



# HTTP 基础

## 1. MIME 类型

* https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/MIME_types

  **媒体类型**（通常称为 **Multipurpose Internet Mail Extensions** 或 **MIME** 类型 ）是一种标准，用来表示文档、文件或字节流的性质和格式。它在[IETF RFC 6838](https://tools.ietf.org/html/rfc6838)中进行了定义和标准化。

* 能够标记
  * 文件  input:file 
  * 文档 http 请求文档
  * 字节流

## 2. http 范围请求

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Range_requests

todo 断点续传相关

https://juejin.cn/post/6844903588825825294

https://juejin.cn/post/6844903577882722317#heading-0



# 实践

## 1. get请求能带请求体吗？post请求参数能在queryString中合理吗？

* body 在chrome里也叫 payload

> POST 请求用的 URL 也可以带参数，这是完全合乎规范且有时候很有用的，比如想在请求日志里暴露出一些参数方便 grep。
>
> GET 和 POST 最明显的区别就是能不能带 body，这也是经常被提起的一个问题，因为很多人认为 GET 请求也可以带 body。说这个问题前，先来改一下术语 body 的叫法 ，body 也可以叫 payload，还有叫 entity 的，现在最新的 HTTP 规范里已经改叫 content，关于 GET 请求能不能带 content，规范里是这么说的：
>
> 
>
> 作者：紫云飞
> 链接：https://www.zhihu.com/question/549907469/answer/2654938556
> 来源：知乎
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



## 2. 代理-http proxy

### 获取真实的请求ip

* 代理服务器访问server，默认拿到代理服务器的IP。如果需要拿到真实的客户端请求IP，必须开启 x-forwarded- 请求头，携带真实的请求IP

> *如果开启了这个x-forwarded，则能在服务端拿到 'x-forwarded-for': '::ffff:10.236.140.5' 就是当前ip*



# https 相关

## 本地开发配置https

参考：https://juejin.cn/post/6847902224270491662

## https 加密过程

### 过程以及由来

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

## SSL延迟

https://www.ruanyifeng.com/blog/2014/09/ssl-latency.html

* https 要比 http 慢2~100倍。

> HTTP耗时 = TCP握手
>
> HTTPs耗时 = TCP握手 + SSL握手



## https 本地配置

### Gatsby 实践

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

### 其他方式



## https 配置

### 配置文件后缀

> - CRT后缀文件是Certificate的简称，可能是PEM编码格式，也可能是DER编码格式。进行证书格式转换前请仔细确认您的证书格式是否需要转换。
> - PEM（Privacy Enhanced Mail）一般为文本格式，以 “-----BEGIN ***-----”开头，以 “-----END ***-----结尾”，中间的内容是Base64编码。这种格式可以保存证书和私钥，为了区分证书与私钥，一般会将PEM格式的私钥后缀改为`.key`。

### 配置步骤

推荐使用 freessl 网站，可以自动续签

https://freessl.cn/

步骤参考：

https://blog.freessl.cn/acme-quick-start/

https://www.cnblogs.com/nuccch/p/16483136.html

### nginx https配置

* Nginx 需要安装ssl模块
* 然后配置 正确的key和cert

参考：https://www.cnblogs.com/ambition26/p/14077773.html

将http默认转发到https



### 我的网站实践

使用 freessl 申请

然后执行如下命令

```bash
acme.sh --issue -d programmerzbb.icu -d www.programmerzbb.icu  --dns dns_dp --force --server https://acme.freessl.cn/v2/DV90/directory/y3ku5f7vq3squ8zzynfv --reloadcmd "sudo docker restart nginx"
```

* 更新证书之后自动重启docker容器



# http 缓存相关

参考：

* https://zhuanlan.zhihu.com/p/93163523
* https://www.jianshu.com/p/54cc04190252
* https://juejin.cn/post/6844903747357769742?utm_source=gold_browser_extension#heading-3

大部分接口都应该选择好的缓存策略，通常浏览器缓存策略分为两种：强缓存和协商缓存，并且都是通过http header来实现的。

## 概述

> 可复用性有几个优点。首先，由于不需要将请求传递到源服务器，因此客户端和缓存越近，响应速度就越快。最典型的例子是浏览器本身为浏览器请求存储缓存。
>
> 此外，当响应可复用时，源服务器不需要处理请求——因为它不需要解析和路由请求、根据 cookie 恢复会话、查询数据库以获取结果或渲染模板引擎。这减少了服务器上的负载。

* 廉价的、快速的

## http caching 标准

* http caching 标准中，有两种不同的类型缓存：私有缓存和共享缓存

### 私有缓存

> 私有缓存是绑定到特定客户端的缓存——通常是浏览器缓存。由于存储的响应不与其他客户端共享，因此私有缓存可以存储该用户的个性化响应。
>
> 另一方面，如果个性化内容存储在私有缓存以外的缓存中，那么其他用户可能能够检索到这些内容——这可能会导致无意的信息泄露。

如果响应内容包含个性化内容并且你只想要响应存储在私有的缓存中，则必须指定 private 指令；

```
Cache-Control: private
```



## 缓存过程分析

浏览器与服务器通信方式为应答模式，浏览器对资源的缓存策略是根据第一次请求资源时返回的响应头来确定的。

* 发起请求前浏览器会在缓存中查找该请求的结果以及缓存标识，发起请求前找缓存
* 每次拿到返回请求结果都会将该结果和缓存标识存入到浏览器缓存中。请求后保存缓存

## 缓存规则

* 根据是否需要向服务端重新发送http请求将缓存分为：强缓存和协商缓存。

1、浏览器在加载资源时，先根据这个资源的一些http header判断它是否命中强缓存，强缓存如果命中，浏览器直接从自己的缓存中读取资源，不会发请求到服务器。比如某个css文件，如果浏览器在加载它所在的网页时，这个css文件的缓存配置命中了强缓存，浏览器就直接从缓存中加载这个css，连请求都不会发送到网页所在服务器。

2、当强缓存没有命中的时候，浏览器一定会发送一个请求到服务器，通过服务器端依据资源的另外一些http header验证这个资源是否命中协商缓存，如果协商缓存命中，服务器会将这个请求返回，但是不会返回这个资源的数据，而是告诉客户端可以直接从缓存中加载这个资源，于是浏览器就又会从自己的缓存中去加载这个资源。

> 强缓存与协商缓存的共同点是：如果命中，都是从客户端缓存中加载资源，而不是从服务器加载资源数据；区别是：强缓存不发请求到服务器，协商缓存会发请求到服务器。

* 就是这个缓存需不需要问一下服务端是否过期。
* 如果没有命中协商缓存，直接响应200code码。

## 强缓存

* 不会向服务端发送请求，直接从缓存中读取，在控制台中能看到200的响应，并且size一栏展示 memory chache或者disk cache。
* 强缓存可以通过设置 Header: expires 和 cache-control 实现。

### 1. expires

缓存过期时间，用来指定资源到期时间，是服务端的具体时间点。

> Expires是http1.0提出的一个表示资源过期时间的header，它描述的是一个绝对时间，由服务器返回，用GMT格式的字符串表示，如：Expires:Thu, 31 Dec 2037 23:55:55 GMT

### 缓存原理

* 拿第一次请求缓存的 expires 和当前时间进行对比，如果命中则从缓存中获取；如果没命中从服务器加载资源，更新expires。

#### 缺点

* 返回一个绝对时间，在服务端和客户端时间差距较大时，缓存容易出现问题；另外可以通过随意修改客户端的时间影响缓存命中结果。是http1.0的产物，所以在http1.1的时候提出了 cache-contorl 是一个相对时间。

### 2. cache-control

http 1.1的产物，浏览器的下次请求会以第一次响应的时间+cache-control过期时间进行对比。

> 浏览器再请求这个资源时，先从缓存中寻找，找到这个资源后，根据它第一次的请求时间和Cache-Control设定的有效期，计算出一个资源过期时间，再拿这个过期时间跟当前的请求时间比较，如果请求时间在过期时间之前，就能命中缓存，否则就不行

#### 和 expires 混用

绝对值和相对值的区别。

* 一般情况适用一个即可，也可以同时使用两个

  cache-control 优先级大于 expires，高版本有限，低版本兼容。

#### 取值

- **public**：所有内容都将被缓存（客户端和代理服务器都可缓存）

- **private**：所有内容只有客户端可以缓存，**Cache-Control的默认取值**

- **no-cache**：客户端缓存内容，但是是否使用缓存则需要经过协商缓存来验证决定

  需要协商！！！

- **no-store**：所有内容都不会被缓存，即不使用强制缓存，也不使用协商缓存

- **max-age=xxx (xxx is numeric)**：缓存内容将在xxx秒后失效。

  是一个相对值，存活毫秒数。

#### stale-while-revalidate

* 参考：https://zhuanlan.zhihu.com/p/64694485

  文档查看 swr.md 

### 应用

* ==注意：强缓存不会在地址栏输入同步请求的时候起作用，只会在脚本、异步请求其作用==

  如上特性，那么在静态服务器就可以设置所有资源的过期时间为永不过期，根据path 的hash值去拿到最新的资源，首页永远不会被缓存。
  
  首页永远不缓存那么它依赖的静态资源就可以每次随着首页的更新而更新（或缓存）。

express 配置

```typescript
app.get('/static/test.txt', (req, res) => {
  try {
    fs.accessSync('./public/test.txt')
    const readable = fs.createReadStream('./public/test.txt')
    // 测试强缓存
    // res.setHeader('Expires', 'Thu, 27 Apr 2023 12:02:04 GMT')
    // 注意书写，如果末尾没有其他配置，不能加 ;
    res.setHeader('Cache-Control', 'max-age=31536000')
    readable.pipe(res)
  } catch (err) {
      res.status(404).send('not found')
  }
})
```

#### 1. 静态资源配置

> 大家都知道现在大部分静态资源都是非覆盖式发布，发一个新版本就会去改变这个资源 URL 中的版本号或者 hash 值，这种情况下，该资源的缓存时长会设置特别大，比如一年、三年甚至十年，在它到期之前，早就已经不在线上使用了，也就是说它永远都不会过期。

* 目前的静态资源都是非覆盖式的，发布新版本就会改变资源的url中的版本号或者hash值。

## 协商缓存

* 如果到了缓存时间，服务端文件并没有发生改变，再去拿响应就会造成浪费。这个时候就需要协商缓存了。

* **协商缓存就是强制缓存失效后，浏览器携带缓存标识向服务器发起请求，由服务器根据缓存标识决定是否使用缓存的过程**

  重点就是缓存标识的识别！

### 标识

* last-modified / **If-Modified-Since**
* **Etag / If-None-Match**

### 流程

#### **Last-Modified / If-Modified-Since**

* last-modified 是服务端响应的资源最后修改时间
* If-Modified-Since 是客户端再次请求时，携带上次请求返回的 last-modified 的值，通过此字段值告诉服务端该资源上次请求返回的最后修改时间。

> 服务器收到该请求，发现请求头含有If-Modified-Since字段，则会根据If-Modified-Since的字段值与该资源在服务器的最后被修改时间做对比，若服务器的资源最后被修改时间大于If-Modified-Since的字段值，则重新返回资源，状态码为200；否则则返回304，代表资源无更新，可继续使用缓存文件

缺点：

* 可能出现服务端资源有变化，而最后修改时间没有变化的情况。

##### 注意

> 如果响应头中有 Last-modified 而没有 Expire 或 Cache-Control 时，浏览器会有自己的算法来推算出一个时间缓存该文件多久，不同浏览器得出的时间不一样，所以 Last-modified 要记得配合 Expires/Cache-Control 使用。

* 因为浏览器有缓存的默认行为，建议配合 expires 或 cache-control 使用！

#### **Etag / If-None-Match**

* Etag是服务器响应请求时，返回当前资源文件的一个唯一标识(由服务器生成)
* If-None-Match是客户端再次发起该请求时，携带上次请求返回的唯一标识Etag值，通过此字段值告诉服务器该资源上次请求返回的唯一标识值。

> Tips: etag 是对比的方式，因此用的是 match。



* 如果匹配成功则返回 304 code，客户端去使用缓存；如果匹配失败，则返回200code，客户端重新请求。

### 优先级

Etg / If-none-match 的优先级高于 last-modified / If-modified-since。

* 最新版本的优先级一定最高

### 注意

* 首页（地址栏输入的地址）能够被缓存，和强缓存的首页不缓存不一样。

### 区别

- 某些服务器不能精确得到资源的最后修改时间，这样就无法通过最后修改时间判断资源是否更新。
- Last-modified 只能精确到秒。
- 一些资源的最后修改时间改变了，但是内容没改变，使用 Last-modified 看不出内容没有改变。
- Etag 的精度比 Last-modified 高，属于强验证，要求资源字节级别的一致，优先级高。如果服务器端有提供 ETag 的话，必须先对 ETag 进行 Conditional Request。

**注意**：实际使用 ETag/Last-modified 要注意保持一致性，做负载均衡和反向代理的话可能会出现不一致的情况。计算 ETag 也是需要占用资源的，如果修改不是过于频繁，看自己的需求用 Cache-Control 是否可以满足。

* etag 更精确一些，但是需要不同分布式服务间的同步；计算占用资源；

### 实际应用

#### 协商缓存和强缓存配合

* 一般都需要这两种缓存配合使用，没有命中强缓存的走协商缓存。

#### 在微服务中

* 微服务中一般不推荐使用 etag 的方式，因为每个服务生产的 etag 可能不一样。

  同理负载均衡的服务器也有可能出现 etag 不一样的情况。

> 分布式系统里多台机器间文件的Last-Modified必须保持一致，以免负载均衡到不同机器导致比对失败；
>
> 
>
> 分布式系统尽量关闭掉ETag(每台机器生成的ETag都会不一样）；

## 浏览器开启 disable cache或者强制请求

* 浏览器开启这个之后只会出现200的请求，客户端请求头中不再携带强缓存和协商缓存的标头。



# http 重定向

> **URL 重定向**（也称为 *URL 转发*）是一种为页面、表单或者整个 Web 站点/应用提供多个 URL 地址的技术。HTTP 对此操作有一种特殊类型的响应，称为 ***HTTP 重定向***（HTTP redirect）。

* 页面、表单、整个web站点/应用 三种场景。

重定向可实现许多目标：

* 站点维护或停机期间的临时重定向
* 永久重定向将在更改站点的URL后，保留现有的连接/书签、上传文件时表示进度的页面等。

## 原理

> 在 HTTP 协议中，重定向操作由服务器向请求发送特殊的重定向响应而触发。重定向响应包含以 `3` 开头的[状态码](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status)，以及 [`Location`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Location) 标头，其保存着重定向的 URL。

* Location 头
* 状态码为 3 开头

浏览器行为：

> 浏览器在接收到重定向时，它们会立刻加载 `Location` 标头中提供的新 URL。除了额外的往返操作中会有一小部分性能损失之外，重定向操作对于用户来说是不可见的。

## 永久重定向

> 这种重定向操作是永久性的。它表示原 URL 不应再被使用，而选用新的 URL 替换它。搜索引擎机器人、RSS 阅读器以及其他爬虫将更新资源原始的 URL。

* 说白了就是利于搜索引擎。

| 状态码 | 状态文本             | 处理方法                                                     | 典型应用场景                       |
| :----- | :------------------- | :----------------------------------------------------------- | :--------------------------------- |
| `301`  | `Moved Permanently`  | [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) 方法不会发生变更。其他方法有可能会变更为 [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) 方法。[1] | 网站重构。                         |
| `308`  | `Permanent Redirect` | 方法和消息主体都不发生变化。                                 | 使用用于非 GET 链接/操作重组网站。 |

[1] 该规范无意使方法发生改变，但在实际应用中用户代理会更改其方法。[`308`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/308) 状态码被创建用来消除在使用非 `GET` 方法时行为的歧义。

## 临时重定向

> 搜索引擎和其他爬虫不会记录新的、临时的 URL。在创建、更新或者删除资源的时候，临时重定向也可以用于显示临时性的进度页面。

* 搜索引擎不友好

| 状态码 | 状态文本             | 处理方法                                                     | 典型应用场景                                                 |
| :----- | :------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| `302`  | `Found`              | [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) 方法不会发生变更。其他方法有可能会变更为 [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) 方法。[2] | 由于不可预见的原因该页面暂不可用。                           |
| `303`  | `See Other`          | [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) 方法不会发生变更，其他方法会*变更*为 `GET` 方法（消息主体丢失）。 | 用于 [`PUT`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/PUT) 或 [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST) 请求完成之后重定向，来防止由于页面刷新导致的操作的重复触发。 |
| `307`  | `Temporary Redirect` | 方法和消息主体都不发生变化。                                 | 由于不可预见的原因该页面暂不可用。当站点支持非 `GET` 方法的链接或操作的时候，该状态码优于 302 状态码。 |

[2] 该规范无意使方法发生改变，但在实际应用中用户代理会改变其方法。[`307`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/307) 状态码被创建用来消除在使用非 `GET` 方法时行为的歧义。

## 特殊重定向

[`304`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/304)（Not Modified）会使页面跳转到本地的缓存副本中（可能已过时），而 [`300`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/300)（Multiple Choice）则是一种手动重定向：将消息主体以 Web 页面形式呈现在浏览器中，列出了可能的重定向链接，用户可以从中进行选择。

# http 压缩

## gzip 的方式

```typescript
// 测试 gzip 的压缩方式

import express from 'express'

const app = express()

app.use('/static', express.static('../public', {
  dotfiles: 'ignore', // 忽略以 . 开头的文件
  etag: false,
  maxAge: 0,
  setHeaders: res => {
    // 测试gzip。压缩文件只需要使用 gzip 的方式压缩，然后告诉客户端使用的压缩方式。不需要修改后缀名。
    res.setHeader('Content-Encoding', 'gzip')
  },
}))

app.listen(8090)
```

# 跨源资源共享（CORS）

> **跨源资源共享**（[CORS](https://developer.mozilla.org/zh-CN/docs/Glossary/CORS)，或通俗地译为跨域资源共享）是一种基于 [HTTP](https://developer.mozilla.org/zh-CN/docs/Glossary/HTTP) 头的机制，该机制通过允许服务器标示除了它自己以外的其他[源](https://developer.mozilla.org/zh-CN/docs/Glossary/Origin)（域、协议或端口），使得浏览器允许这些源访问加载自己的资源

* 一种http头机制，当前服务器标示自己以外的源（主要是域、协议、端口的不同），从而使得这些源能够加载自己的资源。

* 所指的域是指一级二级域名相同！

* 跨源资源共享还通过一种机制来检查服务器是否会允许要发送的真实请求，该机制通过浏览器发起一个到服务器托管的跨源资源的“预检”请求。在预检中，浏览器发送的头中标示有 HTTP 方法和真实请求中会用到的头。

  通过预检来获取允许使用的http方法和是否允许发送真实的请求。

## cors 机制

* cors 更是一种机制来允许web应用服务器进行跨源访问控制。

## 功能概述

跨源资源共享标准新增了一组 http 标头字段，用来表明服务器声明哪些网站通过浏览器有权访问哪些资源。

* 对于那些对服务器数据产生副作用的http请求方法（特别是 [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) 以外的 HTTP 请求，或者搭配某些 [MIME 类型](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/MIME_types)的 [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST) 请求），浏览器必须首先使用 OPTIONS 方法发起一个预检请求（preflight request），==从而获知服务端是否允许该跨源请求。==

  ==对于可能对服务端数据产生副作用的请求（比如post请求），需要预检。==

> 服务器确认允许之后，才发起实际的 HTTP 请求。在预检请求的返回中，服务器端也可以通知客户端，是否需要携带身份凭证（例如 [Cookie](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies) 和 [HTTP 认证](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Authentication)相关数据）。

* 预检的响应头中也可以通知客户端是否需要携带身份凭证。

cors 请求失败可能产生错误，但是为了安全，开发者不能直接通过JavaScript代码层面获取到底哪里出错了，只能通过浏览器的控制台查看错误。（因此，不能在运行时进行错误处理，一锤子买卖，必须调通）

### 简单请求

* 简单请求不会触发 CORS 预检，直接响应来进行跨源限制。简单请求是拿到服务器的响应之后，浏览器没有把响应给开发者，而是报错。

若满足所有以下条件，则该请求可视为简单请求：

- 使用下列方法之一：

  - [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET)
  - [`HEAD`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/HEAD)
  - [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST)

- http 标头的限制，最好是属于http 1.1标头的一部分，如果存在自定义的标头肯定不属于简单请求。

  除了被用户代理自动设置的标头字段（例如`Connection`、`User-Agent`或其他在 Fetch 规范中定义为禁用标头名称的标头），允许人为设置的字段为 Fetch 规范定义的对 CORS 安全的标头字段集合

  。该集合为：

  - [`Accept`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept)
  - [`Accept-Language`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept-Language)
  - [`Content-Language`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Language)
  - [`Content-Type`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type)（需要注意额外的限制）
  - [`Range`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Range)（只允许[简单的范围标头值](https://fetch.spec.whatwg.org/#simple-range-header-value) 如 `bytes=256-` 或 `bytes=127-255`）

* `Content-Type`标头所指定的媒体类型的值仅限于下列三者之一：(不限制任何方法，只能是这三种之一。)

  - `text/plain`
  - `multipart/form-data` 
  - `application/x-www-form-urlencoded`

  看来 application/json 必然会触发预检，只有formdata或者纯文本的请求能够避免。

### 预检请求

> 与[简单请求](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS#简单请求)不同，“需预检的请求”要求必须首先使用 [`OPTIONS`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/OPTIONS) 方法发起一个预检请求到服务器，以获知服务器是否允许该实际请求。"预检请求“的使用，可以避免跨域请求对服务器的用户数据产生未预期的影响。

* 就像协商缓存一样，需要先去发送预检请求获知是否允许发送请求，这样就能避免跨域请求直接修改数据的风险。

#### 预检请求流程

预检请求的时候自动携带的关于跨源请求的header:

* origin
* Access-Control-Request-Method
* Access-Control-Request-Header: 这个就是自定义的header

响应头的设置：

响应码：204 No Content

```text
Access-Control-Allow-Origin: https://foo.example
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER, Content-Type // 客户端能够携带的头
Access-Control-Max-Age: 86400
Access-Control-Allow-Credentials: true // 是否可以携带证书
Access-Control-Expose-Headers  // 客户端能够拿到的header
```

* Access-control-max-age 规定时间内不需要重新发送预检，默认5秒

  > 给定了该预检请求可供缓存的时间长短，单位为秒，默认值是 5 秒。

服务端是控制方，因此它的字段为 control-allow，是否允许

客户端是请求方，所以它的字段为 control-request

### 预检请求和重定向

并不是所有浏览器都支持预检请求的重定向。如果一个预检请求发生了重定向，一部分浏览器将报告错误：

在浏览器的实现跟上规范之前，有两种方式规避上述报错行为：

- 在服务端去掉对预检请求的重定向；
- 将实际请求变成一个[简单请求](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS#简单请求)。

## 附带身份凭证的请求

> [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 或 [Fetch](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API) 与 CORS 的一个有趣的特性是，可以基于 [HTTP cookies](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies) 和 HTTP 认证信息发送身份凭证。一般而言，对于跨源 `XMLHttpRequest` 或 [Fetch](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API) 请求，浏览器**不会**发送身份凭证信息。如果要发送凭证信息，需要设置 `XMLHttpRequest` 对象的某个特殊标志位，或在构造 [`Request`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request) 对象时设置。

客户端需要设置 withCredentials 为true。

* 简单请求，如果响应没有携带 [`Access-Control-Allow-Credentials`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Access-Control-Allow-Credentials)`: true`，则浏览器不会把响应结果给发送者。



# http cookie

> HTTP Cookie（也叫 Web Cookie 或浏览器 Cookie）是服务器发送到用户浏览器并保存在本地的一小块数据。浏览器会存储 cookie 并在下次向同一服务器再发起请求时携带并发送到服务器上。通常，它用于告知服务端两个请求是否来自同一浏览器——如保持用户的登录状态。Cookie 使基于[无状态](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Overview#http_是无状态，有会话的)的 HTTP 协议记录稳定的状态信息成为了可能。

* 服务端保持会话的状态的一种手段

## 作用

会话状态管理

​	如用户登录状态、购物车、游戏分数或其他需要记录的信息

个性化设置

​	如用户自定义设置、主题和其他设置

浏览器行为跟踪

​	如跟踪分析用户行为等

# http 请求范围

Http 协议范围请求允许服务器只发送 HTTP 消息的一部分到客户端。范围请求在传送大的媒体文件，或者与文件下载的断点续传功能搭配时非常有用。

## 检测服务器是否支持范围请求

可以使用 curl 命令去测试，主要看相应头中是否存在 `accept-rangs`，如果存在并且值不为（`none`），那么表示该服务器支持范围请求。

```http
curl -I 'https://static.runoob.com/images/demo/demo2.jpg'

# 相应如下
HTTP/2 200 
server: openresty
date: Tue, 09 May 2023 16:24:15 GMT
content-type: image/jpeg
content-length: 289024
accept-ranges: bytes
age: 1432803
content-md5: mIbpDwFNRitWDc7Jwye9tw==
etag: "9886E90F014D462B560DCEC9C327BDB7"
last-modified: Thu, 19 Jan 2017 07:40:22 GMT
x-m-log: QNM:cdn-cache-dls-hbsjz-sjz-4;QNM3
x-m-reqid: Q78MyJDpz
x-oss-hash-crc64ecma: 3949126287721555526
x-oss-object-type: Normal
x-oss-request-id: 644496CD07479339392B5958
x-oss-server-time: 39
x-oss-storage-class: Standard
x-oss-version-id: null
x-qnm-cache: Hit
```

* 如上相应包含 accept-ranges 字段

  在上面的响应中， `Accept-Ranges: bytes` 表示界定范围的单位是 bytes。这里 [`Content-Length`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Length) 也是有效信息，因为它提供了要检索的图片的完整大小。

  Accept-ranges: bytes 表示范围的单位；

  Content-Length: 表示资源的完整大小

> 如果站点未发送 `Accept-Ranges` 首部，那么它们有可能不支持范围请求。一些站点会明确将其值设置为 "none"，以此来表明不支持。在这种情况下，某些应用的下载管理器会将暂停按钮禁用。

* 如果不支持下载管理会将暂停按钮禁用。

## 从服务器请求特定的范围

加入服务器支持范围请求的话，你可以使用 Range 首部来生成该类请求，指示服务器返回文件的哪一部分或哪几部分。

### 单一范围

请求：

```bash
curl https://static.runoob.com/images/demo/demo2.jpg -i -H "Range: bytes=0-1023"
```

* 主要是设置 header 头 range

响应：

```http
HTTP/1.1 206 Partial Content
Content-Range: bytes 0-1023/146515
Content-Length: 1024
```

* 服务端会返回一个状态码为 206 Partial Content 的响应
* 携带 Header

> 在这里，[`Content-Length`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Length) 首部现在用来表示先前请求范围的大小（而不是整张图片的大小）。[`Content-Range`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Range) 响应首部则表示这一部分内容在整个资源中所处的位置。

### 多重范围

Range 标头支持多段范围请求，使用逗号分隔。

请求：

```bash
curl https://static.runoob.com/images/demo/demo2.jpg -i -H "Range: bytes=0-1023, 1048-2048" 
```

响应：

```http
content-type: multipart/byteranges; boundary="web cache:687ea5e6915b10235a56c12543171408"
content-length: 2290
accept-ranges: bytes
```

> 服务器返回 206 Partial Content 状态码和 Content-Type：multipart/byteranges; boundary=3d6b6a416f9b5 头部，Content-Type：multipart/byteranges 表示这个响应有多个 byterange。每一部分 byterange 都有他自己的 Content-type 头部和 Content-Range，并且使用 boundary 参数对 body 进行划分。

* 多段内容使用 boundary 参数进行划分

### 条件式范围请求

当（中断之后）重新开始请求更多资源片段的时候，必须确保自从上一个片段被接收之后该资源没有进行过修改。

> The [`If-Range`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/If-Range) 请求首部可以用来生成条件式范围请求：假如条件满足的话，条件请求就会生效，服务器会返回状态码为 [`206`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/206) `Partial` 的响应，以及相应的消息主体。假如条件未能得到满足，那么就会返回状态码为 [`200`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/200) `OK` 的响应，同时返回整个资源。该首部可以与 [`Last-Modified`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Last-Modified) 验证器或者 [`ETag`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/ETag) 一起使用，但是二者不能同时使用。

* 确保文件没有被改过

### 其他方式

可以省略右边部分，代表一直到结束：

```text
Range: bytes=200-
```

也可以省略左边部分，代表从头开始：

```text
Range: bytes=-1000
```

而且可以请求多段 range，服务端会返回多段内容：

```http
Range: bytes=200-1000, 2000-6576, 19000-
```

## 分片下载实践

参考：https://zhuanlan.zhihu.com/p/620113538?utm_campaign=shareopn&utm_medium=social&utm_oi=713439222677118976&utm_psn=1628706087619772416&utm_source=wechat_session

* 实现：http -> Range

## 分片下载方案

### 1. 使用 http range

* 这种方案需要客户端和服务端进行开发

* 优点就是能定制进度条

### 2. 使用 http transfer-encoding

参考：https://juejin.cn/post/7362065407137169447 —— todo nest 怎么处理文件下载（主要是流在 nest 中的实现）

代码：transfer-encoding.ts

这种方式不需要客户端和服务端做任何工作，http原生支持

* 缺点就是不太能支持下载进度条的定制，不过浏览器都实现了自己的进度条
* 可以使用抓包工具查看确实是分片下载合并的

### 3. 就是完全定制响应体了

* 这种方式见的少

# http 安全

## CSP

content-security-policy

Csp 内容安全策略是一个额外的安全层，用于检测并削弱某些特定类型的攻击。包括跨站脚本（[XSS](https://developer.mozilla.org/zh-CN/docs/Glossary/Cross-site_scripting)）和数据注入攻击等。无论是数据盗取、网站内容污染还是恶意软件分发，这些攻击都是主要的手段。

### 设置

> 为使 CSP 可用，你需要配置你的网络服务器返回 [`Content-Security-Policy`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Security-Policy) HTTP 标头（有时你会看到 `X-Content-Security-Policy` 标头，但那是旧版本，并且你无须再如此指定它）。

* 服务端设置 HTTP 标头 content-security-policy

除此之外，[``](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/meta) 元素也可以被用来配置该策略，例如

```
<meta
  http-equiv="Content-Security-Policy"
  content="default-src 'self'; img-src https://*; child-src 'none';" />
```

### 缓解跨站脚本攻击

> CSP 的主要目标是减少和报告 XSS 攻击。XSS 攻击利用了浏览器对于从服务器所获取的内容的信任。恶意脚本在受害者的浏览器中得以运行，因为浏览器信任其内容来源，即使有的时候这些脚本并非来自于它本该来的地方。
>
> CSP 通过指定有效域——即浏览器认可的可执行脚本的有效来源——使服务器管理者有能力减少或消除 XSS 攻击所依赖的载体。一个 CSP 兼容的浏览器将会仅执行从白名单域获取到的脚本文件，忽略所有的其他脚本（包括内联脚本和 HTML 的事件处理属性）。
>
> 作为一种终极防护形式，始终不允许执行脚本的站点可以选择全面禁止脚本执行。

* 控制可执行脚本的来源

## 使用 CSP

> 配置内容安全策略涉及到添加 [`Content-Security-Policy`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Security-Policy) HTTP 标头到一个页面，并配置相应的值，以控制用户代理（浏览器等）可以为该页面获取哪些资源。比如一个可以上传文件和显示图片页面，应该允许图片来自任何地方，但限制表单的 action 属性只可以赋值为指定的端点。一个经过恰当设计的内容安全策略应该可以有效的保护页面免受跨站脚本攻击。本文阐述如何恰当的构造这样的标头，并提供了一些例子。

# HTTP认证

## HTTP身份认证

> HTTP 提供一个用于权限控制和认证的通用框架。本页介绍了通用的 HTTP 认证框架，并且展示了如何通过 HTTP “Basic”模式限制对你服务器的访问。

* HTTP提供了一个用于权限控制和认证的通用框架

## 禁止访问

> 如果（代理）服务器收到*无效*的凭据，它应该响应 [`401`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/401) `Unauthorized` 或 [`407`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/407) `Proxy Authentication Required`，用户可以发送新的请求或替换 [`Authorization`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Authorization) 标头字段。
>
> 如果（代理）服务器接受的有效凭据**不足以**访问给定的资源，服务器将响应 [`403`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/403) `Forbidden` 状态码。与 [`401`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/401) `Unauthorized` 或 [`407`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/407) `Proxy Authentication Required` 不同的是，该用户无法进行身份验证并且浏览器不会提出新的的尝试。
>
> 在所有情况下，服务器更可能返回 [`404`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/404) `Not Found` 状态码，以向没有足够权限或者未正确身份验证的用户隐藏页面的存在。

* 401 一般是无效的凭据，也就是未认证
* 403 forbidden 一般是权限不足



# mime type

**MIME type** （现在称为“媒体类型 (media type)”，但有时也是“内容类型 (content type)”）是指示文件类型的字符串，与文件一起发送（例如，一个声音文件可能被标记为 `audio/ogg` ，一个图像文件可能是 `image/png` ）。它与传统 Windows 上的文件扩展名有相同目的。

## 列表参考

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/MIME_types

## 独立类型

*独立*类型表明了对文件的分类，可以是如下之一：

文本、音频、视屏、图片、二进制文件

| 类型          | 描述                                                         | 典型示例                                                     |
| :------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| `text`        | 表明文件是普通文本，理论上是人类可读                         | `text/plain`, `text/html`, `text/css, text/javascript`       |
| `image`       | 表明是某种图像。不包括视频，但是动态图（比如动态 gif）也使用 image 类型 | `image/gif`, `image/png`, `image/jpeg`, `image/bmp`, `image/webp`, `image/x-icon`, `image/vnd.microsoft.icon` |
| `audio`       | 表明是某种音频文件                                           | `audio/midi`, `audio/mpeg, audio/webm, audio/ogg, audio/wav` |
| `video`       | 表明是某种视频文件                                           | `video/webm`, `video/ogg`                                    |
| `application` | 表明是某种二进制数据                                         | `application/octet-stream`, `application/pkcs12`, `application/vnd.mspowerpoint`, `application/xhtml+xml`, `application/xml`, `application/pdf` |

对于 text 文件类型若没有特定的 subtype，就使用 `text/plain`。类似的，二进制文件没有特定或已知的 subtype，即使用 `application/octet-stream`。

* Text 文件类型默认：text/palin；二进制文件默认 application/octet-stream

## 混合类型 multipart

```
multipart/form-data
multipart/byteranges
```

Copy to Clipboard

*Multipart* 类型表示细分领域的文件类型的种类，经常对应不同的 MIME 类型。这是*复合*文件的一种表现方式。`multipart/form-data` 可用于联系 [HTML Forms](https://developer.mozilla.org/zh-CN/docs/Learn/Forms) 和 [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST) 方法，此外 `multipart/byteranges`使用状态码[`206`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/206) `Partial Content`来发送整个文件的子集，而 HTTP 对不能处理的复合文件使用特殊的方式：将信息直接传送给浏览器（这时可能会建立一个“另存为”窗口，但是却不知道如何去显示内联文件。）



