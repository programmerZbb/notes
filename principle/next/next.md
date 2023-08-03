# 入门

## 官方宣传

* 这是一个用于成产环境的react框架

  > Next.js 为您提供生产环境所需的所有功能以及最佳的开发体验：包括静态及服务器端融合渲染、 支持 TypeScript、智能化打包、 路由预取等功能 无需任何配置。

	* 看起来像是一个基于react的全场景最佳实践

## 为什么选next呢

* 零配置。自动编译并打包，从一开始就位生产环境而优化

* 混合模式：ssr 和 ssg

  在一个项目中同时支持构建时预渲染页面（SSG）和请求时渲染页面（SSR）

* 增量静态生成

  在构建之后以增量的方式添加并更新静态预渲染的页面。

# 创建next.js应用程序

## 前言

要从头开始使用 React 构建一个完整的 Web 应用程序，需要考虑许多重要的细节：

- 必须使用打包程序（例如 webpack）打包代码，并使用 Babel 等编译器进行代码转换。
- 你需要针对生产环境进行优化，例如代码拆分。
- 你可能需要对一些页面进行预先渲染以提高页面性能和 SEO。你可能还希望使用服务器端渲染或客户端渲染。
- 你可能必须编写一些服务器端代码才能将 React 应用程序连接到数据存储。

一个 *框架* 就可以解决上述这些问题。但是，这样的框架必须具有正确的抽象级别，否则它将不是很有用。它还需要具有出色的“开发人员体验”，以确保您和您的团队在编写代码时拥有出色的体验。

* 对于一个生产环境，较大的收益就是拆包。拆包就是 包大小和网络流量的互相妥协



## api

### link

* 使用的是js导航
* 在生产环境中，只有页面出现 Link 组件，next会自动在后台预取连接页面的代码(只有生成环境有！)

### img

图片问题

* 确保图片是响应式的在不同的屏幕下
* 优化图片的体积通过第三方工具
* 懒加载问题

next 提供了 Image 组件帮助你处理这些问题

#### 存放

静态文件都存放到 public 目录下，自动处理路由

### metadata

修改元数据，比如 head 中的 title



### 自定义 document 默认的元素

* 包括：html, Head。只要是这个组件包含的区域都可以修改

```typescript
import { Html, Head, Main, NextScript } from 'next/document'

export default function Document() {
  return (
    <Html>
      <Head />
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  )
}
```

### 加载第三方js

* 推荐使用 Script 组件，能够选择加载策略

```typescript
<Script
src="https://connect.facebook.net/en_US/sdk.js"
strategy="lazyOnload"
onLoad={() =>
console.log(`script loaded correctly, window.FB has been populated`)
}
 />
```



## svg 处理

* 和常规的svg处理是一样的，采用了SVGR进行配置

​	参考：https://react-svgr.com/docs/next/

## 样式

### 全局样式

* 在pages目录下创建一个 _app.js 文件，所有的component都会经过它，因此都能加载全局样式

```js
import '../styles/globals.css'

export default function App({Component, pageProps}) {
    return <Component {...pageProps}/>
}
```



## 样式的使用技巧

### 1. 使用类抽象

* 建立一个 utils.modules.scss 作为一个工具类，方便不同的组件使用

### 2. 使用 clsx 库



## 文件路由

* next使用文件路由作为静态页面的路由，默认在 `pages` 文件夹下

* Next.js 13新增：`app directory` ，可以替代 pages 作为新的文件路由

  https://blog.logrocket.com/next-js-13-new-app-directory/

  https://beta.nextjs.org/docs/routing/fundamentals#the-app-directory

### app directory

next.js 提供了一组特别的文件去创建UI，有特定的行为通过嵌套路由：

* page.js 创建唯一的路由UI，并公开可访问
* route.js 创建服务侧的 api 端点
* layout.js 一个layout包裹页面和它们的child。他会为默认作为页面父组件
* loading.js 就是 suspense 的loading。需要再页面上使用 suspense
* not-found.js 当UI使用了 notFound 方法之后，就会呈现这个页面

