参考：https://zhuanlan.zhihu.com/p/145173920

yaml to json: https://onlineyamltools.com/convert-yaml-to-json

# 1. 简介

**YAML** 是一种较为人性化的**数据序列化语言**，可以配合目前大多数编程语言使用。

YAML 的语法比较简洁直观，特点是使用空格来表达层次结构，其最大优势在于**数据结构**方面的表达，所以 YAML 更多应用于**编写配置文件**，其文件一般以 **.yml** 为后缀。

# 2. 基本语法

### 大小写敏感

```yaml
One: 1
one: 2
```

### 用缩进表示层级关系

* 缩进只能使用空格，不能用 TAB 字符
* 缩进的空格数量不重要，但是同一层级的元素左侧必须对齐

```yaml
# YAML
one:
  two: 2
  three:
    four: 4
    five: 5

// 以上的内容转成 JSON 后
"one": {
  "two": 2,
  "three": {
    "four": 4,
    "five": 5 
  }
}
```

### 用 # 表示注释

* 只能支持单行注释

### 一个文件中可以包含多个文件的内容

- 用“ **---** ”即**三个破折号**表示一份内容的**开始**
- 用“ **...** ”即**三个小数点**表示一份内容的**结束**（非必需）

```yaml
---
# 这是第一份内容
one: 1
# 其他内容...
...

---
# 这是第二份内容
two: 2
# 其他内容...
```



## 数据结构与类型

### 对象

表示以键值对（key: value）形式出现的数据

- 使用“**冒号+空格**”来分开**键**与**值**

```yaml
# YAML
key: value

// JSON
"key": "value"
```

- 支持多层嵌套（**用缩进表示层级关系**）。值不需要用引号

```yaml
# YAML
key:
  child-key1: value1
  child-key2: value2

// JSON
"key": {
  "child-key1": "value1",
  "child-key2": "value2",
}
```

- 支持**流式风格（ Flow style）**的语法（用花括号包裹，用逗号加空格分隔，类似 JSON）

```yaml
# YAML
key: { child-key1: value1, child-key2: value2 }

// JSON
"key": { "child-key1": "value1", "child-key2": "value2" }
```

### 数组

- 一组以**区块格式（Block Format）（即“破折号+空格”）**开头的数据组成一个数组。==注意空格！==

```yaml
# YAML
values:
  - value1
  - value2
  - value3

// JSON
"values": [ "value1", "value2", "value3" ]
```

- 同时也支持**内联格式（Inline Format）**来表达（用方括号包裹，逗号加空格分隔，类似 JSON）

```yaml
# YAML
values: [value1, value2, value3]

// JSON
"values": [ "value1", "value2", "value3" ]
```

- 支持多维数组（**用缩进表示层级关系**）

  ```yaml
  # YAML
  values:
    -
      - value1
      - value2
    -
      - value3
      - value4
  
  // JSON
  "values": [ [ "value1", "value2"], ["value3", "value4"] ]
  ```

### 标量（Scalars）

表示 YAML 中最基本的数据类型

### 字符串（String）

- 字符串**一般不需要用引号包裹**，但是如果字符串中**使用了反斜杠“\”开头的转义字符就必须使用引号包裹**

```yaml
# YAML
strings:
  - Hello without quote # 不用引号包裹
  - Hello
   world # 拆成多行后会自动在中间添加空格
  - 'Hello with single quotes' # 单引号包裹
  - "Hello with double quotes" # 双引号包裹
  - "I am fine. \u263A" # 使用双引号包裹时支持 Unicode 编码
  - "\x0d\x0a is \r\n" # 使用双引号包裹时还支持 Hex 编码
  - 'He said: "Hello!"' # 单双引号支持嵌套"

// JSON
"strings":
  [ "Hello without quote",
    "Hello world",
    "Hello with single quotes",
    "Hello with double quotes",
    "I am fine. ☺",
    "\r\n is \r\n",
    "He said: 'Hello!'" ]
```



## 数据重用和合并（变量&继承）

- 为了保持内容的简洁，避免过多重复的定义，YAML 提供了由**锚点标签“&”**和**引用标签“\*”**组成的语法，利用这套语法可以快速引用相同的一些数据...

```yaml
# YAML
a: &anchor # 设置锚点
  one: 1
  two: 2
  three: 3
b: *anchor # 引用锚点

// JSON
"a": {
  "one": 1,
  "two": 2,
  "three": 3
},
"b": {
  "one": 1,
  "two": 2,
  "three": 3
}
```

- 配合**合并标签“<<”**使用可以与任意数据进行合并，你可以把这套操作想象成面向对象语言中的继承...

```yaml
# YAML
human: &base # 添加名为 base 的锚点
    body: 1
    hair: 999
singer:
    <<: *base # 引用 base 锚点，实例化时会自动展开
    skill: sing # 添加额外的属性
programer:
    <<: *base # 引用 base 锚点，实例化时会自动展开
    hair: 6 # 覆写 base 中的属性
    skill: code # 添加额外的属性

// JSON
"human": { "body": 1, "hair": 999 },
"singer": { "body": 1, "hair": 999, "skill": "sing" },
"programer": { "body": 1, "hair": 6, "skill": "code" }
```