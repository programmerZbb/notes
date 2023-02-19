# 0. 前言

## 是什么

* 搜索服务引擎



## 简单易用

* 提供restfulApi去操作



## 数据结构

* https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247486522&idx=1&sn=7b6080756d0711c646fb47d5db49fc97&chksm=ebd74d3bdca0c42d35f7eef97e4f925a20cc82b07ca7aeba21f83ba56135ab2dd1ad73238b72&token=1963867963&lang=zh_CN#rd

众所周知，你要在查询的时候花得更少的时间，你就需要知道他的底层数据结构是怎么样的；举个例子：

- 树型的查找时间复杂度一般是O(logn)
- 链表的查找时间复杂度一般是O(n)
- 哈希表的查找时间复杂度一般是O(1)
- ….不同的数据结构所花的时间往往不一样，你想要查找的时候要**快**，就需要有底层的数据结构支持



# 入门

## kibana

* 简介：kibana Navicat是一个针对 elasticsearch mysql的开源分析及可视化平台，使用 kibana 可以查询、查看并存储在ES索引的数据进行交互操作



## 查询和过滤

* 参考：https://juejin.cn/post/6844903845613535245

Query DSL又叫查询表达式，是一种非常灵活又富有表现力的查询语言，采用JSON接口的方式实现丰富的查询，并使你的查询语句更灵活、更精确、更易读且易调试

### 查询和过滤的区别

> Elasticsearch（以下简称ES）中的数据检索分为两种情况：查询和过滤。
>
> Query查询会对检索结果进行评分，注重的点是匹配程度，例如检索“运维咖啡吧”与文档的标题有多匹配，计算的是查询与文档的相关程度，计算完成之后会算出一个评分，记录在`_score`字段中，并最终按照`_score`字段来对所有检索到的文档进行排序
>
> Filter过滤不会对检索结果进行评分，注重的点是是否匹配，例如检索“运维咖啡吧”是否匹配文档的标题，结果只有匹配或者不匹配，因为只是对结果进行简单的匹配，所以计算起来也非常快，并且过滤的结果会被缓存到内存中，性能要比Query查询高很多

## 官网术语和node es模块的转换

* @elastic/elasticsearch

### search

* 官网术语：

  ```js
  GET /ops-coffee-nginx-2019.05.15/_search
  // search 代表方法，索引对应 index
  ```

  * 指定一个索引 search

  * 具体的设置值都在js的body属性中写

    ```js
    Client.search({
      index: 'ops-coffee-nginx-2019.05.15', // 索引位置
      body: {
        "size": 5,
        "from": 10,
        "query":{
          "match_all": {}
        }
      }
    })
    ```

    

* node @elastic/elasticsearch

  ```js
  Client.search({
    index: 'ops-coffee-nginx-2019.05.15' // 索引位置
  })
  ```

#### 索引

* 索引的更多用法：https://juejin.cn/post/6844903845613535245#heading-2

  主要是索引的模糊用法

#### 分页

上边有说到查询结果`hits`默认只展示10个文档，那我们如何查询10个以后的文档呢？ES中给了`size`和`from`两个参数

**size：** 设置一次返回的结果数量，也就是`hits`中的文档数量，默认为10

**from：** 设置从第几个结果开始往后查询，默认值为0



#### [全文查询](https://juejin.cn/post/6844903845613535245#heading-4)

* https://juejin.cn/post/6844903845613535245#heading-4

##### match

最简单的查询，下边的例子就表示查找`host`为`ops-coffee.cn`的所有记录

```tex
GET /ops-coffee-2019.05.15/_search
{
  "query":{
    "match": {
      "host":"ops-coffee.cn"
    }
  }
}
```



##### multi_match

在多个字段上执行相同的match查询，下边的例子就表示查询`host`或`http_referer`字段中包含`ops-coffee.cn`的记录

```tex
GET /ops-coffee-2019.05.15/_search
{
  "query":{
    "multi_match": {
      "query":"ops-coffee.cn",
      "fields":["host","http_referer"]
    }
  }
}
```

