# 协议入门

https://www.ruanyifeng.com/blog/2016/08/http.html

# http 1.0

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

## 持久链接

1.1 版的最大变化，就是引入了持久连接（persistent connection），==即TCP连接默认不关闭==，可以被多个请求复用，不用声明`Connection: keep-alive`。（默认的）

客户端和服务器发现对方一段时间没有活动，就可以主动关闭连接。不过，规范的做法是，客户端在最后一个请求时，发送`Connection: close`，明确要求服务器关闭TCP连接。

> ```http
> Connection: close
> ```

目前，对于同一个域名，大多数浏览器允许同时建立==6个持久连接。==

## 管道机制

1.1 版还引入了管道机制（pipelining），即在同一个TCP连接里面，客户端可以同时发送多个请求。这样就进一步改进了HTTP协议的效率。

举例来说，客户端需要请求两个资源。以前的做法是，在同一个TCP连接里面，先发送A请求，然后等待服务器做出回应，收到后再发出B请求。管道机制则是允许浏览器同时发出A请求和B请求，但是服务器还是按照顺序，先回应A请求，完成后再回应B请求。

==发送请求的优化==

## Content-Length

一个TCP连接现在可以传送多个回应，势必就要有一种机制，区分数据包是属于哪一个回应的。（怎么样切片）这就是`Content-length`字段的作用，声明本次回应的数据长度。

> ```http
> Content-Length: 3495
> ```

上面代码告诉浏览器，本次回应的长度是3495个字节，后面的字节就属于下一个回应了。

在1.0版中，`Content-Length`字段不是必需的，因为浏览器发现服务器关闭了TCP连接，就表明收到的数据包已经全了。



## 分块传输编码

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



# HTTP 2

2015年，HTTP/2 发布。它不叫 HTTP/2.0，是因为标准委员会不打算再发布子版本了，下一个新版本将是 HTTP/3。

优化点：

头信息：

* 采用二进制
* 压缩（与服务端共同维护一张头信息表，发送id来 diff）

多路复用：

* 多工：双向实时的通信
* 数据流：数据切割，把每个包用 id 做切割，可取消，可指定优先级





## 二进制协议

HTTP/1.1 版的头信息肯定是文本（ASCII编码），数据体可以是文本，也可以是二进制。HTTP/2 则是一个彻底的二进制协议，==头信息和数据体都是二进制，并且统称为"帧"（frame）：头信息帧和数据帧。==

二进制协议的一个好处是，可以定义额外的帧。HTTP/2 定义了近十种帧，为将来的高级应用打好了基础。如果使用文本实现这种功能，解析数据将会变得非常麻烦，二进制解析则方便得多。

## 多工

HTTP/2 复用TCP连接，在一个连接里，客户端和浏览器都可以同时发送多个请求或回应，而且不用按照顺序一一对应，这样就避免了"队头堵塞"。

举例来说，在一个TCP连接里面，服务器同时收到了A请求和B请求，于是先回应A请求，结果发现处理过程非常耗时，于是就发送A请求已经处理好的部分， 接着回应B请求，完成后，再发送A请求剩下的部分。

这样双向的、实时的通信，就叫做多工（Multiplexing）。

## 数据流

因为 HTTP/2 的数据包是不按顺序发送的，同一个连接里面连续的数据包，可能属于不同的回应。因此，必须要对数据包做标记，指出它属于哪个回应。

==HTTP/2 将每个请求或回应的所有数据包，称为一个数据流（stream）。每个数据流都有一个独一无二的编号。数据包发送的时候，都必须标记数据流ID，用来区分它属于哪个数据流。另外还规定，客户端发出的数据流，ID一律为奇数，服务器发出的，ID为偶数。==

数据流发送到一半的时候，客户端和服务器都可以发送信号（`RST_STREAM`帧），取消这个数据流。1.1版取消数据流的唯一方法，就是关闭TCP连接。这就是说，HTTP/2 可以取消某一次请求，同时保证TCP连接还打开着，可以被其他请求使用。

客户端还可以指定数据流的优先级。优先级越高，服务器就会越早回应。

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

  网关层本身错误

  当服务器作为网关，不能及时得到响应时返回此错误代码。



## 缓存

缓存的种类有很多,其大致可归为两类：私有与共享缓存。共享缓存存储的响应能够被多个用户使用。私有缓存只能用于单独用户。本文将主要介绍浏览器与代理缓存，除此之外还有网关缓存、CDN、反向代理缓存和负载均衡器等部署在服务器上的缓存方式，为站点和 web 应用提供更好的稳定性、性能和扩展性。

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Caching

普遍的缓存案例:

- 一个检索请求的成功响应: 对于 [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET)请求，响应状态码为：[`200`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/200)，则表示为成功。一个包含例如HTML文档，图片，或者文件的响应。
- 永久重定向: 响应状态码：[`301`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/301)。
- 错误响应: 响应状态码：[`404`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/404) 的一个页面。
- 不完全的响应: 响应状态码 [`206`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/206)，只返回局部的信息。
- 除了 [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) 请求外，如果匹配到作为一个已被定义的cache键名的响应。



### cache-control 

` **Cache-Control**` 通用消息头字段，被用于在http请求和响应中，通过指定指令来实现缓存机制。缓存指令是==单向的==，这意味着在请求中设置的指令，不一定被包含在响应中。

指定 `no-cache` 或 `max-age=0, must-revalidate` 表示客户端可以缓存资源（客户端不做缓存检查），每次使用缓存资源前都必须重新验证其有效性。这意味着每次都会发起 HTTP 请求，==但当缓存内容仍有效时可以跳过 HTTP 响应体的下载。（因为是响应的时候收到的，浏览器只能取不下载）==

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



# header 字段解析

## 1. origin

请求首部字段 **`Origin`** 指示了==请求来自于哪个站点==。该字段仅指示服务器名称，并不包含任何路径信息。该首部用于 [CORS](https://developer.mozilla.org/zh-CN/docs/Glossary/CORS) 请求或者 [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST)了不包含路径信息，该字段与 [`Referer`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Referer)



## 2. referer

* `**Referer**` 请求头包含了==当前请求页面==的==来源页面==的地址，即表示当前页面是通过此来源页面里的链接进入的。服务端一般使用 `Referer` 请求头识别访问来源，可能会以此进行统计分析、日志记录以及缓存优化等。



# HTTP 基础

## 1. MIME 类型

* https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/MIME_types

  **媒体类型**（通常称为 **Multipurpose Internet Mail Extensions** 或 **MIME** 类型 ）是一种标准，用来表示文档、文件或字节流的性质和格式。它在[IETF RFC 6838](https://tools.ietf.org/html/rfc6838)中进行了定义和标准化。

* 能够标记
  * 文件  input:file 
  * 文档 http 请求文档
  * 字节流