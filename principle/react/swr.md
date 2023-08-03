# 关于 http RFC stale-while-revalidate 提案

## http stale-while-revalidate

参考： https://zhuanlan.zhihu.com/p/64694485

* 是在强缓存失效后，在请求到新鲜数据前的一次缓存。（一般只触发一次）

## 使用场景

> 上面有说到`stale-while-revalidate`在 CDN 上的应用其实并不广泛，那在浏览器端，我们有这样的需求吗？大家都知道现在大部分静态资源都是非覆盖式发布，发一个新版本就会去改变这个资源 URL 中的版本号或者 hash 值，这种情况下，该资源的缓存时长会设置特别大，比如一年、三年甚至十年，在它到期之前，早就已经不在线上使用了，也就是说它永远都不会过期。`stale-while-revalidate`对非覆盖式发布的资源没有用处。
>
> 不过总有一些场景，资源的 URL 是不能变的，比如网站首页，例如 [https://www.taobao.com/](https://link.zhihu.com/?target=https%3A//www.taobao.com/)（淘宝首页是可缓存的静态页面）；又或者说要变 URL 的话成本很大，比如要交给第三方使用的 URL。这些场景下 `stale-while-revalidate`才可能派上用场。

* 静态资源不需要使用，推荐在 url 不变的场景使用，这就催生了前端 swr 的诞生。

# swr 入门

* Stale-while-revalidate 是在强缓存过期之后，先试用不新鲜的数据，后台异步请求新的数据。

  这个使用场景天然放到前端比较合适，因此swr出现比较合理。

参考：https://swr.vercel.app/zh-CN/docs/getting-started

## 相关文章

SWR 是银弹么 - 狒狒神的文章 - 知乎 https://zhuanlan.zhihu.com/p/617439248

## 前端 swr 的诞生

* [一步一步生成出swr](https://juejin.cn/post/7247028435339591740?searchId=20230727234312A3D4ADD550212C54247E)

swr 不是要修改我们自己封装的 api，而是要更优雅的使用 api，也就是我们在组件中 useEffect 中封装的方法进行优化，比如：缓存、重试、更新等。

## 入门

使用 useSWR，并且创建一个 fetcher

```typescript
import useSWR, { Fetcher } from 'swr'
import React, { FC } from 'react';

type Params = Parameters<typeof fetch>
const fetcher = (...args: Array<any>) => {
  const params: any = args
  return fetch(args[0], args[1]).then(res => res.json())
}

export const SWRComp: FC = props => {
  // 需要创建一个 fetcher
  const { data, error, isLoading } = useSWR<{name: string}, any>('http://127.0.0.1:8090/swr-profile', fetcher)
  if (error) return <div>出错了：{error?.message}</div>
  if (isLoading) return <div>loading...</div>

  return (
      <div>我是name: {data?.name}</div>
  );
};
```

### 常规组件遇到问题

> 通常，我们需要将所有的数据请求都保存在顶级组件中，并为树深处的每个组件添加 props。如果我们给页面添加更多的数据依赖，代码将变得更加难以维护。
>
> 虽然我们可以使用 [Context(opens in a new tab)](https://reactjs.org/docs/context.html) 来避免传递 props，但仍然存在动态内容问题：页面内容中的组件可以是动态的，顶级组件可能不知道其子组件将需要什么数据。

* 在发送一个请求，需要多个子组件共享数据的时候，需要一层一层传递props。要不就需要接入状态管理（比如说redux）。

### swr 怎么解决的——特点

https://swr.vercel.app/zh-CN/docs/getting-started#%E5%8F%AF%E5%A4%8D%E7%94%A8%E7%BB%84%E4%BB%B6

* 说白了就是多个子组件共同调用相同的 useSWR 的key，不会请求多次。

  > 最棒的是，只会有 **1 个请求** 发送到 API，因为它们使用相同的 SWR key，因此请求会被自动 **去除重复**、**缓存** 和 **共享**。

* 带缓存

* 聚焦页面自动请求

* 多个页面共享

## 数据更改

### mutate

useSWR 会自动的请求，那么怎么手动触发呢？这个就是useSWR提供的mutate api

相当于广播，所有用到这个key的hook都需要进行请求。

#### 全局数据修改

```typescript
import { mutate } from "swr"
 
function App() {
  mutate(key, data, options)
}
```

#### 绑定数据修改

* 也可以绑定只修改某个特定的key

```typescript
const { data, mutate } = useSWR('/api/user', fetcher)
// 也就是绑定了某个key，并不需要再指定key了
```

理论上在使用 mutate 的时候传递了数据，就不会触发 fetcher 函数，但是实践还是会触发！

#### 重新验证能力

* 如果出现退登等情况，可以通过全局的 mutate 进行广播，让所有相关的key都进行重新验证。

### useSWRMutation

```typescript
// 只能手动触发的hook，那我为啥不直接调用 fetcher 呢？
  const { trigger } = useSWRMutation<any>(LoginUrl, fetcher)
```

* 看来只有缓存和重试逻辑了

#### 场景

适合点击请求等按需加载的场景

## 发布订阅能力

* 通过mutate的使用，能够感觉到 swr 的广播能力，相关的key都会接受到 action。