* 如果不指定fields则查询所有字段

##### query_string （复杂查询）

可以在查询里边使用AND或者OR来完成复杂的查询，例如：

```
GET /ops-coffee-2019.05.15/_search
{
  "query":{
    "query_string": {
      "query":"(a.ops-coffee.cn) OR (b.ops-coffee.cn)",
      "fields":["host"]
    }
  }
}
复制代码
```

以上表示查找host为`a.ops-coffee.cn`或者`b.ops-coffee.cn`的所有记录

也可以用下边这种方式组合更多的条件完成更复杂的查询请求

```
GET /ops-coffee-2019.05.14/_search
{
  "query":{
    "query_string": {
      "query":"host:a.ops-coffee.cn OR (host:b.ops-coffee.cn AND status:403)"
    }
  }
}
复制代码
```

以上表示查询（host为`a.ops-coffee.cn`）或者是（host为`b.ops-coffee.cn`且status为403）的所有记录

与其像类似的还有个**simple_query_string**的关键字，可以将`query_string`中的AND或OR用`+`或`|`这样的符号替换掉

* 理论上能够使用 query_string 代替 match

##### term

==match在查询时，是进行了分词的操作。而term是代表完全匹配，即不进行分词器分析，文档中必须包含整个搜索的词汇==

term可以用来精确匹配，精确匹配的值可以是数字、时间、布尔值或者是设置了`not_analyzed`不分词的字符串

```tex
GET /ops-coffee-2019.05.14/_search
{
  "query":{
    "term": {
      "status": {
        "value": 404
      },
      "test": "value"
    }
  }
}
```

* todo

##### terms

* 多项查询

###### terms query

* 最简单的

```tex
GET /_search
{
  "query": {
    "terms": {
      "user.id": [ "kimchy", "elkbee" ], // 符合一项就行
      "boost": 1.0
    }
  }
}
```

* 还可以使用参数：https://www.elastic.co/guide/en/elasticsearch/reference/7.17/query-dsl-terms-query.html#query-dsl-terms-lookup-params

```js
GET my-index-000001/_search?pretty
{
  "query": {
    "terms": {
        "color" : {
            "index" : "my-index-000001",
            "id" : "2",
            "path" : "color"
        }
    }
  }
}
```





###### Terms Aggregation

多值聚合，根据库中的文档动态构建桶。基于词根的聚合，如果聚合字段是text的话，会对一个一个的词根进行聚合，通常不会在text类型的字段上使用聚合，对标关系型数据中的(Group By)。



##### range

* 区间的筛选

  参考：https://juejin.cn/post/6844903845613535245#heading-9

* range用来查询落在指定区间内的数字或者时间

  ```
  GET /ops-coffee-2019.05.14/_search
  {
    "query": {
      "range":{
        "status":{
          "gte": 400,
          "lte": 599
        }
      }
    }
  }
  ```

##### ***_source***返回指定字段

```
POST /shop/_search HTTP/1.1
Host: 192.168.10.235:9200
Content-Type: application/json
Content-Length: 120

{
    "query": {
        "match_all": {}
    },
    "_source": [
        "id",
        "nickname",
        "age"
    ]
}
复制代码
```

这样查询结果就只会返回 id、nickname、age 三个指定的字段。



#### bool-组合查询

通常我们可能需要将很多个条件组合在一起查出最后的结果，这个时候就需要使用ES提供的`bool`来实现了

例如我们要查询`host`为`ops-coffee.cn`且`http_x_forworded_for`为`111.18.78.128`且`status`不为200的所有数据就可以使用下边的语句

```
GET /ops-coffee-2019.05.14/_search
{
 "query":{
    "bool": {
      "filter": [
        {"match": {
          "host": "ops-coffee.cn"
        }},
        {"match": {
          "http_x_forwarded_for": "111.18.78.128"
        }}
      ],
      "must_not": {
        "match": {
          "status": 200
        }
      }
    }
  }
}
```

