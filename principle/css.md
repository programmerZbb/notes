# 1. Grid 布局

* 采用网格布局的区域，称为"容器"（container）。容器内部采用网格定位的子元素，称为"项目"（item）。
* item 默认采用 content-box，如果不设置本身的宽度，则采用网格分割的宽高，包括margin 也会占用 item 分配的宽高。
* `grid-template-rows` 规定 的是 item 的长度，具体内容的长度，需要减去 margin

* 如果 item 内的内容长度超过 item 的长度，则按照 item 的长度压缩。比如 margin 设置 20 ，item 100px，那么你的内容只有 80px （！ 错误，永远保持你设置的长度，content-box。只不过 Chrome浏览器显示 102 px 加了 border ）

* item 内部的排列写法都是 item 与 flex 不同

  ```css
  justify-items
  ```

* Grid-template-areas 会覆盖 `grid-template-rows`

# 2. height

100% 是相对于内容的？



# 3. H5 开发问题

## 3.1 ios 异形屏

https://blog.csdn.net/shenxianhui1995/article/details/109113462



# 4. 水印的问题

* 水印就是 一个 cover 盒子在最上层，然后显示信息

* 主要用到

  ```css
  .cover {
    pointer-events: none; /* 主要是不阻挡遮挡的元素的事件 */
  }
  ```

  

# 5. css 通用属性值

## unset

如果CSS关键字 **`unset`** 从其父级继承，则将该属性重新设置为继承的值，如果没有继承父级样式，则将该属性重新设置为初始值。换句话说，在第一种情况下（继承属性）它的行为类似于[`inherit`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/inherit)似于[`initial`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/initial)CSS简写属性 [`all`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/all)

## all

* 所有属性默认值

  

CSS 通用属性值： [`initial`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/initial)https://developer.mozilla.org/zh-CN/docs/Web/CSS/inherit) and [`unset`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/unset)



# 可替换元素

