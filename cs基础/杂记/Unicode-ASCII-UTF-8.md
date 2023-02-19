阅读参考：https://cloud.tencent.com/developer/article/1441294

知乎参考：https://www.zhihu.com/question/23374078/answer/69732605

# ascII

- ASCII 码使用指定的7 位或8 位二进制数组合来表示128 或256 种可能的字符。标准ASCII 码也叫基础ASCII码，使用7 位二进制数（剩下的1位二进制为0）来表示所有的大写和小写字母，数字0 到9、标点符号， 以及在美式英语中使用的特殊控制字符。其中最后一位用于奇偶校验。
- 问题：ASCII是单字节编码，无法用来表示中文（中文编码至少需要2个字节），所以，中国制定了GB2312编码，用来把中文编进去。但世界上有许多不同的语言，所以需要一种统一的编码。



# Unicode

unicode狭义来说只是一个字符集

- Unicode把所有语言都统一到一套编码里，这样就不会再有乱码问题了。
- Unicode最常用的是用两个字节表示一个字符（如果要用到非常偏僻的字符，就需要4个字节）。现代操作系统和大多数编程语言都直接支持Unicode。



# unicode 和 ascII 的区别

- ASCII编码是1个字节，而Unicode编码通常是2个字节。 字母A用ASCII编码是十进制的65，二进制的01000001；而在Unicode中，只需要在前面补0，即为：00000000 01000001。
- 新的问题：如果统一成Unicode编码，乱码问题从此消失了。但是，如果你写的文本基本上全部是英文的话，用Unicode编码比ASCII编码需要多一倍的存储空间，在存储和传输上就十分不划算。

Unicode多了中文，不会乱码。但是有可能浪费空间



# urf-8

utf-8是编码规则，通俗说UTF-8是Unicode的一种实现方式

- 所以，本着节约的精神，又出现了把Unicode编码转化为“可变长编码”的UTF-8编码。
- UTF-8编码把一个Unicode字符根据不同的数字大小编码成1-6个字节，常用的英文字母被编码成1个字节，汉字通常是3个字节，只有很生僻的字符才会被编码成4-6个字节。如果你要传输的文本包含大量英文字符，用UTF-8编码就能节省空间。
- 

![img](https://ask.qcloudimg.com/http-save/1692602/377ilfsviw.png?imageView2/2/w/1620)

- 从上面的表格还可以发现，UTF-8编码有一个额外的好处，就是ASCII编码实际上可以被看成是UTF-8编码的一部分，所以，大量只支持ASCII编码的历史遗留软件可以在UTF-8编码下继续工作。



# ==这才是重点==

* Unicode是字符集，定义每个字符对应的数字。也就是十进制的数字。

* UTF-8、UTF-16等是编码格式，定义“字符对应的数字”如何以二进制的方式存储。

比如UTF-8还有一些符号位，这个是unicode中是不存在的，实现和定义分开。



https://www.zhihu.com/question/23374078/answer/24385963

简单来说：

- Unicode 是「[字符集](https://www.zhihu.com/search?q=字符集&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A24385963})」
- UTF-8 是「[编码规则](https://www.zhihu.com/search?q=编码规则&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A24385963})」

其中：

- 字符集：为每一个「字符」分配一个唯一的 ID（学名为码位 / 码点 / Code Point）
- 编码规则：将「码位」转换为字节序列的规则（编码/解码 可以理解为 加密/解密 的过程）



# 计算机中通用的字符编码工作方式

- ==在计算机内存中（只有内存中需要使用UNICODE编码！）==，统一使用Unicode编码，==当需要保存到硬盘或者需要传输的时候（保存或者传输的时候就需要压缩）==，就转换为UTF-8编码。
-  用记事本编辑的时候，从文件读取的UTF-8字符被转换为Unicode字符到内存里，编辑完成后，保存的时候再把Unicode转换为UTF-8保存到文件： 

![img](https://ask.qcloudimg.com/http-save/1692602/zs2m48pymr.png?imageView2/2/w/1620)

-  浏览网页的时候，[服务器](https://cloud.tencent.com/product/cvm?from=10680)会把动态生成的Unicode内容转换为UTF-8再传输到浏览器：

  ![img](https://ask.qcloudimg.com/http-save/1692602/zxks1cbo4v.png?imageView2/2/w/1620)

# js中的unicode

* js 中使用 \\u 加4位16进制的方式使用unicode。（utf-8是编码方式，不能使用）

```js
let str = 'dd\u006e'

let str1 = 'dd\u0061'
// 0061 就是小写a
```

## js 中 Unicode 使用问题

* 因为js中能够直接使用Unicode码，而js中的字符串不能够换行。

  因此后端传入的字符串需要执行 

  ```js
  str.replace('\u2028')
  ```

  



## js 中特殊字符转义

参考：http://tools.jb51.net/table/javascript_escape