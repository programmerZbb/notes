# web安全

## 总览

https://developer.mozilla.org/zh-CN/docs/Web/Security



## CSRF

> 跨站请求伪造（CSRF，Cross-site request forgery）是一种冒充受信任用户，向服务器发送非预期请求的攻击方式。例如，这些非预期请求可能是通过在跳转链接后的 [URL](https://developer.mozilla.org/zh-CN/docs/Glossary/URL) 中加入恶意参数来完成：

* 跨站请求伪造是一种冒充受信任用户，向服务器发送非预期请求的攻击方式，攻击服务器（目标）
* 主要是插入非法标签的形式

### 预防

有很多预防 CSRF 的方法，比如实现 [RESTful API](https://developer.mozilla.org/zh-CN/docs/Glossary/REST)，增加 secure token 等等。

### 场景

发送请求自动携带 cookie

* 主要原因就是session id保存在cookie中，发送请求自动携带。那么就有可能出现别的网站发送了一条认证过的请求。

## XSS

https://juejin.cn/post/6912030758404259854

跨站脚本攻击（Cross-site scripting，XSS）是一种安全漏洞，攻击者可以利用这种漏洞在网站上注入恶意的客户端代码。若受害者运行这些恶意代码，攻击者就可以突破网站的访问限制并冒充受害者。根据开放式 Web 应用安全项目（OWASP）

* 主要是脚本攻击。（客户端攻击）
* 比如打开了一个新的链接，然后返回一段可执行的客户端代码，获取一些敏感信息。

### 场景

在以下 2 种情况下，容易发生 XSS 攻击：

1. 数据从一个不可靠的链接进入到一个 Web 应用程序。劫持网页
2. 没有过滤掉恶意代码的动态内容被发送给 Web 用户。

> 恶意内容一般包括 [JavaScript](https://developer.mozilla.org/zh-CN/docs/Glossary/JavaScript)，但是，有时候也会包括 HTML，FLASH 或是其他浏览器可执行的代码。XSS 攻击的形式千差万别，但他们通常都会：将 cookies 或其他隐私信息发送给攻击者，将受害者重定向到由攻击者控制的网页，或是经由恶意网站在受害者的机器上进行其他恶意操作。

* 执行一个js，将隐私信息发送给攻击者的网站；或者重定向到攻击者网站；执行其他恶意操作；

XSS 攻击可以分为 3 类：存储型（持久型）、反射型（非持久型）、DOM 型。存储型的最常见。

### 防护

* CSP
* 用户输入内容进行过滤、转义