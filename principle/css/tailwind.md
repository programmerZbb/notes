# 基础

## tailwind css preflight

Tailwind css 预置了一些样式，可以在跟节点的css看到

```css
/* tailwindcss预置 */
@tailwind base; /* Preflight will be injected here */
@tailwind components;
@tailwind utilities;
```

* 具体预置样式参考：https://www.tailwindcss.cn/docs/preflight

可以关闭预置配置：

* tailwind.config.js

```typescript
module.exports = {
  corePlugins: {
    preflight: false,
  }
}
```

## 响应式方案

### 屏幕划分

| 设备划分                 | 尺寸区间            | 宽度设置 |
| ------------------------ | ------------------- | -------- |
| 超小屏幕（手机）         | < 768px             | 100%     |
| 小屏设备（平板）         | >= 768px ~ 992px    | 750px    |
| 中等屏幕（桌面显示器）   | >= 992px ~ < 1200px | 970px    |
| 宽屏设备（大桌面显示器） | >=1200px            | 1170px   |

### tailwind 响应式设计

#### 断点

| Breakpoint prefix | Minimum width | CSS                                  |
| ----------------- | ------------- | ------------------------------------ |
| `sm`              | 640px         | `@media (min-width: 640px) { ... }`  |
| `md`              | 768px         | `@media (min-width: 768px) { ... }`  |
| `lg`              | 1024px        | `@media (min-width: 1024px) { ... }` |
| `xl`              | 1280px        | `@media (min-width: 1280px) { ... }` |
| `2xl`             | 1536px        | `@media (min-width: 1536px) { ... }` |

#### 修饰符——用来范围响应

| Modifier  | Media query                                      |
| --------- | ------------------------------------------------ |
| `max-sm`  | `@media not all and (min-width: 640px) { ... }`  |
| `max-md`  | `@media not all and (min-width: 768px) { ... }`  |
| `max-lg`  | `@media not all and (min-width: 1024px) { ... }` |
| `max-xl`  | `@media not all and (min-width: 1280px) { ... }` |
| `max-2xl` | `@media not all and (min-width: 1536px) { ... }` |

## 在css文件中使用预处理器（嵌套的语句）

* 参考：https://tailwindcss.com/docs/using-with-preprocessors#nesting

因为 tailcss 原则上不推荐使用预处理器了，也就是只需要额外写css文件，但是如果你想在css文件中使用预处理器那种嵌套的写法，可以参考如上配置。

## 定制主题

不推荐直接使用 `[]` 的方式进行自定义没有预置的样式，而是推荐使用在 config 中对预置进行扩展，如下扩展一个颜色。

```tsx
{
  theme: {
    // 这里就是对原有配置进行扩展
    extend: {
      colors: {
        myBlue: '#000'
      },
    },
  },
}
```

### variants

* 自定义类似于 `hover:` 一样的



# 思想

## 不离开 html

> **不离开html** 、**少样式代码甚至零样式代码** 的价值观
>
> **总结** 预处理器的核心优势是为了让开发人员更快书写样式代码，而TailwindCSS旨在消除样式代码，既然开发人员都不用写样式代码了，那还需要预处理器干嘛？:smile:

## 与预处理器

Tailwind 推荐从项目中把 css 预处理器移除

* 参考：https://tailwindcss.com/docs/using-with-preprocessors

## html 上 class 过多？

> 以前我也会这样想，甚至在页面级别的样式中使用BEM规范，但是后来发现实属没必要，非常规整的class命名浪费的精力极大（比如苦思如何给页面元素命名），而获得收益又是非常小的（页面根本不会被复用）。
> 只有组件级别的抽象才适合用BEM，为了方便样式覆盖，有时候必须完全摒弃TailwindCSS的方案。
>
> https://juejin.cn/post/7200782261997338681?searchId=2023112116130204E00A5E6F423C918BD7

* 组件级别的抽象可能需要提供使用者可覆盖 class 名称，可能需要摒弃 tailwind css 方案

## 为什么设计系统习惯使用 100/200 这样的设计语言？

以下内容来自chatGPT:

prompt:

> 为什么设计系统喜欢使用100、200的表述方式？比如说颜色、阴影什么的。为什么不用10、20这样呢？

```
设计系统喜欢使用数字如100、200来表示颜色、阴影等属性的值，而不使用10、20等较小的数字，这主要是为了提供更多的精细度和区分度。以下是一些原因：

1. **精细度和区分度：** 使用更大的数字范围可以提供更多的取值选择，允许设计系统定义更多的不同属性值。这使得在设计中可以更精细地控制颜色、阴影等属性的表现，从而使设计更加具有灵活性和可定制性。

2. **可扩展性：** 使用更大的数字范围为设计系统留下了更多的空间，以便将来可以添加更多的属性值，而不会受到数字值耗尽的限制。这种扩展性可以使设计系统更具未来发展的潜力。

3. **清晰性和易读性：** 大数字往往更具有直观性和易读性，设计师和开发人员可以更容易地理解和解释这些数字所代表的具体属性值。较大的数字也可以直观地传达出属性值的大小或强度。

举例来说，如果一个设计系统使用100、200等数字来表示阴影的强度，那么可以轻松地区分出不同级别的阴影效果，例如100表示较浅的阴影，200表示较深的阴影，而使用10、20等较小的数字则可能无法提供足够的区分度。

综上所述，设计系统喜欢使用较大数字（如100、200等）来表示颜色、阴影等属性的值，是为了提供更多的精细度和区分度，以及更好的可扩展性和易读性。
```

# 组件

## 推荐组件库

https://daisyui.com/docs/install/?lang=zh_hans

纯css组件库，不需要依赖任何语言

## 关于组件库的思考

css 原子化是每个开发人员都尝试过的一种方式，也就是css提取公共的能力，但 tailwind css 不只有 css 原子化的能力，还提供了方便的预置css样式、方便的响应式系统、主题继承等，更关键的是它的任何预置都是可以很方便自定义的，并不需要去手动添加css样式。

* tailcss 在常规的 react 项目中使用

​	这样的话就舍弃了 tailwind 更强的其他能力，只利用了其css原子化和预置样式的能力，属实是浪费啊

* 在 tailcss 专用UI库中使用

  tailcss 专用组件库，需要学习新的开发范式，有一定的学习成本，这个是一个不可忽略阻碍大家迁移的因素。而且 tailwind css UI lib 一般都是纯 css 样式组件库，不会提供一个组件常用能力的封装，这一点需要我们自己去开发，这个需要注意！

# 参考：

阅读参考：

* https://juejin.cn/post/7200782261997338681?searchId=20231120182124C930A78D0B4589DE9D1A#heading-5



# unocss

参考：https://juejin.cn/post/7244818201976078394?searchId=20231130131125C8B3E8AC34DA07821A2D

代码：`/Users/zhangbinbinb28199/workspace/code/some-practice/some-practice/project/tailwindcss/tailwindcss`

## 自定义值

不需要像 tailwind 自定义值需要使用 `bg-[#fff]` 的方式，只需要使用 bg-#fff 的方式，但是不推荐这样使用，因为可读性太差！

## icon 处理

* 注意：只限于icon处理（也就是svg，因为只处理了svg的方案），其他的png可能不合适，因为其使用了 css: mask 的方案

  > 阅读参考：https://www.zhangxinxu.com/wordpress/2017/11/css-css3-mask-masks/

## 问题

* 可读性问题
* 没有条件class
* 动画太麻烦
