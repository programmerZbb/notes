# 1.认识node.js

![node实现设计](..\pic\node实现设计.png)

## 1.2 nodejs 和 JavaScript 区别

* ECMAscript（语法和词法）

  定义了语法，写JavaScript和node都得遵守

  * 不能操作DOM，不能绑定事件，不能发送ajax请求

  * 不能处理http 请求，不能操作文件
  * 只是定义了语法

* javascript

  * 使用ECMA 的语法+ webapi
  * DOM、BOM操作、事件绑定、ajax等

* nodejs

  ecmaScript + node api

  * 处理 http 、操作文件

## 1.3 commonjs(看node的笔记)

导出：

```js
function add () {
    
}

module.exports = {
    add
}
```



## 1.4 debug

* 在node.js 文件中直接打断点，然后点开右侧的debug，点击 start

# 2. 准备

## 2.1 server 开发和前端开发的区别

* 服务的稳定性
* 考虑内存和 cpu（优化、扩展）
* 日志记录
* 安全
* 集群和服务拆分

### 2.1.1 服务的稳定性

* server 端可能遭到各种恶意攻击和误操作
* 单个客户端可以挂掉，服务端不能挂
* 用 PM2 做进程守护

### 2.1.2 考虑CPU和内存（优化和扩展）

* 客户端独占一个浏览器，内存和CPU 不是问题
* server 端承载很多请求，CPU和内存都是紧缺资源
* 使用 stream 写日志（优化），使用 radis 存 session（扩展）

### 2.1.3 日志记录

* 前端发送日志
* server 端要记录日志，存储日志，分析日志

### 2.1.4 安全

* server端要随时准备接受各种恶意攻击

### 2.1.5 集群和服务拆分

* 产品发展速度快，流量可能会迅速增加
* 通过扩展机器和服务拆分来承接？支持服务拆分？？？？

## 2.2 项目介绍

从三个方面开始，产品分析

* 目标
* 需求
* 技术方案

### 2.2.1 目标

* 开发一个博客系统，具有博客的基本功能
* 只做 server 端，不管前端

### 2.2.3 技术方案

* 数据如何存储
* 与前端如何对接，即接口设计

存储：

* 博客
* 用户

api 设计

![api-design](..\picture\node-project\api-design.png)



# 3. 前置知识

## 3.1 http 相关

* http 默认端口 80，https 默认端口 443

## 3.2 nodejs 处理http请求

* get 请求和 queryString
* post 请求和 postdata
* 路由

Get 请求处理

```js
let http = require('http')
const querystring = require('querystring')

const server = http.createServer((req, res) => {
    // res.setHeader('Set-Cookie', 'testServer=11111111')
    // res.setHeader('location', 'http://127.0.0.1:3001')
    console.log('url:', req.method)
    console.log('url:', req.url)
    let query = querystring.parse(req.url.split('?')[1])
    console.log('query:', query)
    res.writeHead(200, {
        'content-type': 'text/plain; charset=UTF-8'
    })
    res.end(JSON.stringify(query))
})

server.listen(3000, () => {
    console.log('server is running')
})
```

* 用到 http 和 querystring 模块，`req.url` req 对象中有一切信息。
* req.headers 里面有一些基本的信息，包含请求头的信息

req 的事件：

* data 事件

  该事件能监听接受到的数据

  ```js
  req.on('data', (chunk) => {
      console.log(chunk.toString)
  })
  ```

  

* end 事件

  能够监听到数据请求完成

  ```js
  req.on('end', () => {
      console.log('请求结束')
  })
  ```

处理 post 请求(需要 tostring 方法可以将接受到的二进制转化成字符串)

思路就是用一个水管将桶连起来，不断的接受，知道数据结束

```js
const http = require('http')

const server = http.createServer((req, res) => {
    console.log('content-type: ', req.headers['content-type'])
    req.on('data', (chunk) => {
        console.log(chunk.toString(), '--------接受的数据')
    })
    req.on('end', () => {
        console.log('接受数据结束')
    })
})

server.listen(3000, () => {
    console.log('server is running')
})
```

### 文档