```typescript
import { notFound } from 'next/navigation'

export default function Card() {
    notFound()

    return (
        <div>我是测试</div>
    )
}
```

### 自定义 document

使用自定义 document 能够更新 html和 body 标签。如果想要覆盖默认的 document，需要在文件系统 `pages/_document` 文件

* 参考：https://nextjs.org/docs/pages/building-your-application/routing/custom-document

## 预渲染和数据获取

### **hydration**

* 原意：水合、水合作用

为啥用这个词呢？刚下载的html只有UI结构，框架绑定的事件并没有绑定。hydration过程就像注水一样，让页面丰满起来。

### 预渲染形式

Next 有两种预渲染形式：static generation 和 server-side rendering，区别就是它何时为页面生成 HTML

* static generation 是预渲染在 build time。然后在每次请求呈现预渲染页面
* 服务端渲染在每次请求的时候生成html

> 开发环境是在每次请求进行预渲染。在生产环境中静态生成只发生一次，在构建时，不会每次请求都发生

### 混合渲染

重要的是，Next.js 允许您**选择**用于每个页面的预呈现表单。[您可以通过对大多数页面使用静态生成](https://nextjs.org/docs/basic-features/pages#static-generation-recommended)并为其他页面使用[服务器端呈现](https://nextjs.org/docs/basic-features/pages#server-side-rendering)来创建“混合”Next.js 应用程序。

### 什么时候使用静态生成和服务端渲染呢

建议尽可能的使用静态渲染（有数据和无数据），只需要在构建时生产一次，并由cdn提供服务，这比每次让服务器在请求时呈现页面要快

* 如果页面能在请求之前呈现，可以选择静态生成（比如：帮助中心、营销页面）
* 如果页面无法再请求之前呈现，并且每次请求页面都是不同的，可以使用 ssr。或者可以跳过预呈现并使用客户端js来填充经常更新的数据（和spa一样了）



### static generation

* next 默认为所有不需要数据的页面开启 static generate。
* 对于某些页面，如果不获取外部的数据就无法呈现页面。也许您需要在构建时访问文件系统、获取外部 API 或查询数据库。next 开箱即用支持这种情况。

#### 使用 `getStaticProps` 生成带数据页面

```typescript
export default function Home(props) { ... }

export async function getStaticProps() {
  // Get external data from the file system, API, DB, etc.
  const data = ...

  // The value of the `props` key will be
  //  passed to the `Home` component
  return {
    props: ...
  }
}
```

* 在导出组件的同时，需要导出一个 getStaticProps async 函数。

  该函数在生产构建时运行；并且在函数内部可以将获取到的数据作为 props 发送给导出的页面

> 在开发环境中，getStaticProps 在每次请求上运行

#### markdown 文件的 metadata

```markdown
---
title: 'When to Use Static Generation v.s. Server-side Rendering'
date: '2020-01-02'
---

We recommend using **Static Generation** (with and without data) whenever possible because your page can be built once and served by CDN, which makes it much faster than having a server render the page on every request.

You can use Static Generation for many types of pages, including:

- Marketing pages
- Blog posts
- E-commerce product listings
- Help and documentation

You should ask yourself: "Can I pre-render this page **ahead** of a user's request?" If the answer is yes, then you should choose Static Generation.

On the other hand, Static Generation is **not** a good idea if you cannot pre-render a page ahead of a user's request. Maybe your page shows frequently updated data, and the page content changes on every request.

In that case, you can use **Server-Side Rendering**. It will be slower, but the pre-rendered page will always be up-to-date. Or you can skip pre-rendering and use client-side JavaScript to populate data.
```

* 你可以看到md文件的顶部包含 title 和 date 等元数据。它们被叫做 YAMLFront Matter，它们能被 `gray-matter` 库解析

### 需要数据的预渲染

* 可以使用 fetch 去请求数据

```typescript
export async function getSortedPostsData() {
  // Instead of the file system,
  // fetch post data from an external API endpoint
  const res = await fetch('..');
  return res.json();
}
```

> next polyfills fetch() 在客户端和服务端，不需要专门引入它

* 这些请求代码在构建的时候运行在服务端，不会被发送到客户端



### [`fallback` key](https://nextjs.org/docs/api-reference/data-fetching/get-static-paths#fallback-false)

* todo

### [`getStaticPaths`](https://nextjs.org/docs/basic-features/data-fetching#getstaticpaths-static-generation)

todo



### 服务端渲染——解决需要频繁请求和变化的需求

* 需要导出 getServerSidProps。相对于普通的 spa 相当于给了页面默认值，比如一个列表页默认展示第一页

```typescript
export function getServerSideProps(context) {
    console.log(context.query, '-----context')
    return {
        props: {
            type: context.query.type || '1'
        }
    }
}
// 如上，根据请求参数展示不同页面
```

* 服务端渲染的 ttfc(time to first byte)要比静态生成慢，这个是已知的

### 客户端渲染策略



### SWR

* todo



## 三种渲染方式

* CSR: client side render
* SSG: Server Static generation
* SSR: Server Side Render



## 动态路由

* 我们页面的数据是根据外部的数据获得的，因此每个页面应该有不同的路由（也就是动态路由）

### 实现动态路由的步骤

1. 我们需要创建一个名为 `[id].js` 的文件，放到 `pages/posts` 目录下。在next.js 中动态路由的页面名需要以`[`开头以`]`结束

2. 在 `[id].js` 文件中书写正常的组件代码。需要导出一个 `getStaticPaths` async函数。在这个函数中，我们需要返回一个列表，包含所有可能的值。
3. 最后我们需要实现 `getStaticProps`，获取必要的数据通过给定的`id`。getStaticProps 函数有一个 `params` 参数，这个参数包含的动态路由id

![img](https://nextjs.org/static/images/learn/dynamic-routes/how-to-dynamic-routes.png)

### 实现

```typescript
export async function getStaticProps({params}) {
  // 会收到一个 params参数，这个参数的id就是要跳转的文章id
    const postData = await getPostData(params.id)

    return {
        props: {
            postData,
        }
    }
}

export async function getStaticPaths() {
    const paths = getAllPostIds()

    // 需要返回一个固定结构的对象，其中paths是一个如下的固定结构的数组
    /**
    	{
            params: {
                id: 'ddd',
            },
        }
    */
    return {
        paths,
        fallback: false,
    }
}
```

### 请求外部数据

* 还是使用 fetch api，具体查看文档

### 开发环境vs生产环境

* 开发环境：getStaticProps 运行在每次请求
* 生成环境：getStaticProps 运行在 build time

### fallback

* false: 如果是false，如果请求的路径不是 getStaticPaths 返回的路径，则相应一个404页面

* true: 如果是true，getStaticProps 变化的行为：

  * 如果是 getStaticPaths 返回的路径，则会在 build time进行渲染
  * 如果请求的路径不是 getStaticPaths 返回的路径，不会直接响应404页面。相反，next会创建一个 `fallback` 的页面版本在第一次请求这类路径的时候
  * 在后台，next 会静态生成一个请求路径的页面，为后续相同路径的请求提供服务，就像其他在构建时预渲染的页面一样

  说白了就是为不存在的路径提供一个fallback页面

* blocking：新的路径会服务端渲染通过`getStaticProps`，会为将来的请求提供缓存，仅仅在第一次请求时运行一次

说白了就是为页面提供兜底的，和react的相似

### 捕获所有的路由

* 动态路由能够延伸到捕获所有的路径，通过添加扩展运算符 `[...id]`
  * 能够匹配 `/posts/a`、 `/posts/a/b`、 `/posts/a/b/c`
* 如果想实现这种方式，在 getStaticPaths函数中，你必须返回一个 id 为数组的对象

```typescript
return [
  {
    params: {
      // Statically Generates /posts/a/b/c
      id: ['a', 'b', 'c'],
    },
  },
  //...
];
```

* 在 getStaticProps 中获取的 `params.id` 会是一个数组

  ```typescript
  export async function getStaticProps({ params }) {
    // params.id will be like ['a', 'b', 'c']
  }
  ```



### router

如果你想要访问 next 路由，你可以通过引入 useRouter hook从`next/router`

### 定制404页面

通过创建一个自定义的404页面，放在 `pages/404/js`。这个页面会静态生成在构建时

### Lambdas

* Lambdas

### api routes

* 可以用来做api mock。最简单的实践

```typescript
export default function handler(req, res) {
    res.status(200).json({text: 'hello'})
}
```

* req 和 res 和express的req、res是一致的，进行了适度的扩展

#### api routes 使用提示

##### 1. 不要 fetch 一个api在 getStaticProps 或者 getStaticPaths 中

不应该在 getStaticProps 或者 getStaticPaths 中请求一个api route。相反，我们应该直接写服务端代码在它们之中（或者直接调用helper函数）。

> 原因是这些代码不会被加载到客户端，那么这些请求也不会再客户端触发，建议直接写服务端代码

##### 2. 一个最佳实践——怎么去请求 api route

页面 form 表单发起 post 请求，这个api route不会是你客户端bundle的一部分，因此你可以安全的写服务端代码

#### 和 serverless 有什么联系

* todo

### 预览模式

static generation是非常有用的当你从一个没有 head 的cms系统中的页面请求数据时。然而，当你在一个cms系统中保存草稿并想实时预览草稿那么它就不太理想。（也就是常见的spa部分区域刷新的功能），你希望next去渲染这些页面再请求时而不是build time。

next 提供了一个预览模式去解决这个问题，它利用了 api routes。

> [预览模式](https://link.juejin.cn/?target=https%3A%2F%2Fvercel.com%2Fdocs%2Fnext.js%2Fpreview-mode)允许开发人员在将静态生成的内容发布到 Web 之前查看它的草稿。Next.js 能够在请求时间而不是构建时间生成这些草稿页面，以便开发人员可以看到他们的内容在发布时的样子。

### Dynamic api routes

* Todo 后期学



## 发布 next 应用

* npm run build
* npm run start

## 还有一些疑问

### 结合spa?

* 这个后期肯定要尝试的

### api route 能直接部署吗？不需要鉴权啥的？

* 尤其是和 serverless 什么关系



## 导出静态html

https://nextjs.org/docs/advanced-features/static-html-export

`next export` 允许你导出你的nextjs应用通过html，可以不通过nodejs渲染。

```json
{
  "build:html": "next build && next export"
}
```

> 注意：必须在本地启动node服务的情况下，才能导出html。也就是 `npm run start`，再执行 html export。

### 如果是 server side render 会发生什么

* 用 `getServerSideProps` 尝试一下

  不能导出使用 `getServerSideProps` 生命周期的页面！

* https://nextjs.org/docs/messages/gssp-export



## api

### public directory

* 使用import引入静态文件

  ```typescript
  import { ReactComponent as Svg } from 'public/my-logo.svg'
  ```



# 搜索引擎优化

## seo 简介

### 1. 什么是SEO

* seo 的目的是创建一种策略能够提升搜索引擎的排行。更高的排行意味着更高的自然流量，导致更多的业务





# 实战

## 同构

高端点的词Universal APP，为什么要同构，因为客户端渲染存在一个缺点，就是首屏加载过大文件或过多文件会变得特别慢，因此可以把首屏放在服务端来渲染来提升首屏速度，首屏加载过后路又开始交给客户端控制，又变成了SPA应用，整个代码都是用JavaScript来编写，服务端采用nodejs。



作者：前端周公子
链接：https://juejin.cn/post/6844903666906824718
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
