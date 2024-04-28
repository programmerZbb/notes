# tailwindcss 

## 简介

> 本 CSS 框架本质上是一个工具集，包含了大量类似 `flex`、 `pt-4`、 `text-center` 以及 `rotate-90` 等工具类，可以组合使用并直接在 HTML 代码上实现任何 UI 设计。

## 基础使用

参考：https://www.tailwindcss.cn/docs/installation

### 配置

* 编译型 css 框架
* 一个 `Postcss` 插件，通过修改 postcss ast 实现

Postcss 配置：

```js
export default {
  plugins: {
    // css 嵌套规则，由 tailwindcss 提供
    'tailwindcss/nesting': {},
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

Tailwind 配置，创建 tailwind.config.js

注意：

TailwindCSS并不会生成一个全量的样式包，而是根据具体使用到的语法生成对应的样式代码，这样可以确保打包产生的样式包是最小的。

### 使用

```html
<div class="w-32 h-32 bg-blue-500">
</div>
```

### 自定义 class 中使用 tailwind 语法

这里很想我们的组件思想

比如如下场景：

```html
<div class="p-2 text-gray-900 font-semibold">首页</div>
<div class="p-2 text-gray-900 font-semibold">学习TailwindCSS</div>
<div class="p-2 text-gray-900 font-semibold">TailwindCSS的设计哲学</div>
<div class="p-2 text-gray-900 font-semibold">最佳实践</div>
```

我们就可以像使用组件一样，抽象一个更高维度的 class：

```css
.menu {
  @apply p-2 text-gray-900 font-semibold;
}
```

#### 注意

1. 尽管使用这种方式能够解决样式重复的问题，但是tailwind并不推荐这样做，不建议在项目的早期就进行这样的抽象，tailwind会把他们编译成重复的样式，有可能会造成css文件变大。
2. 你可以通过抽象组件的方式避免抽象class。

具体参考：https://tailwindcss.com/docs/reusing-styles

### tailwind 预设之外的配置

```html
<div class="w-[139px] h-[77px] bg-[#165DFF]"></div>
```

### 出色的响应式方案

如果要想让其在`768px`以上的屏幕上显示为其他的颜色，只需要：

```html
<div class="w-32 h-32 bg-blue-500 md:max-lg:bg-green-500">hh</div>
```

响应式断点：

| Breakpoint prefix | Minimum width | CSS                                  |
| ----------------- | ------------- | ------------------------------------ |
| `sm`              | 640px         | `@media (min-width: 640px) { ... }`  |
| `md`              | 768px         | `@media (min-width: 768px) { ... }`  |
| `lg`              | 1024px        | `@media (min-width: 1024px) { ... }` |
| `xl`              | 1280px        | `@media (min-width: 1280px) { ... }` |
| `2xl`             | 1536px        | `@media (min-width: 1536px) { ... }` |

#### 注意

* 移动端优先，也就是移动端不需要加任何前缀。
* 断点的含义是 **大于等于**，即`min-width`，而非`max-width`。

### Dark Mode

得益于 tailwind 出色的 theme 配置能力，只需要在配置文件中配置：`darkMode: 'class'`，即可使用。

```html
{/* 测试暗黑 */}
<div class="w-32 h-32 bg-blue-500 dark:bg-yellow-500">暗黑</div>
```

### 修改、扩展 tailwind 主题系统

```js
export default {
  // 定制一些主题
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

* 使用

```html
{/* 测试修改扩展 */}
<div class="bg-myBlue"></div>
```

全量主题配置参考：https://github.com/tailwindlabs/tailwindcss/blob/master/stubs/config.full.js

## 哲学&思想

* 只需书写 HTML 代码，无需书写 CSS，即可快速构建美观的网站。

* 放弃使用css预处理器。参考：https://tailwindcss.com/docs/using-with-preprocessors

* 成为你的设计系统的 API（设计系统的语言）

* 体积小 - 绝不包含任何用不到的 CSS 代码。

  Tailwind 会在针对生产环境进行构建时自动删除所有未使用到的 CSS 代码，也就是说 你所获得的最终的 CSS 代码包的尺寸是最小的。事实上，大部分 Tailwind 项目所生成的 CSS 代码包都小于 10kB 。



# 问题&思考

## 1. css 预处理器还有未来吗？

肯定有，不是所有场景都适合使用原子化css

* css 的语义化
* 组件 css 的覆盖

## 2. 与 css in js 比较

css in js 缺点：

* css-in-js 运行时解析的实现版本增加了运行时性能压力，尤其在 React18 调度机制模式下，存在无法解决的性能问题（运行时插入样式会导致 React 渲染暂停，浏览器解析一遍样式，渲染再继续，然后浏览器又解析一遍样式）。

* 增加了包体积。相比原生或者 css-modules 方案来说，增加了运行时框架代码 8kb 左右。

* 让 ReactDevTools 结构变得复杂，因为 css-in-js 会包裹额外的 React 组件层用来实现样式插入。

# unocss

## 作者

https://github.com/antfu

## 为什么需要了解？

https://antfu.me/posts/reimagine-atomic-css-zh

tailwind 存在的问题：

* 编译时的拖累
* 可扩展性的缺失

## 基础——做了什么改进

* UnoCSS 是一个**引擎**，而非一款**框架**，因为它**并未提供核心工具类**，所有功能可以通过预设和内联配置提供。

* 更优秀的编译时

  > 从结果来看，UnoCSS 可以比 **Tailwind 的 JIT 引擎快 200 倍**！说实话，在按需生成的情况下，Windi 和 Tailwind JIT 都已经算是超快了，UnoCSS 的性能提升感知度可能没有那么高。然而，几乎为零的开销意味着你可以将 UnoCSS 整合到你现有的项目中，作为一个增量解决方案与其他框架一同协作，而不需要担心性能损耗。

  现在说是比 `tailwindcss` 快5倍，其实当年可是比 `tailwindcss JIT` 模式快200倍的存在，后面 `tailwindcss` 应该是做了大量的优化工作。

* 属性化模式

  ```html
  <div class="m-2 rounded text-teal-400" />
  <!-- 现在你可以这么写： -->
  <div m-2 rounded text-teal-400 />
  ```

* 前缀组

  ```html
  <div class="hover:bg-gray-400 hover:font-medium font-light font-mono"/>
  <!-- 简化之后： -->
  <div class="hover:(bg-gray-400 font-medium) font-(light mono)"/>
  ```

* 牛逼的指令系统

​	配置参考：

```js
// 可以做一些业务组件样式集合
shortcuts: [
  // dynamic shortcuts
  [/^btn-(.*)$/, ([, c]) => `bg-${c}-400 text-${c}-100 py-2 px-4 rounded-lg`],
]
```

* 检查器——牛逼的生态

  本地服务器可以打开：http://localhost:5173/__unocss#/，查看所有动态生成的class和大小

* 完美的icon系统

  Antfu的思考：https://antfu.me/posts/icons-in-pure-css-zh

  https://unocss.dev/presets/icons

## 把 tailwind 作为预设

```js
// uno.config.ts
import {
  defineConfig,
  presetWind,
  presetAttributify,
  transformerVariantGroup,
  transformerDirectives,
  presetIcons,
} from 'unocss'
import { FileSystemIconLoader } from '@iconify/utils/lib/loader/node-loaders'
// import  {presetDaisy } from 'unocss-preset-daisy'

export default defineConfig({
  // ...UnoCSS选项
  presets: [
    presetWind(), // tailwind 预设
    presetAttributify(), // 属性化预设
    presetIcons({
      // 图标默认行为
      // extraProperties: {
      //   display: 'inline-block',
      //   'vertical-align': 'middle'
      //   // ...
      // },
      collections: {
        icon: FileSystemIconLoader(
          './src/assets/biz',
          svg => svg.replace(/#FFF/, 'currentColor'), // 替换白色为当前继承颜色
        ),
        // mdi 图标参考：https://pictogrammers.com/library/mdi/
        mdi: () => import('@iconify-json/mdi/icons.json').then(i => i.default), // 第三方mdi集合
      },
      scale: 1, // 相对于当前字体大小（1em）的缩放比例
      warn: true,
      prefix: 'i-', // 前缀，使用时相当于：prefix + collections名称:svg名称
    }), // 预设图标
    // presetDaisy(), // daisy 预设
  ],
  transformers: [
    transformerVariantGroup(), // 转换组
    transformerDirectives(), // 指令转换器
  ],
  // 可以做一些业务组件样式集合
  shortcuts: [
    // dynamic shortcuts
    [/^btn-(.*)$/, ([, c]) => `bg-${c}-400 text-${c}-100 py-2 px-4 rounded-lg`],
  ]
})
```

# 思考

* 目前阶段什么技术是我们要追踪的？