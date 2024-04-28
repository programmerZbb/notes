# 简介

>Nginx是lgor Sysoev为俄罗斯访问量第二的rambler.ru站点设计开发的。从2004年发布至今，凭借开源的力量，已经接近成熟与完善。
>
>Nginx功能丰富，可作为HTTP服务器，也可作为反向代理服务器，邮件服务器。支持FastCGI、SSL、Virtual Host、URL Rewrite、Gzip等功能。并且支持很多第三方的模块扩展。
>
>Nginx的稳定性、功能集、示例配置文件和低系统资源的消耗让他后来居上，在全球活跃的网站中有12.18%的使用比率，大约为2220万个网站。

* 还可以做邮箱服务器？

> 参考：
>
> * https://juejin.cn/post/6844904144235413512#heading-15

# Nginx 常用功能

## 1. http 代理

Http代理，反向代理：作为 web 服务器最常用的功能之一，尤其是反向代理

正向代理、反向代理如图

![img](https://www.runoob.com/wp-content/uploads/2018/08/1535725078-5993-20160202133724350-1807373891.jpg)

* 正向代理也就是请求代理，用户在真正请求网络之前做的代理，比如说 vpn
* 反向代理：也就是web服务的前向代理服务器，面向的是服务器，在服务器之前做代理

## 2. 负载均衡

Nginx提供的负载均衡策略有两种：

* 内置策略：轮询，加权轮询，Ip hash
* 扩展策略：太多了。。。

![img](https://www.runoob.com/wp-content/uploads/2018/08/1535725078-8303-20160202133753382-1863657242.jpg)

* 普通轮询就是每次换一个服务器
* 加权轮询就是权重越高，接待的服务越多

![img](https://www.runoob.com/wp-content/uploads/2018/08/1535725078-1224-20160201162405944-676557632.jpg)

* ip hash 算法，对客户端请求的ip进行hash操作，然后根据hash结果将同一个客户端ip的请求分发给同一台服务器进行处理，可以解决session不共享的问题。

  能够解决每次请求共享session



## 3. web缓存

Nginx可以对不同的文件做不同的缓存处理，配置灵活，并且支持FastCGI_Cache，主要用于对FastCGI的动态程序进行缓存。配合着第三方的ngx_cache_purge，对制定的URL缓存内容可以的进行增删管理。

* todo



# nginx 配置

## 配置文件结构

如果你下载好啦，你的安装文件，不妨打开conf文件夹的nginx.conf文件，Nginx服务器的基础配置，默认的配置也存放在此。

在 nginx.conf 的注释符号为： **#**



### 语法

配置文件语法：key value 参数1 参数2...;



基础配置如下：

```nginx
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```

Nginx 文件结构

```nginx
...              #全局块

events {         #events块
   ...
}

http      #http块
{
    ...   #http全局块
    server        #server块
    { 
        ...       #server全局块
        location [PATTERN]   #location块
        {
            ...
        }
        location [PATTERN] 
        {
            ...
        }
    }
    server
    {
      ...
    }
    ...     #http全局块
}
```

* 最外层是全局块
* events 块
* http块
  * server 块

1、**全局块**：配置影响nginx全局的指令。一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等。

2、**events块**：配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等。

3、**http块**：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等。

4、**server块**：配置虚拟主机的相关参数，一个http中可以有多个server。可以有多个location块

5、**location块**：配置请求的路由，以及各种页面的处理情况。

## docker nginx 配置

主配置：/etc/nginx/nginx.conf (这些信息在dockerhub nginx可以看到)

### 引入其他配置

```nginx
include /etc/nginx/conf.d/*.conf;
```

## 虚拟主机 server 配置

就是可以用一台 nginx 服务器来为多个域名和端口的提供服务。

* server 可以配置多个

## http-location 配置

### 静态服务器

四种配置路由

```nginx
# location 四种配置语法
# 1. 精确匹配
location = /test111 {
  default_type text/plain;
  return 200 "test111";
}
# 2. 前缀匹配，比如：/test22/aaa
location /test22 {
  default_type text/plain;
  # 直接返回当前路径
  return 200 $uri;
}
# 3. 正则: ~，大小写不敏感 *
location ~* ^/test33/AA.*\.html$ {
  default_type text/plain;
  return 200 $uri;
}
# 4. ^~ 提高优先级
# location ^~ /test22 {
#     default_type text/plain;
#     return 200 "sss";
# }
```

指定静态文件：

```nginx
# 指定静态文件
location /html1 {
  default_type text/html;
  alias "${html_path}/test1.html";
}
location /html2 {
  default_type text/html;
  alias "${html_path}/test2.html";
}
```

#### alias vs root

```nginx
location /html1 {
  default_type text/html;
  # 变量拼接的方式
  alias "${html_path}/test1.html";
}
location /html2 {
  default_type text/html;
  root "${html_path}/test2.html";
}    
```

两个都是指定目录

* alias 不会拼接匹配的路径，直接换个名称
* Root 会添加这个前缀

### proxy http vs https

* http 默认访问了80端口；https默认访问了443端口。其他区别待补充

### 反向代理能力 —— 也就是网关

#### rewrite 规则

```nginx
server {
    rewrite {规则} {定向路径} {重写类型} ;
}
```

1、规则：可以是字符串或者正则来表示想匹配的目标url

2、定向路径：表示匹配到规则后要定向的路径，如果规则里有正则，则可以使用$index来表示正则里的捕获分组

3、重写类型：

> **last** ：相当于Apache里德(L)标记，表示完成rewrite，浏览器地址栏URL地址不变。停止rewrite检测【如果没有匹配到，会继续向下匹配】 **break**；本条规则匹配完成后，终止匹配，不再匹配后面的规则，浏览器地址栏URL地址不变。停止rewrite检测【如果没有匹配到，则不再向下匹配，直接返回结果404】

​	**redirect**：返回302临时重定向，浏览器地址会显示跳转后的URL地址。

​	**permanent**：返回301永久重定向，浏览器地址栏会显示跳转后的URL地址permanent：返回301永久重定向，浏览器地址栏会显示跳转后的URL地址

```nginx
# proxy 测试
location /api-test {
  # default_type text/plain;
  # 修改header
  proxy_set_header name test;
  proxy_pass http://host.docker.internal:8099;
  # last ：相当于Apache里德(L)标记，表示完成rewrite，浏览器地址栏URL地址不变。停止rewrite检测【如果没有匹配到，会继续向下匹配】
  # break；本条规则匹配完成后，终止匹配，不再匹配后面的规则，浏览器地址栏URL地址不变。停止rewrite检测【如果没有匹配到，则不再向下匹配，直接返回结果404】
  rewrite ^/api-test(.*) /api$1 break;
}
```

## nginx 逻辑

### if 语句

参考：https://www.cnblogs.com/songxingzhu/p/6382007.html

正则表达式匹配：

==:等值比较;

~：与指定正则表达式模式匹配时返回“真”，判断匹配与否时区分字符大小写；

~*：与指定正则表达式模式匹配时返回“真”，判断匹配与否时不区分字符大小写；

!~：与指定正则表达式模式不匹配时返回“真”，判断匹配与否时区分字符大小写；

!~*：与指定正则表达式模式不匹配时返回“真”，判断匹配与否时不区分字符大小写；

### 实现一个灰度系统

比如：实现一个灰度系统。实现原理就是一个灰度系统给用户注入一个 cookie，让其跳转到对应的版本。

* 可以用 random 实现，0.2 以下去高版本

```nginx
# 灰度实现
upstream nest_server_v1.0 {
  server host.docker.internal:8099;
}
upstream nest_server_v2.0 {
  server host.docker.internal:8089;
}
upstream default {
  server host.docker.internal:8099;
}

server {
  # 灰度逻辑
  set $gray_server "default";
  # 使用的是正则的方式，因为是包含的关系。加个引号吧，防止中间有啥特殊符号
  if ($http_cookie ~* "version=1.0") {
    set $gray_server nest_server_v1.0;
  }
  if ($http_cookie ~* "version=2.0") {
    set $gray_server nest_server_v2.0;
  }
  
  location /api-test {
    # 直接使用upstream中配置的服务
    proxy_pass http://$gray_server;
    # last ：相当于Apache里德(L)标记，表示完成rewrite，浏览器地址栏URL地址不变。停止rewrite检测【如果没有匹配到，会继续向下匹配】
    # break；本条规则匹配完成后，终止匹配，不再匹配后面的规则，浏览器地址栏URL地址不变。停止rewrite检测【如果没有匹配到，则不再向下匹配，直接返回结果404】
    rewrite ^/api-test(.*) /api$1 break;
  }
}
```

## upstream 配置

https://blog.redis.com.cn/5130.html

例子：

```nginx
upstream backend {
    server backend1.example.com       weight=5;
    server backend2.example.com:8080;
    server unix:/tmp/backend3;

    server backup1.example.com:8080   backup;
    server backup2.example.com:8080   backup;
}

server {
    location / {
        proxy_pass http://backend;
    }
}
```

配置指令

| 语法:   | `upstream name { ... }` |
| ------- | ----------------------- |
| 默认值: | —                       |
| 上下文: | `http`                  |

定义一组服务器。 这些服务器可以监听不同的端口。 而且，监听在TCP和UNIX域套接字的服务器可以混用。

* 在配置 location 块的时候需要指定 proxy_pass 为 http://backend

```nginx
upstream backend {
    server backend1.example.com weight=5;
    server 127.0.0.1:8080       max_fails=3 fail_timeout=30s;
    server unix:/tmp/backend3;
}
```

> 默认情况下，nginx按加权轮转的方式将请求分发到各服务器。 在上面的例子中，每7个请求会通过以下方式分发： 5个请求分到`backend1.example.com`， 一个请求分到第二个服务器，一个请求分到第三个服务器。 与服务器通信的时候，如果出现错误，请求会被传给下一个服务器，直到所有可用的服务器都被尝试过。 如果所有服务器都返回失败，客户端将会得到最后通信的那个服务器的（失败）响应结果。

* 策略：默认加权轮转，通过weight配置加权值；如果一个出现错误，会传给下一个服务区，知道所有的服务器都被尝试过；如果有所的服务器都失败了，会将最后通信的哪个服务器的相应结果返回。

server 语法

| 语法:   | `**server** *address* [*parameters*];` |
| ------- | -------------------------------------- |
| 默认值: | —                                      |
| 上下文: | `upstream`                             |

定义服务器的地址`*address*`和其他参数`*parameters*`。地址可以是域名或者IP地址，端口是可选的，或者是指定“`unix:`”前缀的UNIX域套接字的路径。如果没有指定端口，就使用80端口。 如果一个域名解析到多个IP，本质上是定义了多个server。

你可以定义下面的参数：`weight`=`*number*`设定服务器的权重，默认是1。`max_fails`=`*number*`设定Nginx与服务器通信的尝试失败的次数。在`fail_timeout`参数定义的时间段内，如果失败的次数达到此值，Nginx就认为服务器不可用。在下一个`fail_timeout`时间段，服务器不会再被尝试。 失败的尝试次数默认是1。设为0就会停止统计尝试次数，认为服务器是一直可用的。 你可以通过指令

### 什么定义为失败呢

proxy_next_upstream、 fastcgi_next_upstream和memcached_next_upstream来配置什么是失败的尝试。 默认配置时，`http_404`状态不被认为是失败的尝试。`fail_timeout`=`*time*`设定

- 统计失败尝试次数的时间段。在这段时间中，服务器失败次数达到指定的尝试次数，服务器就被认为不可用。
- 服务器被认为不可用的时间段。

默认情况下，该超时时间是10秒。`backup`标记为备用服务器。当主服务器不可用以后，请求会被传给这些服务器。`down`标记服务器永久不可用，可以跟ip_hash指令一起使用。

* 例如

  ```nginx
  upstream backend {
      server backend1.example.com     weight=5;
      server 127.0.0.1:8080           max_fails=3 fail_timeout=30s;
      server unix:/tmp/backend3;
  
    	# 这个服务器为备用服务器
      server backup1.example.com:8080 backup;
  }
  ```

### 使用负载均衡策略

* 在配置 location 块的时候需要指定 proxy_pass 为 `http://backend`

### unix 域套接字-机器内部进程通信的方式

https://www.cnblogs.com/nufangrensheng/p/3569416.html

### 负载均衡策略——ip_hash 策略

| 语法:   | `**ip_hash**;` |
| ------- | -------------- |
| 默认值: | —              |
| 上下文: | `upstream`     |

指定服务器组的负载均衡方法，请求基于客户端的IP地址在服务器间进行分发。 IPv4地址的前三个字节或者IPv6的整个地址，会被用来作为一个散列key。 这种方法可以确保从同一个客户端过来的请求，会被传给同一台服务器。除了当服务器被认为不可用的时候，这些客户端的请求会被传给其他服务器，而且很有可能也是同一台服务器。

> 从1.3.2和1.2.2版本开始支持IPv6地址。

如果其中一个服务器想暂时移除，应该加上`down`参数。这样可以保留当前客户端IP地址散列分布。

* 如果有一个服务器需要移除，需要加上down（下线）参数

配置如下：
```nginx
upstream backend {
  	# ip_hash 策略
    ip_hash;

    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com down;
    server backend4.example.com;
}
```

> 从1.3.1和1.2.2版本开始，`ip_hash`的负载均衡方法才支持设置服务器权重值。

### keepalive-每个worker进程与后端服务器保持连接的最大数量

| 语法:   | `**keepalive** *connections*;` |
| ------- | ------------------------------ |
| 默认值: | —                              |
| 上下文: | `upstream`                     |

这个指令出现在版本 1.1.4.

激活对上游服务器的连接进行缓存。

`*connections*`参数设置每个worker进程与后端服务器保持连接的最大数量。这些保持的连接会被放入缓存。 如果连接数大于这个值时，最久未使用的连接会被关闭。

> 需要注意的是，`keepalive`指令不会限制Nginx进程与上游服务器的连接总数。 新的连接总会按需被创建。 `*connections*`参数应该稍微设低一点，以便上游服务器也能处理额外新进来的连接。

* 应该设置低一点

配置memcached上游服务器连接keepalive的例子：

```nginx
upstream memcached_backend {
    server 127.0.0.1:11211;
    server 10.0.0.2:11211;

    keepalive 32;
}

server {
    ...

    location /memcached/ {
        set $memcached_key $uri;
        memcached_pass memcached_backend;
    }

}
```

#### http 代理的情况

对于HTTP代理，proxy_http_version指令应该设置为“`1.1`”，同时“Connection”头的值也应被清空。

```
upstream http_backend {
    server 127.0.0.1:8080;

    keepalive 16;
}

server {
    ...

    location /http/ {
        proxy_pass http://http_backend;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
        ...
    }
}
```

> 另外一种选择是，HTTP/1.0协议的持久连接也可以通过发送“Connection: Keep-Alive”头来实现。不过不建议这样用。

#### 提供的变量

`ngx_http_upstream_module`模块支持以下嵌入变量：

`$upstream_addr`保存服务器的IP地址和端口或者是UNIX域套接字的路径。 在请求处理过程中，如果有多台服务器被尝试了，它们的地址会被拼接起来，以逗号隔开，比如： “`192.168.1.1:80, 192.168.1.2:80, unix:/tmp/sock`”。 如果在服务器之间通过“X-Accel-Redirect”头或者error_page有内部跳转，那么这些服务器组之间会以冒号隔开，比如：“`192.168.1.1:80, 192.168.1.2:80, unix:/tmp/sock : 192.168.10.1:80, 192.168.10.2:80`”。`$upstream_response_time`以毫秒的精度保留服务器的响应时间，（输出）单位是秒。 出现多个响应时，也是以逗号和冒号隔开。`$upstream_status`保存服务器的响应代码。 出现多个响应时，也是以逗号和冒号隔开。`$upstream_http_...`保存服务器的响应头的值。比如“Server”响应头的值可以通过`$upstream_http_server`变量来获取。 需要注意的是只有最后一个响应的头会被保留下来。



### 更多

* 查看：https://blog.redis.com.cn/5130.html



## Nginx 变量

Nginx 变量所有变量都可以通过 `$变量` 的方式进行引用

变量分为两种内置预定义变量和自定义变量

https://blog.csdn.net/m0_37556444/article/details/84563520

### 自定义变量

* 在 server http location等模块中使用set命令声明变量

  ```nginx
  set $变量名 变量值
  ```

  注意nginx中的变量必须都以$开头。
  
  *# 变量只能在 server location if 中set*

### 变量可见性

nginx的配置文件中所有使用的变量都必须是声明过的，否则nginx会无法启动并打印相关异常日志

nginx变量的一个有趣的特性就是nginx中每一个变量都是全局可见的，而他们又不是全局变量。比如下面这个例子

在不同层级的标签中声明的变量性的可见性规则如下:

1. location标签中声明的变量中对这个location块可见
2. server标签中声明的变量对server块以及server块中的所有子块可见
3. http标签中声明的变量对http块以及http块中的所有子块可见

识别到不存在的变量不会直接保存，会返回空。因此要主要变量的可见规则



### 内置预变量

参考：http://timd.cn/nginx/varindex/

| 全局变量名         | 功能                                                         |
| ------------------ | ------------------------------------------------------------ |
| `$host`            | 请求信息中的 `Host`，如果请求中没有 `Host` 行，则等于设置的服务器名，不包含端口 |
| `$request_method`  | 客户端请求类型，如 `GET`、`POST`                             |
| `$remote_addr`     | 客户端的 `IP` 地址                                           |
| `$args`            | 请求中的参数                                                 |
| `$arg_PARAMETER`   | `GET` 请求中变量名 PARAMETER 参数的值，例如：`$http_user_agent`(Uaer-Agent 值), `$http_referer`... |
| `$content_length`  | 请求头中的 `Content-length` 字段                             |
| `$http_user_agent` | 客户端agent信息                                              |
| `$http_cookie`     | 客户端cookie信息                                             |
| `$remote_addr`     | 客户端的IP地址                                               |
| `$remote_port`     | 客户端的端口                                                 |
| `$http_user_agent` | 客户端agent信息                                              |
| `$server_protocol` | 请求使用的协议，如 `HTTP/1.0`、`HTTP/1.1`                    |
| `$server_addr`     | 服务器地址                                                   |
| `$server_name`     | 服务器名称                                                   |
| `$server_port`     | 服务器的端口号                                               |
| `$scheme`          | HTTP 方法（如http，https）                                   |

### 利用 upstream 作为变量

location 中是能够直接访问 upstream 中的服务名的

```nginx
# 负载均衡配置
upstream nest-server {
  # 配置就是server块
  server host.docker.internal:8099 weight=2;
}
location /api-test {
  # default_type text/plain;
  # 修改header
  proxy_set_header name test;
  # 直接使用upstream中配置的服务
  proxy_pass http://nest-server;
}
```

## Linux防火墙 —— 利用Nginx开放指定端口

参考：https://zhulijun.club/back-end/server/Nginx%E5%AE%89%E8%A3%85%E4%B8%8E%E9%98%B2%E7%81%AB%E5%A2%99%E9%85%8D%E7%BD%AE.html#%E5%AE%89%E8%A3%85-nginx

### docker nginx 容器防火墙

* 只要在启动 Nginx 容器的时候让它完全代理网络就可以达到防火墙的目的

## nginx 静态资源压缩

参考：https://cloud.tencent.com/developer/article/1696134

### 抽成公共配置

参考：https://serverfault.com/questions/373578/how-can-i-configure-nginx-locations-to-share-common-configuration-options



# Nginx 高可用

* 参考 Linux 基础文档。主要是 keepalived + Nginx 实现高可用。

## VRRP —— 虚拟路由器冗余协议

[虚拟路由冗余协议](https://www.zhihu.com/search?q=虚拟路由冗余协议&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"87711150"})（VRRP）通过几台设备联合组成一台虚拟路由设备，将虚拟路由设备IP地址作为用户[默认网关](https://www.zhihu.com/search?q=默认网关&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"87711150"})实现与外部网络通信。

### 冗余备份

正常情况下，主设备负责转发数据流，当主设备出现故障时，会选择备组里优先级较高的设备作为主设备继续负责转发数据，实现网关冗余备份，同时达到链路冗余功能。



## keepalived 

https://zhuanlan.zhihu.com/p/566166393



# nginx 问题记录

## docker 重启Nginx端口被占用

参考：https://stackoverflow.com/questions/42303401/nginx-will-not-start-address-already-in-use