* request 的文档对应着 [http.IncomingMessage 类](http://nodejs.cn/api/http.html#http_class_http_incomingmessage)

* response 对应着 [http.ServerResponse 类](http://nodejs.cn/api/http.html#http_class_http_serverresponse)

### 总结

1. request 类中的 url 能获取到路由和queryString

2. queryString 模块能解析 querystring 请求

3. 响应的数据必须是字符串，用 JSON.stringify 序列化

4. 还有一种方式能设置header

   ```js
   res.writeHeader(404, {'Content-type': 'text/plain'})
   ```

   修改返回内容的另一种方式（除了 end），不用再 end 里面修改数据

   ```js
   res.write('404 Not Found\n')
   ```
   
   另一种是`res.setHeader()` 
   
5. 使用 request.method 来获取请求的方法，获取到的 method 是大写的

6. req 对象的headers 里面的key 都是小写的

   res 中 setheader 中的 key 都是大写的
   
7. req.on('data') 里面的数据的 chunk 是继承的 node 的 Buffer 类，buffer 有一个 toString() 方法能够将二进制数据转化成 string 类型的。

   在文档中的 <http://nodejs.cn/api/http.html#http_http_request_options_callback>

# 4. 搭建开发环境

* 从 0 开始，不使用任何框架
* 使用 nodemon 检测文件变化，自动重启node
* 使用 cross-env 设置环境变量，兼容 mac、linux、windows

# 5. 开发接口

* 初始化路由： 根据之前技术方案的设计，做出路由
* 返回假数据，将路由和数据处理分离，以符合设计原则

接口分类：

1. blog
2. user

# 6.项目结构

1. src 中的项目结构

   src 下三个重要的目录

   model 目录：下面存放着数据模型，就是返回的数据的基本格式，使用 class 类的方式定义好了

   router 目录： 下面存放着路由处理，只做路由的拦截

   controller 目录：存放着获取数据的逻辑
   
   补充： bin 文件夹下的 www 文件是创建服务的文件

# 7. MySQL 基本使用

## 1. 简介

* web server 中最流行的关系型数据库

## 2. 操作

### 1. 基本语句

![基本语句](..\picture\node-practice\基本语句.png)

* `show databases` 显示所有的库

### 2. 建库

![建库](..\picture\node-practice\建库.png)

* 新建一个库，起一个名字，在 schemas 中能看见。执行 show databases 也能看见下面所有的库

### 3. 建表

![建表数据](..\picture\node-practice\建表数据.png)

* 需要存储的数据

blog 表：

![blog表](..\picture\node-practice\blog表.png)

user 表：

![user表](..\picture\node-practice\user表.png)

* datatye 是类型，字符串用 varchar() 来创建，longtext 表示能存储大数据（最多4g的数据）

  bigint() 表示更大的 int 类型，varchar(10) 不管英文字母还是汉字还是数字都是10个长度。（MySQL>=5 版本）

* pk 主键 是主键，包证不能重复

* nn不为空 是否必须

* ai 自动添加，不需要手动去添加

#### 新建表

![创建表](..\picture\node-practice\创建表.png)

* 右键点击创建表

### 4. 表操作

每一行操作都要有分号

* ==首先需要使用该库==

  ```sql
  use myblog;
  ```

  注释：

  ```mysql
  -- 我是注释
  ```

* 显示该库下面的所有表

  ```sql
  show tables
  ```

* 避免键名为关键字，比如 password 就是 sql的关键字（只有在 insert into 中才需要添加）

  可以用 `` 包括的方式来包围这个关键字

#### 1. 新增数据

* 新增数据是增加表的一行中的每一列

  ```sql
  insert into users (username, `password`, realname) values ('zhangsan', '123', '张三');
  ```

#### 2. 查询

查询所有：

```sql
select * from users;
```

注意：最好不要使用 `*`查询，会很消耗性能

查询列：

```sql
select id, username from users;
```

查询条件：(多个条件)

```sql
select * from users where username='zhangsan';
-- 交集
select * from users where username='zhangsan' and `password`='123';
-- 并集
select * from users where username='zhangsan' or `password`='123';
-- 模糊查询，使用 like 关键字代替 =，查询的value 用 % 包括
select * from users where username like '%zhang%';
-- 查询正序排序
select * from users where username='zhangsan' order by id;
-- 查询降序排序
select * from users where username='zhangsan' order by id desc;
-- 不等于
select * from users where state <> '0';
```

#### 3. 更新

* 更新语句在执行的时候会报错

  `you are using safe mode`

  解决：

  ```sql
  SET SQL_SAFE_UPDATES = 0;
  ```

  然后执行，即可删掉这行语句

```sql
update users set realname='测试', title='测试2' where username='zhangsan';
```



#### 4. 删除

```sql
delete from users where username='ceshi'
```

* 一定要写条件，不然删除整个表

> 实际开发过程中，并不会直接删除掉这个数据，而是添加一个 state 列的形式来记录是否删除的状态，用 update 语句更新删除状态--软删除。

软删除：

```sql
update users set state='0' where username='zhangsan';
```

总结： 所有的操作表的语句都有两个关键字（这样可以方便记住是否有 from 关键字）

#### 其他关键字

* limit 在查询的尾部添加限制条数

  ```sql
  limit 10;
  ```

* count() 函数，计数; as 为另命名

  ```sql
  count(*) as pv
  ```

* distinct（明显的清楚地独特的），表去重，在字段的前面加

  ```sql
  count(DISTINCT member_id) as uv
  ```

* 对象类型的键也可以使用对象调用的方式使用

  例如：

  ```sql
  field['path'].string_type
  ```

## 3. 在 node 中操作MySQL

* 需要安装 mysql 插件

  ```shell
  npm i mysql
  ```

### 使用

新建一个mysql 项目，在index.js 文件中

```js
const mysql = require('mysql')
// 创建连接对象，在使用 workbench 的时候都填过
const con = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: 'qazplm',
    port: '3306'，
    database: 'myblog' //连接的库，相当于 use myblog
})
// 开始连接
con.connect()
// 执行sql语句
const sql = 'select * from users'
con.query(sql, (err, result) => { // node 中很多的回调，前面都是err，后面是 result
    if (err) {
        return console.log(err)
    }
    console.log(result)
})
// 关闭连接
con.end()
```

* 更新的 result 需要根据 result 中的 `affectedRows`或者 `changedRows` 是否为0 来判断（为0则失败）

![node-sql-res](..\picture\node-practice\node-sql-res.png)

* 新增需要看 insertId

## 4. 项目中使用 mysql

* 在 src 中新建一个 conf 文件夹

  然后新建一个 db.js 文件

* ==获取环境变量==

  在 node 中能够使用 process 模块能够拿到当前项目启动的进程

  在 package.json 的 scripts 中配置了启动命令

  ```js
  "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "dev": "cross-env NODE_ENV=dev nodemon ./bin/www.js",
      "prd": "cross-env NODE_ENV=production nodemon ./bin/www.js"
    }
  ```

  而 `process.env` 能够拿到 node 启动时候配置的变量

  因此可以通过 `process.env.NODE_ENV`拿到环境变量

db.js 文件

```js
const env = process.env.NODE_ENV // 环境变量
let MYSQL_CONF
if (env === 'dev') {
    MYSQL_CONF = {
        host: 'localhost',
        user: 'root',
        password: 'qazplm',
        port: '3306'，
        database: 'myblog' //连接的库，相当于 use myblog
    }
}
if (env === 'production') {
    MYSQL_CONF = {
        host: 'localhost',
        user: 'root',
        password: 'qazplm',
        port: '3306'，
        database: 'myblog' //连接的库，相当于 use myblog
    }
}
module.exports = {
    MYSQL_CONF
}
```

* 在 src 下面新建一个 db 文件夹

  下面为操作 db 的文件

* 小技巧

  在拼接 sql 查询的时候，可以使用如下的小技巧以防后面的条件不存在

  ```js
  let sql = `select * from users where 1=1 `
  ```

  同理，在get请求的query拼接的时候也可以使用同样的技巧，以防后面的query不存在

  ```js
  ?a=100
  ```




==总结：==

* 每次查找的结果都是数组，没有查找到为空数组。
* 更新，删除，新增则会返回一个对象

# 8. 登录开发

* 核心：登录校验和登录信息存储。

简介：

1. cookie 和 session 
2. session 写入 radis （内存数据库，mysql是硬盘数据库）
3. 开发登录功能，和前端联调（用 Nginx 做反向代理）

## 8.1 cookie

* 存储在浏览器端的一段字符串（最大5k）（4097个字节，不同浏览器不一样）
* 跨域不共享（domain）
* 格式 k1=v1;k2=v2;  可存储结构化数据
* ==每次发送请求会将请求域的 cookie 发送给 server 端==（比如 淘宝的页面请求百度的接口，则携带百度这个域名下的cookie）
* server 端可以修改 cookie 并返回浏览器
* 浏览器也可以通过 js 来修改 cookie （有限制）

cookie 组成

| name | value | domain                     | path           | expires                                          | size | httpOnly                                           |
| ---- | ----- | -------------------------- | -------------- | ------------------------------------------------ | ---- | -------------------------------------------------- |
| key  | value | 域名（一级域名、二级域名） | 路径（默认 /） | 过期时间（默认 1969-1....,不设置的话会永久有效） | 大小 | 只能通过http请求的方式获取和修改，js不能获取和修改 |



### 8.1.1 客户端 js 操作 cookie

* 客户端查看 cookie （三种方式）

* js 查看、修改 cookie （有限制）

  js 操作，和 localstorage 不一样，每次都只能加一个，而且只能修改key,value，不能设置过期时间和路径等。

  ```js
  document.cookie = 'k1=v1;' // 只能累加，不会去覆盖的，会一直加下去
  ```


### 8.1.2 server 端操作 cookie

* 查看 cookie
* 操作 cookie
* 实现登录验证

1. 解析 cookie（需要自己封装方法解析出 cookie的值）

   ```js
   const cookie = req.headers['cookie'] || ''
   ```

2. 操作 cookie

   ```js
   res.setHeader('Set-Cookie', 'username=aaa; path=/; httpOnly; expires=111') // 默认的path是当前请求的路径
   ```

   * 加了 httpOnly 之后前端使用 `document.cookie` 就只能打印一个空字符串。不能修改cookie，但是能添加cookie。

     js 操作 cookie 相当于每次加一条 cookie，发送 http 请求的时候，会从下往上添加到 header 中的 cookie，后端获取的对象的 key 的值就会覆盖，只会拿到最上面那一条 cookie，就是刚访问页面的时候后端设置的 cookie。

   * `expires`的值需要为 Date 对象的 `toUTCString()`方法打印出来的时间

## 8.2 session

* 只用 cookie 的登录的方式，会暴露 username，很危险
* 如何解决：cookie中存放userid，server 端对应 username
* 解决方案：session，即 server 端存储用户信息。

基本逻辑：每次访问是否登录都要给客户端设置一个 session，未登录这个session在后端就没有存值（或者为空），登录之后将该 session 存的值设置成用户的值。

登录不登录都给你存一个 sessionid。

```js
// 全局的session数据，全局的能用大写
const SESSION_DATA = {}
```



## 8.3 session 存在的问题

* 目前 session 直接是js 变量，存放在 nodejs 进程内存中
* 第一，进程内存有限，访问量过大，内存暴增怎么办
* 第二，正式线上是多进程的（包括多机房），进程之间的内存无法共享(qq 不能访问 chrome)

进程内存模型：

![进程模型](..\picture\node-practice\进程模型.png)

* 8进制用 0 开头，16进制用 0x 开头
* 进程的内存包括 栈（stack）、堆（heap）

操作系统内存分配：

![操作系统内存分配](..\picture\node-practice\操作系统内存分配.png)

* nodejs 的内存：32位系统中只有 1.6G 的内存限制，64位的也不会超过 3G

nodejs 线上运行：

​	node 线上运行肯定要根据服务器的硬件配置，进行多个进程（多个nodejs 服务），比如说 双核（同时并行处理2个进程，保证进程负载平均）

#### session 开发套路

1. 进来的强求，不管有没有 userid 或者 服务端保存的 sessionData 都要赋值，然后没有在客户端保存的 cookie的进行标记，最后在客户端进行 cookie保存。只有在登录的时候才会设置id对应的 sessionData 保存，其他接口都不变。
2. 主要是分两步
   * 设置 cookie中的userid
   * 登录后在 session 中保存 sessionData。

## 8.4 解决session存在的问题——解决方案：redis

* web server 最常用的缓存数据库，数据库存放在内存中（内存读写快，比较昂贵，内存断电丢失）
* 相比于 mysql，访问速度快（内存和硬盘不是一个数量级的）
* 成本过高，可存储的数据量更小（内存的硬伤）

![redis在server端](..\picture\node-practice\redis在server端.png)

### 为什么 session 适合使用 redis

* session 访问频繁，对性能要求极高
* session 可不考虑断电丢失数据的问题（内存的硬伤）存储这加快访问的数据，顶多重新登录
* session 中存储的数据不会很大（相比于mysql 中的数据）

### 为什么网站数据不适合 redis

* 操作频率不是太高，相较于 session 操作
* 断电不能丢失，必须保留
* 数据量太大，存储成本高

## 8.5 redis 使用

### 1.安装

参考：

<https://www.runoob.com/redis/redis-install.html>

mac 安装：

```shell
brew install redis
```

### 2.启动

* windows 需要配置环境变量，配置系统的环境变量，路径配置到 redis 的目录即可

启动 redis

```shell
redis-server.exe 
# 切换到 redis 目录下面执行下面的命令，两个不同的窗口
redis-cli.exe -h 127.0.0.1 -p 6379
```

* 注意：第二种执行的方式`redis-cli.exe -h 127.0.0.1 -p 6379`，目前只知道 cmd 的方式执行，不知道 bash 怎么执行（windows）

![redis 启动](..\picture\node-practice\redis 启动.png)

### 3. 基本操作

* redis 是 key value 的形式，存放的字符串的形式，如果需要保存对象，需要转换成字符串。
* 直接在命令行中 `set key value`可以设置
* `get key` 能够获取到值
* keys * 得到所有的key
* del key 删除key

注意：

* 传一个不存在的值，redis 会返回一个 null，不会直接报错，注意做判断。

* 小技巧：不论保存这对象还是字符串都返回成功

  ```js
  try {
      return JSON.parse(value)
  } catch (err) {
      return value
  }
  ```

### 4. 用 redis 保存 sessionData

### 5. 接口鉴权（authentication）

* 在项目开发的过程中，增加，更新，删除的接口需要鉴权，获取列表和获取详情的接口不需要鉴权（因为没有登录也可能看见列表）
* 本项目中使用 session 的方式提供鉴权。
* 项目鉴权需要写一个方法来判断

## 8.6 和前端联调

* 登录依赖 cookie，必须使用浏览器联调
* cookie 跨域不共享的，前端和server端必须同域
* 需要使用 Nginx 做代理，让前后端同域

### 1. Nginx介绍

* 高性能的 web 服务器，开源免费
* 一般用做静态服务（js、css、img 文件等）、负载均衡（各个机器承担的流量相同）
* 反向代理（本课程用到）

<https://www.runoob.com/w3cnote/nginx-setup-intro.html>

反向代理如下：

![Nginx反向代理](..\picture\node-practice\Nginx反向代理.png)

* 反向代理，服务端对客户端来说是黑盒的，不知道访问哪里

> 正向代理，VPN那种形式，客户端能够控制访问哪个域名

#### 基本配置

* 下载完成解压之后，在Nginx目录下`nginx.exe`文件执行
* 在 conf 文件夹下的 `nginx.conf` 的文件能修改配置，用管理员权限打开

配置文件：（注意不加冒号）

```shell
#user  nobody;
worker_processes  2;
#配置工作核数

server {
        listen       8080;
# 端口修改
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

#        location / {
#           root   html;
#           index  index.html index.htm;
#       }
# 配置代理，所有静态文件代理到 8081
		location / {
		proxy_pass http://localhost:8081;
		}
		location /api/ {
		proxy_pass http://localhost:8080;
# 做代理之后 host 不一样了，需要把host传过去		
		proxy_set_header Host $host;
		}
```



