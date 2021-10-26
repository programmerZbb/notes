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



# Element.getBoundingClientRect()

`**Element.getBoundingClientRect()**` 方法返回元素的大小及其相对于视口的位置。

https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect



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