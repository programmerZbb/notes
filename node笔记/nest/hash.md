# hash 散列 

https://zhuanlan.zhihu.com/p/37165658

hash: 剁碎、打乱

> 哈希算法(Hash Algorithm)又称散列算法、散列函数、哈希函数，是一种从任何一种数据中创建小的数字“指纹”的方法。哈希算法将数据重新打乱混合，重新创建一个哈希值。对任意一组输入数据进行计算，得到一个固定长度的输出摘要，一般用于鉴权、认证、加密、索引等。

* Hash算法 又称为散列算法、哈希函数、散列函数。
* 创建小的数字 `指纹` 的方法
* 能够把任意长度数据进行计算，得到一个固定长度的输出摘要。（固定长度）也就预示着能多长内容进行hash计算，得到一个固定长度的hash值

## hash 算法和哈希碰撞

https://www.ruanyifeng.com/blog/2018/09/hash-collision-and-birthday-attack.html

* 哈希碰撞就是生成相同的hash值

### 如何避免hash碰撞

防止哈希碰撞的最有效方法，就是扩大哈希值的取值空间。

16个二进制位的哈希值，产生碰撞的可能性是 65536 分之一。也就是说，如果有65537个用户，就一定会产生碰撞。哈希值的长度扩大到32个二进制位，碰撞的可能性就会下降到 4,294,967,296 分之一。

### 避免hash碰撞的方式

hash取值空间也大，碰撞概率越低。

* 使用 sha256 

> 22个字符的哈希值，就能保证300万亿次计算里面，只有1000亿分之一的概率发生碰撞。常用的 SHA256 哈希函数产生的是64个字符的哈希值，每个字符的取值范围是0~9和a~f，发生碰撞的概率还要低得多。

## 使用场景

https://juejin.cn/post/6962913806477426696#heading-2

### 用户密码场景

> 对一个互联网平台来说，用户的密码安全是非常重要的，泄漏用户的密码需要负相应的法律责任。一个用户的密码可能是通用的，微信，QQ，抖音等等，可能使用的都是相同的手机号+密码的形式，一旦一个平台的密码被泄漏，那么可能所有平台的密码都泄漏了，所以关于密码安全，有一个原则：不能存储明文，最好开发人员都不知道密码是什么。
>
> 保存密码就可以使用hash。服务端保存密码的hash值，用户登录时，将密码的hash值发送给服务器，服务器通过比对来判断密码是否正确。下面用md5来举例，如果密码只做一次md5，那么还是不够安全，有一个网站 [www.cmd5.com](https://link.juejin.cn?target=https%3A%2F%2Fwww.cmd5.com) 记录了很多哈希值，用来查找对比...那么，我们想到加盐(password+salt).md5，但是这样所有用户都使用一个盐，也不安全。

* 服务端为了防止不同的网站获取密码，采用保存密码的hash值的方式
* 但是做一次md5也是不够的，目前已经能存在可以破解的网站，也就是彩虹表

### 加盐hash

> 加盐也就是使得食物变得更美味。

参考：https://www.zhihu.com/question/20299384

也就是客户端从服务端获取一个 salt，在保存密码的时候 hash 等于 密码＋盐的hash值

* 具体hash方案建议使用 sha 算法

> 数据库存上salt和hash，每当用户注册，随机生成salt，hash = md5(password + salt)
>
> 登录的时候验证md5(input + salt)和hash是否相等即可



# 工具

## cryptp.js 

官方：https://cryptojs.gitbook.io/docs/

Crypto: 加密

https://juejin.cn/post/7028402602094100510

https://zhuanlan.zhihu.com/p/146098209

crypto.js 是一个工具，CryptoJS 是一个不断增长的标准和安全加密算法集合，使用最佳实践和模式在 JavaScript 中实现。它们速度很快，并且具有一致且简单的界面。

* 加密算法JavaScript的实现

### 使用

```typescript
import sha256 from 'crypto-js/sha256';
import md5 from 'crypto-js/md5'
import Base64 from 'crypto-js/enc-base64';
import Base64URL from 'crypto-js/enc-base64url';
import Hex from 'crypto-js/enc-hex';
import HmacMd5 from 'crypto-js/hmac-md5'

// console.log(sha256('test').toString(), Base64.stringify(sha256('test2')))

// md5测试
/**
 * tostring 直接转换为相应的hex编码的字符串
 * Base64.stringify 这个工具能将crypto编码的words转换为base64位的字符串（可以通过hex转base64查看结果相等）
 * You can convert a WordArray object to other formats by explicitly calling the toString method and passing an encoder.
 *  我们可以转换words为其他格式的编码通过调用 toString方案传递其他编码器
 */
console.log(md5('test'), Base64.stringify(md5('test')), md5('test').toString(Base64), md5('test').toString(Hex))

// sha256测试
// console.log(sha256('test'), sha256('test').toString(), Base64.stringify(sha256('test')));

// hmac算法测试，主要是加盐
let base64 = HmacMd5('test', 'salt').toString(Base64)
console.log(HmacMd5('test', 'salt'), HmacMd5('test', 'salt').toString(Base64),
    // Base64.parse(base64)
  );

// 生产出base64url的code，主要是防止在url传递的过程中发生转移
console.log(md5('test').toString(Base64), md5('test').toString(Base64URL))

```

#### 文件md5

* 在webpack计算hash中使用。主要是更新md5的使用，能够不断更新。

```typescript
let a2 = require("crypto");
let fs = require("fs");

//第一种方法，针对大文件
let dataStream = fs.createReadStream("README.md");

const hashValue = a2.createHash('md5');
dataStream.on('data', dd => {
  hashValue.update(dd, 'utf8');
});

dataStream.on('end', () => {
  const md5 = hashValue.digest('hex');
  console.log(md5);
});

//第二种方法，针对小文件
let text = fs.readFile('README.md', 'utf8' , (err, data) => {
    if (err) {
      console.error(err)
      return
    }
    console.log(a2.createHash('md5').update(data,'utf8').digest('hex'));
  })

```

### hmac 算法

https://www.liaoxuefeng.com/wiki/1016959663602400/1183198304823296

* 也就是在加密的时候 加盐，增加复杂度
* 这个也就是在做前端密码登录的时候所使用的加码算法

### 加盐

* 在用户账号密码登录的时候，加盐是由服务端生成的随机数，目的就是增加密码破解的难顶

参考：https://zhuanlan.zhihu.com/p/22860282

### hex 编码

hex: 16进制

> HEX编码（十六进制编码）就是**使用数字0~9加上字母A~F一共16个字符表示的一种编码解码方式**，其中A~F相当于十进制的10~15，总计16个，这些称作十六进制数字。

* 使用常见字符进行编码的方式



# https 加密

* 非对称加密使用的是 RSA 公钥私钥的方式，再配合hash值（一般是SHA算法）