主要有四个关键字来组合查询之间的关系，分别为：

https://juejin.cn/post/6925707999247876110#heading-8

**must：** 类似于SQL中的AND，必须包含

```tex
POST /shop/_search HTTP/1.1
Host: 192.168.10.235:9200
Content-Type: application/json
Content-Length: 481

{
    "query": {
        "bool": {
            "must": [
                {
                    "multi_match": {
                        "query": "沙僧",
                        "fields": [
                            "nickname",
                            "desc"
                        ]
                    }
                },
                {
                    "term": {
                        "sex": 0
                    }
                }
            ]
        }
    }
}
```



**must_not：** 类似于SQL中的NOT，必须不包含

**should：** 满足这些条件中的任何条件都会增加评分`_score`，不满足也不影响，`should`只会影响查询结果的`_score`值，并不会影响结果的内容

**filter：** 与must相似，但不会对结果进行相关性评分`_score`，大多数情况下我们对于日志的需求都无相关性的要求，所以建议查询的过程中多用filter

* 如何添加评分

  *filter不会对结果进行相关性评分`_score`，可以在外层用 constant_score 进行包装，进行评分。https://www.elastic.co/guide/en/elasticsearch/guide/2.x/ignoring-tfidf.html*

##### ==注意==

* 上述四个关键字的查找，都需要在 bool 中进行，否则报错

  https://www.elastic.co/guide/en/elasticsearch/reference/7.17/query-dsl-bool-query.html

### 聚合

* 官方文档：https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-bucket-terms-aggregation.html

聚合的数据需要去aggregation中拿，https://juejin.cn/post/7028507565390561287#heading-3

查询

```tex
GET /car/_search
{
    "size" : 0,
    "aggs" : {
        "popular_colors" : { // 聚合到这个字段
            "terms" : {
                "field" : "color"
            }
        }
    }
}

```



返回值

```tex
{
    "took": 33,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": 8,
        "max_score": 0,
        "hits": []
    },
    "aggregations": {
        "popular_colors": { // 这个字段是自定义的
            "doc_count_error_upper_bound": 0,
            "sum_other_doc_count": 0,
            "buckets": [
                {
                    "key": "红",
                    "doc_count": 4
                },
                {
                    "key": "绿",
                    "doc_count": 2
                },
                {
                    "key": "蓝",
                    "doc_count": 2
                }
            ]
        }
    }
}

```



#### 概念

要掌握聚合，你只需要明白两个主要的概念：

- ***桶（Buckets）\***

  满足特定条件的文档的集合

- ***指标（Metrics）\***

  对桶内的文档进行统计计算

这就是全部了！每个聚合都是一个或者多个桶和零个或者多个指标的组合。

桶在概念上类似于 SQL 的分组（GROUP BY），而指标则类似于 `COUNT()` 、 `SUM()` 、 `MAX()` 等统计方法。



#### DSL搜索-过滤器 ***post_filter***

将金额money小于100并且大于1000的数据过滤掉。注意和 filter 关键字区分

```
POST /shop/_search HTTP/1.1
Host: 192.168.10.235:9200
Content-Type: application/json
Content-Length: 226

{
    "query": {
        "match": {
            "desc": "大学"
        }
    },
    "post_filter": {
        "range": {
            "money": {
                "gt": 100,
                "lt": 1000
            }
        }
    }
}
复制代码
```

***post_filter*** 和 ***query*** 是同级的



#### DSL搜索-排序 ***sort***

https://juejin.cn/post/6925707999247876110#heading-10

es默认是按 **_score** 排序

指定字段排序：

​	正序asc

​	倒序desc



多个排序条件，组合排序

```
POST /shop/_search HTTP/1.1
Host: 192.168.10.235:9200
Content-Type: application/json
Content-Length: 193

{
    "query": {
        "match": {
            "desc": "大学"
        }
    },
    "sort": [
        {
            "age": "asc"
        },
        {
            "money": "desc"
        }
    ]
}
复制代码
```

**注意：text类型无法排序，keyword类型可以**















