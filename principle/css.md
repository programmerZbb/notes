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



# Element.getBoundingClientRect()

`**Element.getBoundingClientRect()**` 方法返回元素的大小及其相对于视口的位置。

https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect