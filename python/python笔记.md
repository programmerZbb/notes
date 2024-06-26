# 1. 初始

* 以`.py`文件后缀

* 可以用 python 命令执行

* 也可以直接输入 `python`，启动 python 解释器，然后数据要执行的语句

  指定 Python3 直接输入，`python3 `启动

## print 函数

* 可以原样输出打印的字符，有多少空格输出多少空格。遇到 `,`则输出一个空格。

## 输入

* Python 按用户输入的打印

  `>>> name = input()`

  用户输入的字符就会被保存在 name 变量中。

# 2. 语法

* Python 语法采用的缩进的方式，缩进必须存在，会报错
* 一个 `:` 代表下面的缩进的代码是一个代码块

```python
# 这是一段测试代码
a = 100
if a >= 0:
    print(a)
else:
    print(-1)
```

```python
import time 
# 引入模块
print(time.time())

```



# 3.基础

## 1. 数据类型和变量

* 在 Python 中，能够直接处理的数据类型如下几种：

* 整数

  Python 可以处理任意大小的整数，包括负整数。`110`、`-110`

  十六进制：`0xa11`

  对于很长的数，Python 允许在数字中间以`_`分割，写成`10_000_000`和`10000000`是一样的，

  十六进制也可以写 `0x12a_a34`

  ```python
  0xf4240 == 1_000_0_00
  // True
  ```

  



----------

# 视频内容

# 1. 简介

* 目前 Python 以3.x 为准

* Python 有官方版 和 发行版

  官方版：解释器和标准库，带来的帮助是有限的

  发行版：解释器和标准库+常用的软件包。Python 科学计算会安装很多的软件包，软件包之间是有依赖关系的，会出现依赖失败的情况。初学者不需要考虑依赖关系，依赖都集成了。

  Python 最流行的 发行版 anaconda