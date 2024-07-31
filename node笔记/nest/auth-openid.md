# 阅读

* https://www.ory.sh/docs/oauth2-oidc/overview/oauth2-concepts#oauth-20-scope-permission
* https://www.ruanyifeng.com/blog/2019/04/oauth-grant-types.html
* https://xie.infoq.cn/article/41ac7535056b5000f8e3870cf
* 微软文档：https://learn.microsoft.com/zh-cn/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios

## OAuth2

### 简介

参考：https://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html

OAhuth 是一个关于授权（authorization）的开放网络标准，在全世界广泛引用，目前版本是2.0

* 标准化的授权方案，标准化之后就能通用的参数传递，不用没家都实现每家的标准

本文对OAuth2.0设计思路和运行流程进行解析，主要参考资料是 [RFC 6749](http://www.rfcreader.com/#rfc6749)

### RFC 6749

> OAuth 引入了一个授权层，用来分离两种不同的角色：客户端和资源所有者。......资源所有者同意以后，资源服务器可以向客户端颁发令牌。客户端通过令牌，去请求数据。

### 应用场景

一个第三方网站需要访问当前网站的信息，但是需要用户授权，怎么获得用户的授权就是 OAuth2.0的目标。

* 第三方网站不能获取用户的账密去登录

### 四种流程

1. Authorization Code：授权码模式，因为需要在各个节点往返三次，俗称 3 leg。
2. Implicit：隐式授权，相对于授权码模式做了简化。
3. Resource Owner Password Credentials：密码认证模式。
4. Client Credentials：客户端认证模式。

### 名词

> （1） **Third-party application**：第三方应用程序，本文中又称"客户端"（client），即上一节例子中的"云冲印"。
>
> （2）**HTTP service**：HTTP服务提供商，本文中简称"服务提供商"，即上一节例子中的Google。
>
> （3）**Resource Owner**：资源所有者，本文中又称"用户"（user）。
>
> （4）**User Agent**：用户代理，本文中就是指浏览器。
>
> （5）**Authorization server**：认证服务器，即服务提供商专门用来处理认证的服务器。
>
> （6）**Resource server**：资源服务器，即服务提供商存放用户生成的资源的服务器。它与认证服务器，可以是同一台服务器，也可以是不同的服务器。

知道了上面这些名词，就不难理解，OAuth的作用就是让"客户端"安全可控地获取"用户"的授权，与"服务商提供商"进行互动。

### 思路

> OAuth在"客户端"与"服务提供商"之间，设置了一个授权层（authorization layer）。"客户端"不能直接登录"服务提供商"，只能登录授权层，以此将用户与客户端区分开来。"客户端"登录授权层所用的令牌（token），与用户的密码不同。用户可以在登录的时候，指定授权层令牌的权限范围和有效期。
>
> "客户端"登录授权层以后，"服务提供商"根据令牌的权限范围和有效期，向"客户端"开放用户储存的资料。

* 提供一个授权层（即授权服务器），客户端只能登录授权服务器。授权服务器指定登录所有的令牌（token）的权限范围和有效期

### 流程

![OAuth运行流程](https://www.ruanyifeng.com/blogimg/asset/2014/bg2014051203.png)

> （A）用户打开客户端以后，客户端要求用户给予授权。
>
> （B）用户同意给予客户端授权。
>
> （C）客户端使用上一步获得的授权，向认证服务器申请令牌。
>
> （D）认证服务器对客户端进行认证以后，确认无误，同意发放令牌。
>
> （E）客户端使用令牌，向资源服务器申请获取资源。
>
> （F）资源服务器确认令牌无误，同意向客户端开放资源。

不难看出来，上面六个步骤之中，B是关键，即用户怎样才能给于客户端授权。有了这个授权以后，客户端就可以获取令牌，进而凭令牌获取资源。

### 授权模式

客户端必须得到用户的授权（authorization grant），才能获得令牌（access token）。OAuth 2.0定义了四种授权方式。

- 授权码模式（authorization code）
- 简化模式（implicit）
- 密码模式（resource owner password credentials）
- 客户端模式（client credentials）

必须通过用户的授权，也就是用户点击确认（非常重要）

### 授权码模式（常用）

> 这种方式是最常用的流程，安全性也最高，它适用于那些有后端的 Web 应用。授权码通过前端传送，令牌则是储存在后端，而且所有与资源服务器的通信都在后端完成。这样的前后端分离，可以避免令牌泄漏。

> 授权码模式（authorization code）是功能最完整、流程最严密的授权模式。它的特点就是通过客户端的后台服务器，与"服务提供商"的认证服务器进行互动。

![授权码模式](https://www.ruanyifeng.com/blogimg/asset/2014/bg2014051204.png)

>（A）用户访问客户端，后者将前者导向认证服务器。
>
>（B）用户选择是否给予客户端授权。
>
>（C）假设用户给予授权，认证服务器将用户导向客户端事先指定的"重定向URI"（redirection URI），同时附上一个授权码。
>
>（D）客户端收到授权码，附上早先的"重定向URI"，向认证服务器申请令牌。这一步是在客户端的后台的服务器上完成的，对用户不可见。
>
>（E）认证服务器核对了授权码和重定向URI，确认无误后，向客户端发送访问令牌（access token）和更新令牌（refresh token）。

* 重要步骤：

  授权后，需要返回授权码；第三方服务使用授权码和重定向URL去认证服务器申请令牌；

  认证服务器核对授权码和重定向URL后，向客户端发送访问令牌（access token）和更新令牌（refresh token）；

#### 约定的参数

A 步骤客户端需要向授权服务器发送的URL参数如下：

* response_type: 表示授权类型，必须，此处为code

* Client_id: 表示客户端ID，必选；

* redirect_utl: 表示授权后的重定向URL，可选项；

* scope: 表示申请的权限范围；可选

* state: 表示客户端的当前状态，可以指定任意值，认证服务器会原封不动返回，可作为客户端校验请求的参数；

  ==state项不用动态数据的话会存在CSRF漏洞==

例子如下：

```http
GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz
        &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
Host: server.example.com
```

C步骤中，服务器回应客户端的URI，包含以下参数：

- code：表示授权码，必选项。该码的有效期应该很短，通常设为10分钟，客户端只能使用该码一次，否则会被授权服务器拒绝。该码与客户端ID和重定向URI，是一一对应关系。
- state：如果客户端的请求中包含这个参数，认证服务器的回应也必须一模一样包含这个参数。

注意：授权服务器返回的code码有效期应该设置很短，通常为10分钟，只能使用一次。

一般情况下鉴权服务器会把code鉴权路径的path设置为 `/callback`。

例子：

```http
HTTP/1.1 302 Found
Location: https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA
          &state=xyz
```

D步骤中，客户端向认证服务器申请令牌的HTTP请求，包含以下参数：

- grant_type：表示使用的授权模式，必选项，此处的值固定为"authorization_code"。
- code：表示上一步获得的授权码，必选项。
- redirect_uri：表示重定向URI，必选项，且必须与A步骤中的该参数值保持一致。
- client_id：表示客户端ID，必选项。

type、code、redirect_url、client_id

* code需要和redirect_utl、client_id一一对应

```http
POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA
&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb
```

E步骤中，认证服务器发送的HTTP回复，包含以下参数：

- access_token：表示访问令牌，必选项。
- token_type：表示令牌类型，该值大小写不敏感，必选项，可以是bearer类型或mac类型。
- expires_in：表示过期时间，单位为秒。如果省略该参数，必须其他方式设置过期时间。
- refresh_token：表示更新令牌，用来获取下一次的访问令牌，可选项。
- scope：表示权限范围，如果与客户端申请的范围一致，此项可省略。

```http
     HTTP/1.1 200 OK
     Content-Type: application/json;charset=UTF-8
     Cache-Control: no-store
     Pragma: no-cache

     {
       "access_token":"2YotnFZFEjr1zCsicMWpAA",
       "token_type":"example",
       "expires_in":3600,
       "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
       "example_parameter":"example_value"
     }
```

从上面代码可以看到，相关参数使用JSON格式发送（Content-Type: application/json）。此外，HTTP头信息中明确指定不得缓存。

* 不能使用http缓存

### 简化模式（SSO的原型）

> 简化模式（implicit grant type）不通过第三方应用程序的服务器，直接在浏览器中向认证服务器申请令牌，跳过了"授权码"这个步骤，因此得名。所有步骤在浏览器中完成，令牌对访问者是可见的，且客户端不需要认证。

![简化模式](https://www.ruanyifeng.com/blogimg/asset/2014/bg2014051205.png)

它的步骤如下：

> （A）客户端将用户导向认证服务器。
>
> （B）用户决定是否给于客户端授权。
>
> （C）假设用户给予授权，认证服务器将用户导向客户端指定的"重定向URI"，并在URI的Hash部分包含了访问令牌。
>
> （D）浏览器向资源服务器发出请求，其中不包括上一步收到的Hash值。
>
> （E）资源服务器返回一个网页，其中包含的代码可以获取Hash值中的令牌。
>
> （F）浏览器执行上一步获得的脚本，提取出令牌。
>
> （G）浏览器将令牌发给客户端。

### 更新令牌

如果用户访问的时候，客户端的"访问令牌"已经过期，则需要使用"更新令牌"申请一个新的访问令牌。

客户端发出更新令牌的HTTP请求，包含以下参数：

- grant*type：表示使用的授权模式，此处的值固定为"refresh*token"，必选项。
- refresh_token：表示早前收到的更新令牌，必选项。
- scope：表示申请的授权范围，不可以超出上一次申请的范围，如果省略该参数，则表示与上一次一致。

下面是一个例子。

> ```http
>      POST /token HTTP/1.1
>      Host: server.example.com
>      Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
>      Content-Type: application/x-www-form-urlencoded
> 
>      grant_type=refresh_token&refresh_token=tGzv3JOkF0XG5Qx2TlKWIA
> ```

### 流行的 OAuth2 客户端框架

* 参考：https://www.ory.sh/docs/hydra/guides/using-oauth2

## access token vs refresh token



## 与 cookie

由于 Cookie 容易被 CSRF 攻击，不建议采用 cookie 的方式传输 token。尽量不要用 URI 参数的方法，因为浏览器历史记录、服务器日志等可能泄露 URI 上的机密信息。

# OpenID connect(OIDC)

参考：https://zhuanlan.zhihu.com/p/95064385

## 问题

* 一个公司多个系统，不可能每个系统都维护一个登录系统，账密数据库。
* 重新登陆账密是一个很繁琐的过程，容易造成用户流失，不如通过sso方式登录
* OAuth2 系统提供了一种第三方鉴权的方案

最好的解决方案就是 将用户认证和授权这些事 交给专门的 **identity** provider（idp）服务来处理。

可以通过idp服务极大的简化用户的注册和登录流程。

## openid connect

> OpenID Connect在2014年发行。虽然它不是第一个idp标准，但从可用性、简单性方面来说，它可能是最好的。OpenID Connect从SAML和OpenID 1.0/2.0中做了大量借鉴。

* openid 在 OAuth2 基础上做了扩展，OAuth2 使用 access token来授权第三方应用访问受保护的信息。
* Openid 遵循 OAuth2.0 协议流程（流程不变），在此基础上增加了 id token 来解决第三方应用的用户身份认证的问题。

### id token

> OpenID Connect将用户身份认证信息以id token的方式给到三房应用。[id token](https://www.zhihu.com/search?q=id token&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"95064385"})基于JWT(json web token）进行封装，具有自包含性、紧凑性和防篡改性等特点。三方应用在验证完id token的正确性后，可以进一步通过oAuth2授权流程获得的access token读取更多的用户信息。

* 为啥还有加一个id token呢？说白了还是想服务端变成无状态的，便于扩展。

### 特点

> 容易处理的id token。OpenID Connect使用JWT来给应用传递用户的身份信息。JWT以其高安全性（防止token被伪造和篡改）、跨语言、支持过期、自包含等特性而著称，非常适合作为token来使用。
>
> 基于oAuth2.0协议。id token是经过oAuth2.0流程来获取的，这个流程即支持web应用，也支持原生app。
>
> 简单。OpenID Connect足够简单。但同时也提供了大量的功能和安全选项以满足企业级业务需求。

* 基于 OAuth2.0 那么就必须按照Oauth的流程来，也就是说流程上必须存在access token，额外请求的参数也必须放到 scope 中。

### OpenID connect 角色

* 用户。

* RP：Relying Party，申请授信信息的可信客户端（既上文中提到的三方应用）。

* OP：OpenID Provider，提供身份认证的服务方，比如google和facebook等公司的系统。

* id token：包含身份认证信息的JWT。

* user info api，返回用户信息的接口，当RP使用id token来访问时，返回授权用户的信息。

看来需要使用id token来获取用户的信息。

### 流程

https://juejin.cn/post/6906471941142806541#heading-4

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3309aff6f8f04ef5b2f65345a41f0dc4~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

RP(client)发送一个认证请求到 OpenID Provider（OP）。

OP对End User进行认证并获得相应的授权。

OP返回一个ID Token或者access Token给RP。

RP使用access token向UserInfo Endpoint请求用户信息。

UserInfo Endpoint返回相应的用户信息给RP。

* 拿到id token 需要向usefinfo 接口请求用户信息。
* id token 也能直接代替 access token

重点角色：

* OP: Openid provider
* UserInfo api

### id token 参数

```json
{
  "sub"       : "alice",
  "iss"       : "https://openid.flydean.com",
  "aud"       : "client-12345",
  "nonce"     : "n-0S6_WzA2Mj",
  "auth_time" : 1311280969,
  "acr"       : "c2id.loa.hisec",
  "iat"       : 1311280970,
  "exp"       : 1311281970
}
```

- sub = Subject Identifier：必须。iss提供的EU的唯一标识；最长为255个ASCII个字符；
- iss = Issuer Identifier：必须。提供认证信息者的唯一标识。一般是Url的host+path部分；
- aud = Audience(s)：必须。标识ID-Token的受众。必须包含OAuth2的client_id；
- nonce：RP发送请求的时候提供的随机字符串，用来减缓重放攻击，也可以来关联ID-Token和RP本身的Session信息。
- auth_time = AuthenticationTime：EU完成认证的时间。如果RP发送认证请求的时候携带max_age的参数，则此Claim是必须的。
- acr = Authentication Context Class Reference：可选。表示一个认证上下文引用值，可以用来标识认证上下文类。
- iat = Issued At Time：必须。JWT的构建的时间。
- exp = Expiration time：必须。ID-Token的过期时间；

## 基于 openid 的 SSO

### 重定向

* 至少需要两次重定向吧？

  一次重定向到当前服务的接口，让接口取鉴权；

  接口鉴权完成重定向到最开始无权限的页面；

### sso 通用标准OpenID Connect

https://juejin.cn/post/6906471941142806541#heading-4

* 相当于OAuth2.0最后那个客户端服务器验证code的步骤后添加userInfo接口的访问。





# JWT

JSON WEB Token 是一种token令牌的方式。JSON Web Token（缩写 JWT）是目前最流行的跨域认证解决方案。

* 跨域认证解决方案

https://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html

是 rfc7519 的一个标准：https://datatracker.ietf.org/doc/html/rfc7519

## 跨域认证存在的问题

* 扩展性不好，分布式架构需要session共享（可能通过Redis）
* 登录状态保存到了服务端，服务端压力大

### 无状态 session

* 服务端不需要保持这个状态，不需要不同的实例之间共享状态。这个非常重要。

## JWT 原理

JWT 的原理是，服务器认证以后，生成一个 JSON 对象，发回给用户，就像下面这样。

* 服务端认证后返回一个JSON对象

> ```javascript
> {
>   "姓名": "张三",
>   "角色": "管理员",
>   "到期时间": "2018年7月1日0点0分"
> }
> ```

以后，用户与服务端通信的时候，都要发回这个 JSON 对象。服务器完全只靠这个对象认定用户身份。为了防止用户篡改数据，服务器在生成这个对象的时候，会加上签名（详见后文）。

这样服务端就变为无状态的了，从而比较容易扩展。

## JWT 数据结构

JWT 的数据结构大概如下：

![img](https://www.wangbase.com/blogimg/asset/201807/bg2018072304.jpg)

是一个很长的字符串，中间用 `.` 分割成三部分。

JWT 的三个部分依次如下：

* Header(头部)
* Payload （负载）
* Signature (签名)

表达式如下：

```tex
Header.Payload.Signature
```

![img](https://www.wangbase.com/blogimg/asset/201807/bg2018072303.jpg)

### Header

Header 部分是一个JSON对象，描述了JWT的元数据，如下：

> ```javascript
> {
>   "alg": "HS256",
>   "typ": "JWT"
> }
> ```

上面代码中，`alg`属性表示签名的算法（algorithm），默认是 HMAC SHA256（写成 HS256）；`typ`属性表示这个令牌（token）的类型（type），JWT 令牌统一写为`JWT`。

需要将Header转换为base64url的格式

```typescript
import Utf8 from 'crypto-js/enc-utf8'
import Base64URL from 'crypto-js/enc-base64url';

// 把一个普通的字符串转换为base64 url。参考：https://gist.github.com/joecliff/10948117
// 这也是jwt种Header等普通对象转换为base64url的方式
const utf8String = Utf8.parse(JSON.stringify({
  "alg": "HS254",
  "typ": "JWT"
}))
console.log(Base64URL.stringify(utf8String))
```

### payload

Payload也是一个JSON对象，用来存放实际需要传递的数据，JWT规定了7个官方字段供选用

- iss (issuer)：签发人
- exp (expiration time)：过期时间
- sub (subject)：主题
- aud (audience)：受众
- nbf (Not Before)：生效时间
- iat (Issued At)：签发时间
- jti (JWT ID)：编号

除了官方字段，你还可以再这个部分定义私有字段，比如

```javascript
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

注意，JWT 默认是不加密的，任何人都可以读到，所以不要把秘密信息放在这个部分。

这个 JSON 对象也要使用 Base64URL 算法转成字符串。

### signature

Signature 部分是对前两部分的签名，防止数据篡改。

首先，需要指定一个密钥（secret）。这个密钥只有服务器才知道，不能泄露给用户。然后，使用 Header 里面指定的签名算法（默认是 HMAC SHA256），按照下面的公式产生签名。

> ```javascript
> HMACSHA256(
>   base64UrlEncode(header) + "." +
>   base64UrlEncode(payload),
>   secret) === sign
> ```

算出签名以后，把 Header、Payload、Signature 三个部分拼成一个字符串，每个部分之间用"点"（`.`）分隔，就可以返回给用户。

* 应该是一个对称加密，不是非对称加密的签名，要不然太浪费时间
* 这样计算之后，任何一个部分都不能被修改了，如果header被修改，那么整个jwt对比将会失效。

## Base64URL


前面提到，Header 和 Payload 串型化的算法是 Base64URL。这个算法跟 Base64 算法基本类似，但有一些小的不同。

JWT 作为一个令牌（token），有些场合可能会放到 URL（比如 api.example.com/?token=xxx）。Base64 有三个字符`+`、`/`和`=`，在 URL 里面有特殊含义，所以要被替换掉：`=`被省略、`+`替换成`-`，`/`替换成`_` 。这就是 Base64URL 算法。

## JWT 的使用

客户端收到服务器返回的 JWT，可以储存在 Cookie 里面，也可以储存在 localStorage。

此后，客户端每次与服务器通信，都要带上这个 JWT。你可以把它放在 Cookie 里面自动发送，但是这样不能跨域，所以更好的做法是放在 HTTP 请求的头信息`Authorization`字段里面。

> ```javascript
> Authorization: Bearer <token>
> ```

另一种做法是，跨域的时候，JWT 就放在 POST 请求的数据体里面。

* 几种方式我都遇到过

大致流程如下：

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8b161071501442dfa83e639dac13a89b~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

## 特点

（1）JWT 默认是不加密，但也是可以加密的。生成原始 Token 以后，可以用密钥再加密一次。

​	不能加密，要不然下次服务端就解析不错过期时间和用户信息等内容。

（2）JWT 不加密的情况下，不能将秘密数据写入 JWT。

（3）JWT 不仅可以用于认证，也可以用于交换信息。有效使用 JWT，可以降低服务器查询数据库的次数。

（4）JWT 的最大缺点是，由于服务器不保存 session 状态，因此无法在使用过程中废止某个 token，或者更改 token 的权限。也就是说，一旦 JWT 签发了，在到期之前就会始终有效，除非服务器部署额外的逻辑。

（5）JWT 本身包含了认证信息，一旦泄露，任何人都可以获得该令牌的所有权限。为了减少盗用，JWT 的有效期应该设置得比较短。对于一些比较重要的权限，使用时应该再次对用户进行认证。

（6）为了减少盗用，JWT 不应该使用 HTTP 协议明码传输，要使用 HTTPS 协议传输。

secret 用了SHA256算法，理论上不存在破解的可能性，因此可以是固定的。

## 存在的问题

1. 安全性：可以被复制去伪造。

   解决： jwt时效性、强制https

2. 性能问题：

   每次验证都需要解析对比

3. 没法立即失效

   session 因为是存在服务端的，那我们就可以随时让它失效，而 JWT 不是，因为是保存在客户端，那我们是没法手动让他失效的。

   比如踢人、退出登录、改完密码下线这种功能就没法实现。

   但也可以配合 redis 来解决，记录下每个 token 对应的生效状态，每次先去 redis 查下 jwt 是否是可用的，这样就可以让 jwt 失效。

## JWT 失效问题

通常，JWT令牌失效的方法有以下几种：

1. 设置过期时间：在创建JWT令牌时，我们可以设置一个过期时间，当令牌过期后，它将自动失效。一旦过期，即使攻击者截获了令牌，也无法使用该令牌进行进一步的操作。
2. 使用黑名单：在退出登录或修改密码时，我们可以将JWT令牌添加到一个黑名单中。在下次令牌验证时，我们可以先检查JWT令牌是否在黑名单中，如果存在，则说明该令牌已失效。
3. 修改密钥：JWT令牌使用密钥进行签名，如果我们修改了密钥，旧的JWT令牌就会失效。当用户退出登录或修改密码时，我们可以生成一个新的密钥，并使用该密钥来签名新的JWT令牌。这样，旧的JWT令牌就无法被攻击者使用。

总之，在退出登录或修改密码时，我们需要让旧的JWT令牌失效，以确保用户的安全。我们可以通过设置过期时间、使用黑名单和修改密钥等方式来实现JWT令牌的失效。

一般用黑名单的模式比较好，需要定期清除黑名单！

## JWT 续签问题

如何持续保持用户登录状态。

阅读：https://www.cnblogs.com/wyy1234/p/9805843.html#_label3_2

## JWT secret

* secret 就是服务端的私钥，要保证这个私钥不会暴漏，方式客户端私自签发。

## 其他实践

* Java 安全相关：https://javaguide.cn/system-design/security/jwt-intro.html

## 解析工具

https://tooltt.com/jwt-decode/

比如如下一个jwt:

```tex
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJqdGkiOiIyYzczYWJjYS0xZmVkLTQ1ZjUtOTdkMS02MTI2YzFhYjM4ZTIiLCJzdWIiOiJBdXRoIiwidXNlckluZm8iOnsiYXBwSWQiOiJ3ZWIiLCJ1c2VySWQiOiI1MTU2MjMxMTkiLCJpcCI6IjEwMy4xOTYuNjUuMzYiLCJleHBpcmVzIjo4NjQwMH0sImV4cCI6MTY4NDk4NTczOX0.mqAWgwxzTlV5Hlq61_tjrnPGPWiGoIi_ZmaVJBPKJTw
```