在 [CSS](https://developer.mozilla.org/zh-CN/docs/Web/CSS) 中，**可替换元素**（**replaced element**）的展现效果不是由 CSS 来控制的。这些元素是一种外部对象，它们外观的渲染，是独立于 CSS 的。

比如图片的展示，视频的播放，iframe 的展示，都是不受控的。

## 控制内容框对象的位置

* 只能够控制对象的位置

[`object-fit`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/object-fit)

指定可替换元素的内容对象在元素盒区域中的填充方式。（有些类似于 [`background-size`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-size) ）

[`object-position`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/object-position)

指定可替换元素的内容对象在元素盒区域中的位置。（类似于 [`background-position`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-position)



# width

* 百分比：永远相对于的是父元素的 content-width。不论哪种盒模型。



# 动画

* animation 是逐帧执行的，能够修改每一帧的样式

## 动画和文档流

* 动画不会脱离文档流：比如 `translateX` ，它本身的浮动不会影响其他的元素，但是修改它的宽高还是会影响文档流中给它留下的位置。因此，并没有脱离文档流。
* 动画相当于提升了图层，能够使用 GPU 进行加速



# 盒模型

## 标准盒模型



## 行内元素自动换行问题

```html
<div class="inner">
  <span>
    dwada111
  </span>
  <span>
    dwada222dwadawdawdawdawdawdwadw
  </span>
</div>
```

* 上面第二个span，如果父盒子宽度小，则可能自动拐弯换行

# 高级api

## Element.getBoundingClientRect()——边界矩形

`**Element.getBoundingClientRect()**` 方法返回元素的大小及其相对于视口的位置。

* 通俗点说就是获取边界矩形

https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect

如果是标准盒子模型，元素的尺寸等于`width/height` + `padding` + `border-width`的总和。如果`box-sizing: border-box`，元素的的尺寸等于 `width/height`。

* 其实使用 style. 或者 offsetTop 等属性也能获取到这些熟悉，这个api只是提供了一个更为方便的方法访问这些属性。

* 返回的结果是包含完整元素的==最小矩形(如果行内元素换行，那就是最小包含矩形)==，并且拥有`left`, `top`, `right`, `bottom`, `x`, `y`, `width`, 和 `height`这几个以像素为单位的只读属性用于描述整个边框。除了`width` 和 `height` 以外的属性是相对于视图窗口的左上角来计算的。

  ==top、left是相对于视口的位置，滚动也会发生变化。非常方便==



## getClientRects()——获取矩形

https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getClientRects

`**Element.getClientRects()**` 方法返回一个指向客户端中每一个盒子的边界矩形的矩形集合。

> 起初，微软打算让这个方法给文本的每一行都返回一个TextRectangle，但是，CSSOM工作草案规定它应该给每个边框返回一个ClientRect。因此，对于行内元素这两个定义是相同的，但是对于块级元素，Mozilla只会返回一个矩形。（译者注：==对于行内元素，元素内部的每一行都会有一个边框；对于块级元素，如果里面没有其他元素，一整块元素只有一个边框==）。

### 与getBoundingClientRect的区别

* getBoundingClientRect 不论行内还是块级元素都返回矩形边界

* ==getClientRects 行内元素会返回每一行的边界，所以返回的是一个数组==

  行内元素自动换行——是指最后一个 span 如果内容过长可能出现折叠换行，内容不连续，该span被拆成了两部分，因此需要获取一个数组，分别定制

> 不过呢，**行内元素**会产生自动换行这类看似分割整体的歧义，所以，我会把行内元素根据它换行划分成*多个盒子边界矩形*。这也是我和我的兄弟——getBoundingClientRect的区别。
>
> https://zhuanlan.zhihu.com/p/38568124



## DOMRect 

一个 DOMRect 代表一个矩形

*`DOMRect` 从它的父类继承方法，[`DOMRectReadOnly`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMRectReadOnly)。* 不同之处在于它们不再是只读的。

DOMRectReadOnly 没有啥特殊的

# 类型选择器(后期需要总结)

https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Selectors

* 计算角度一定是从内到外出发，采用 id > class > 标签

  这个不一定，主要是首先考虑选择器的类型，采用  id > class > 标签，最后再比较距离。

* 如果选择器类型相同的话，谁越近权重越高

  同样如果选择器相同，谁的路径越长越高

* 权重 > 距离



1. 先看类型 id > class > 标签，从外到里
2. 类型相同看距离，越近越高
3. 距离不相同，则路径越长越好;(越长越精确)



## 相对选择器

https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Selectors#relative_selector



## 属性选择器

* 写法：全部在中括号内完成，类似于正则，需要写规则的写到等号的前边、属性的后边，需要写匹配规则的需要写到第二个参数。

  匹配规则和正则一样，需要采用 i(不区分小写) s(区分大小写，默认)的写法。

```css
a {
  color: blue;
}

/* 以 "#" 开头的页面本地链接 */
a[href^="#"] {
  background-color: gold;
}

/* 包含 "example" 的链接 */
a[href*="example"] {
  background-color: silver;
}

/* 包含 "insensitive" 的链接,不区分大小写 */
a[href*="insensitive" i] {
  color: cyan;
}

/* 包含 "cAsE" 的链接，区分大小写 */
a[href*="cAsE" s] {
  color: pink;
}

/* 以 ".org" 结尾的链接 */
a[href$=".org"] {
  color: red;
}
```



## 选择器列表

https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/Selectors

* 需要注意：兄弟选择器

  | [相邻兄弟选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Adjacent_sibling_combinator) | `h1 + p` | [相邻兄弟](https://developer.mozilla.org/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Combinators#Adjacent_sibling) |
  | ------------------------------------------------------------ | -------- | ------------------------------------------------------------ |
  | [通用兄弟选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/General_sibling_combinator) | `h1 ~ p` | [通用兄弟](https://developer.mozilla.org/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Combinators#General_sibling) |



# z-index 和 float

* float 是脱离文档流，是布局层面的修改
* z-index 是渲染层面的修改，是图层的提升。



# css 属性

## 1. clip-path

https://developer.mozilla.org/zh-CN/docs/Web/CSS/clip-path

* 用来裁剪方式创建元素的显示区域。对元素的显示区域进行裁剪。



## 2. pointer-events

* https://developer.mozilla.org/zh-CN/docs/Web/CSS/pointer-events

  鼠标事件怎么样穿透

  > 某个特定的图形元素可以成为鼠标事件的target

## 3. overflow

* overflow: overlay; 和auto表现一样，只能在 webkit 内核中使用，滚动条不会占据位置。

  在 Chrome 和 Safari 能使用

  https://developer.mozilla.org/zh-CN/docs/Web/CSS/overflow

## 4. filter

[CSS](https://developer.mozilla.org/zh-CN/docs/Web/CSS)属性 **`filter`** 将模糊或颜色偏移等图形效果应用于元素。滤镜通常用于调整图像、背景和边框的渲染。

### 函数

1. url：获取指向 SVG 滤镜的 URI，该 [SVG filter](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/filter) 可以嵌入到外部 XML 文件中。

2. blur: 使模糊

3. contrast 对比度

   filter: contrast(500%);

4. grayscale 改变图像灰度

   > [`grayscale()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter-function/grayscale) 函数将改变输入图像灰度。`amount` 的值定义了转换的比例。值为 `100%` 则完全转为灰度图像，值为 `0%` 图像无变化。值在 `0%` 到 `100%` 之间，则是效果的线性乘数。若未设置值，默认是 `0`。

5. hue-rotate 色相旋转
6. drop-shadow [`drop-shadow()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter-function/drop-shadow) 函数对输入图像应用阴影效果。



# lottie 动画

参考：剖析 lottie-web 动画实现原理 - 云音乐前端技术团队的文章 - 知乎 https://zhuanlan.zhihu.com/p/342477231

* 样例：https://lottiefiles.com/featured



# 1. 渐变色字体

1. 首先把背景色渐变：

   `background-image: liner-gradient(to right, #000， #fff)`

2. 背景剪裁，只有字体显示背景

   `background-clip: text;`

3. 字体颜色设置成透明：

   `color: transparent`

# flex

## flex 1 和 width 0

* 不知道原因，只说表象吧。如果需要 flex 1 的元素宽度需要父元素分配，则需要设置宽度为0；否则将会受到子元素宽度的影响！

> 如果没有设置width,当内部元素的内容大小超过平均分配的剩余空间时,元素的宽度等于内容大小,如果设置了width并且这个width的大小小于平均分配的剩余空间大小时,取平均分配的剩余空间;
> 当flex设置为 1 时 相当于 剩余空间大小 = 父元素的宽度 因此
> 平均的剩余空间大小等于 = 父元素的宽度 / 元素的个数
> 直接设置width为0可以保证元素宽度平分父元素宽度

## flex 怎么设置超出隐藏——flex省略号不出现

* 如果 flex: 1 沾满了，没给其他元素位置，也可以使用这个破解！

```less
.box {
  flex: 1,
  width: 0 // 这个是关键
}
```

## flex 子元素过长导致父元素兄弟元素被挤压

* 如题，也可以使用 width: 0 解决

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .parent {
            display: flex;
            align-items: stretch;
            height: 500px;
        }
        .child1 {
            background-color: red;
            flex: 1;
        }
        .child1-child {
            width: 1000px;
            background-color: blue;
            height: 300px;
        }
        .child2 {
            width: 100px;
        }
    </style>
</head>
<body>
    <!-- flex 子元素过长导致父元素兄弟元素被挤压 -->
    <div class="parent">
        <div class="child1">
            <div class="child1-child"></div>
        </div>
        <div class="child2"></div>
    </div>
</body>
</html>
```

* 子元素把父元素宽度撑大了，宽度设置为0，让flex去计算



# 文档流相关

## position

### Fixed 布局

* fixed 是相对于浏览器的位置

#### 什么情况下能影响 fixed 布局

* 在父元素使用了 translate 样式后，子元素的 fixed 布局会失效。会相对于父元素布局

# 工具

## scss



## classnames

* 这个工具能把驼峰转换成 - 拼接的

  https://github.com/JedWatson/classnames

# 开发中成长——问题记录

## 父组件overflow:hidden怎么破

* 可以使用一个代理盒子，这个盒子是一个 fixed 定位的盒子，相对于浏览器窗口的，需要超出显示的盒子写到这个代理盒子的里面即可实现。

## 新手引导样式怎么做

* 一个蒙层，怎么样让一个按钮穿过蒙层呢？也就是子元素穿过蒙层的样式呢？

![guide-intro](./imgs/css/guide-intro.png)

	* 实际上蒙层的样式是由父元素的 border-shadow 控制的，也就是这个蒙层完全就是border的阴影，子元素的样式就能直接穿透了。比如说不设置背景色就能拿到蒙层后的元素样式。（注意：不能用边框，动态的，不知道边框多大）

# css3

## 前缀

* 浏览器针对css3实现的不同，需要加前缀

  todo 前缀和浏览器内核的对应关系
