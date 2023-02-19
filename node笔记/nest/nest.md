# 简介

> Nest 是一个用于构建高效，可扩展的 [Node.js](http://nodejs.cn/) 服务器端应用程序的框架。它使用渐进式 JavaScript，内置并完全支持 [TypeScript](https://www.tslang.cn/)（但仍然允许开发人员使用纯 JavaScript 编写代码）并结合了 OOP（面向对象编程），FP（函数式编程）和 FRP（函数式响应编程）的元素。
>
> 在底层，Nest使用强大的 HTTP Server 框架，如 Express（默认）和 Fastify。Nest 在这些框架之上提供了一定程度的抽象，同时也将其 API 直接暴露给开发人员。这样可以轻松使用每个平台的无数第三方模块。

* 使用渐进式JavaScript
* 内置ts支持
* OOP、FP、FRP 编程思想
* 底层使用 express，进行了一定程度的抽象，也将api直接暴露给了开发人员

## 哲学

> 近年来，感谢 Node.js，JavaScript 已成为前端和后端应用程序的网络“通用语言”。这产生了令人敬畏的项目，如 [Angular](https://angular.cn/)，React 和 Vue，它们提高了开发人员的工作效率，并能够构建快速，可测试和可扩展的前端应用程序。然而，虽然 Node（和服务器端 JavaScript ）存在大量优秀的库，帮助器和工具，但它们都没有有效地解决主要问题 - 架构。

* 架构问题：express 并没有提供狠多开发实践（或者模板规范）对程序进行约束

  > Nest 提供了一个开箱即用的应用程序架构，允许开发人员和团队创建高度可测试，可扩展，松散耦合且易于维护的应用程序。



## 平台

* 默认 express，可使用其他框架，需要做适配器。

无论使用哪种平台，它都会暴露自己的 API。 它们分别是 `NestExpressApplication` 和 `NestFastifyApplication`。

将类型传递给 NestFactory.create() 函数时，如下例所示，app 对象将具有专用于该特定平台的函数。 但是，请注意，除非您确实要访问底层平台 API，否则无需指定类型。

```typescript
const app = await NestFactory.create<NestExpressApplication>(AppModule);
```



# MVC

* 看起来这些抽象的设定都是 MVC 决定的？那么啥是MVC，为什么要这样做？

# app

## 超时设置

```typescript
const server = await app.listen(3000);
server.setTimeout(1000);
```



# 控制器

==控制器负责处理传入的请求和向客户端返回响应==。就是一个桥梁

![img](https://docs.nestjs.com/assets/Controllers_1.png)



控制器的目的是接收应用的特定请求。**路由**机制控制哪个控制器接收哪些请求。通常，每个控制器有多个路由，不同的路由可以执行不同的操作。

为了创建一个基本的控制器，我们使用类和`装饰器`。装饰器将类与所需的元数据相关联，并使 Nest 能够创建路由映射（将请求绑定到相应的控制器）。

## 表现

* 一个基础类，用 @Controller 来装饰

## 路由

* 控制器能够传入路由前缀，使我们轻松的对一组相关的路由进行分组，并最大程度地减少重复代码
  * 只是一个前缀

```typescript
// 一个基础的控制器
@Controller('test')
class TestController {
  @Get('/getInfo')
  public getInfo(): string {
    return 'test info';
  }
}
```

什么是路由路径 ？ 一个处理程序的路由路径是通过连接为控制器 （Controller） 声明的（可选）前缀和请求装饰器中指定的任何路径来确定的。

* 就是控制器 + 请求装饰器传入的路径

> 例如，路径前缀 `customers` 与装饰器 `@Get('profile')` 组合会为 `GET /customers/profile` 请求生成路由映射。

### 为什么只需要返回一个值呢？

此函数将返回 `200` 状态代码和相关的响应，在本例中只返回了一个字符串。为什么会这样？ 为了解释原因，首先我们将介绍 Nest 使用两种不同的操作响应选项的概念：

|              |                                                              |
| ------------ | :----------------------------------------------------------- |
| 标准（推荐） | 使用这个内置方法，当请求处理程序返回一个 `JavaScript` 对象或数组时，它将自动序列化为 `JSON`。但是，当它返回一个 `JavaScript` 基本类型（例如`string、number、boolean`）时， Nest 将只发送值，而不尝试序列化它。这使响应处理变得简单：只需要返回值，其余的由 Nest 负责。 |
| 类库特有的   | 我们可以在函数签名处通过 `@Res()` 注入类库特定的响应对象（例如， `Express`）。使用此方法，你就能使用由该响应对象暴露的原生响应处理函数。例如，使用 `Express`，您可以使用 `response.status(200).send()` 构建响应 |

* 标准：不调用原生框架对象。直接返回一个js数据
* 使用类库：传入express 的 res 对象，就能调用 express 的方法

> 注意！Nest 检测处理程序何时使用 `@Res()` 或 `@Next()`，表明你选择了特定于库的选项。如果在一个处理函数上同时使用了这两个方法，那么此处的标准方式就是自动禁用此路由, 你将不会得到你想要的结果。如果需要在某个处理函数上同时使用这两种方法（例如，通过注入响应对象，单独设置 cookie / header，但把其余部分留给框架），你必须在装饰器 `@Res({ passthrough: true })` 中将 `passthrough` 选项设为 `true`

* 什么意思呢？现在还没看懂。就是设置完是否再交还给 nest 框架继续处理。

  比如如下：框架会默认进行重定向，如果你需要再重定向前进行一些操作，可以添加这个属性

```typescript
@Get('auth/login')
  @Redirect('/')
  async login(
    @Request() req,
    @Res({ passthrough: true }) res: ExpressRes,
    // @Session() session,
  ) {
    // 成功之后返回 token
    // console.log(req.session, '--9999');
    const { access_token } = await this.authService.login(req.user);
    // session.id = access_token;
    res.cookie('access_token', access_token);
    return access_token;
    // return req.user;
  }
```

## request 对象——获取客户端的请求数据（包括header）

@Req 注入

```typescript
import { Controller, Get, Req } from '@nestjs/common';
import { Request } from 'express';

@Controller('test')
export class TestController {
  @Get()
  public getInfo(@Req() req: Request): string {
    return 'test info';
  }
}
```

* Req 对象上挂载了所有express request 对象上的属性和方法，一般情况下不需要专门拿 req 对象去获取，有专门的装饰器。

> `Request` 对象代表 `HTTP` 请求，并具有查询字符串，请求参数参数，HTTP 标头（HTTP header） 和 正文（HTTP body）的属性（在[这里](https://expressjs.com/en/api.html#req)阅读更多）。在多数情况下，不必手动获取它们。 我们可以使用专用的装饰器，比如开箱即用的 `@Body()` 或 `@Query()` 。 下面是 Nest 提供的装饰器及其代表的底层平台特定对象的对照列表。

下面是装饰器对应列表：

|                           |                                   |
| :------------------------ | :-------------------------------- |
| `@Request()，@Req()`      | `req`                             |
| `@Response()，@Res()*`    | `res`                             |
| `@Next()`                 | `next`                            |
| `@Session()`              | `req.session`                     |
| `@Param(key?: string)`    | `req.params`/`req.params[key]`    |
| `@Body(key?: string)`     | `req.body`/`req.body[key]`        |
| `@Query(key?: string)`    | `req.query`/`req.query[key]`      |
| `@Headers(name?: string)` | `req.headers`/`req.headers[name]` |
| `@Ip()`                   | `req.ip`                          |
| `@HostParam()`            | `req.hosts`                       |

为了与底层 HTTP 平台（例如，`Express` 和 `Fastify`）之间的类型兼容， Nest 提供了 `@Res()`和 `@Response()` 装饰器。`@Res()` 只是 `@Response()` 的别名。两者都直接暴露了底层平台的 `response` 对象接口。

* 如果使用了这 @Res 装饰器后，需要手动响应数据。否则请求将被挂起。

## 资源

* 各种http method响应

> Nest 为所有标准的 HTTP 方法提供了相应的装饰器：`@Put()`、`@Delete()`、`@Patch()`、`@Options()`、以及 `@Head()`。此外，`@All()` 则用于定义一个用于处理所有 HTTP 请求方法的处理程序。



## 路由通配符

* 和 express 一样，支持路由通配符

路由路径 `'ab*cd'` 将匹配 `abcd` 、`ab_cd` 、`abecd` 等。字符 `?` 、`+` 、 `*` 以及 `()` 是它们的正则表达式对应项的子集。连字符（`-`） 和点（`.`）按字符串路径逐字解析。



## 下面都是 res 响应的设置，req 请求的获取使用上面的装饰器

## 状态码

如上所述，默认情况下，响应的**状态码**总是默认为 **200**，除了 POST 请求（默认响应状态码为 **201**

* post 请求响应码参考：http

通过 @HttpCode 装饰器进行修改

```typescript
// post 请求默认201
@Post('post')
// 修改http code
@HttpCode(200)
public getData() {
  return 'post data';
}
```

通常，状态码不是固定的，而是取决于各种因素。在这种情况下，您可以使用类库特有（library-specific）的 **`response`** （通过` @Res()`注入 ）对象（或者在出现错误时，抛出异常）。

* 如果有内部逻辑需要抛出错误，需要引入 res 对象，返回500

### HttpStatus

* nest 为我们设置了 httpCode 常量，HttpStatus
* 在实际项目开发中可以直接复制参考！！

## headers 

* header设置使用 @Header 装饰器
* 或者使用类库

### 设置 headers 和 获取 headers 区别

* 设置使用 @Header 装饰器

  content-type 参考：https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type

* 获取使用 @Headers 装饰器

## redirect

* `@Redirect()` 装饰器有两个可选参数，`url` 和 `statusCode`。 如果省略，则 `statusCode` 默认为 `302`。
* 也可以返回一个特定结构，动态修改重定向的内容

```typescript
// post 请求默认201
  @Post('post')
  // 修改http code
  @HttpCode(200)
  @Header('Cache-Control', 'none')
  @Redirect('https://www.baidu.com', 301)
  public getData() {
    // return 'post data';
    // 覆盖redirect
    return {
      url: 'https://www.figma.com',
      statusCode: 302,
    };
  }
```

## 函数签名

* 指函数的参数描述

## 路由参数

* 使用 @Param 装饰器，传递给函数签名
* 也可以传递变量名，获取指定的参数值

```typescript
@Get('/get/:id')
public getInfo(@Req() req: Request, @Param('id') params): string {
  console.log(req.url, params);
  return 'test info2';
}
```

## 子路由域

`@Controller` 装饰器可以接受一个 `host` 选项，以要求传入请求的 `HTTP` 主机匹配某个特定值。

* 限制子域

## 作用域

* todo

## 异步性

1. 直接使用 async 函数，Nest 能够自行解析它

   ```typescript
   @Get('async')
   public async getFile() {
     const result = await new Promise((res, rej) => {
       setTimeout(() => {
         res(2);
       }, 500);
     });
     return result;
   }
   ```

2. 这是完全有效的。此外，通过返回 RxJS [observable 流](http://reactivex.io/rxjs/class/es8/Observable.js~Observable.html)，Nest 路由处理程序将更加强大。 Nest 将自动订阅下面的源并获取最后发出的值（在流完成后）。

   ```typescript
   
   ```

* todo 待尝试！记得学习 rxjs

## request payload

* 在post请求时，我们可以通过 @Body 来获取请求的负载（payload）

* 需要定义一个 DTO （data transfer object）schema。只是作为一个约束

  > `DTO`是一个对象，它定义了如何通过网络发送数据。我们可以通过使用 **TypeScript** 接口（Interface）或简单的类（Class）来定义 DTO 模式。有趣的是，我们在这里推荐使用**类**。为什么？类是 JavaScript ES6 标准的一部分，因此它们在编译后的 JavaScript 中被保留为实际实体。另一方面，由于 TypeScript 接口在转换过程中被删除，所以 Nest 不能在运行时引用它们。这一点很重要，因为诸如**管道**（Pipe）之类的特性为在运行时访问变量的元类型提供更多的可能性。

  * 推荐使用类进行约束，类是es6的一部分
  * schema -> dto

## 从客户端拿取数据

一下三种方式

1. Query
2. Param
3. Body

## 错误处理

* Todo 后续学习

## 最后一步

* 有了 controller 之后，应该怎么样使用 controller 呢？

  我们需要再 module 装饰器中把 controller 引入

  > 我们使用 `@Module()` 装饰器将元数据附加到模块类中，现在，Nest 可以轻松反射（reflect）出哪些控制器（controller）必须被安装。

  * 新建一个 model 文件

  ```typescript
  import { Module } from '@nestjs/common';
  import { CatsController } from './cats/cats.controller';
  
  @Module({
    controllers: [CatsController],
  })
  export class AppModule {}
  ```

## 不得不使用类库

* 在一些情况下，不得不引用 http 类库的 res 对象进行操作。比如根据某种条件返回不同的状态码或者 cookie

  nest 推荐以下方式，你就能与底层框架原生的响应对象（Response）进行交互（例如，根据特定条件设置 Cookie 或 HTTP 头），并将剩余的部分留给 Nest 处理。

* 为什么不直接使用类库的方法呢？

  > 并且实际上通过提供对响应对象的完全控制（标头操作，特定于库的功能等）在某些方面提供了更大的灵活性，但应谨慎使用此种方法。通常来说，这种方式非常不清晰，并且有一些缺点。 主要的缺点是你的代码变得依赖于平台（因为不同的底层库在响应对象（Response）上可能具有不同的 API），并且更加难以测试（您必须模拟响应对象等）。

# providers

Providers 是 Nest 的一个基本概念。

许多基本的 Nest 类可能被视为 provider - `service`,` repository`, `factory`, `helper` 等等。

* 比如 service 处理逻辑，DBhelper 等

 他们都可以通过 controller 的 `constructor` **注入**依赖关系。

这意味着对象可以彼此创建各种关系，并且“连接”对象实例的功能在很大程度上可以委托给 `Nest`运行时系统。（注入到controller构造函数中）

## 表现

==Provider 只是一个用 `@Injectable()` 装饰器注释的类。==

![img](https://docs.nestjs.com/assets/Components_1.png)



## 作用

控制器应处理 `HTTP` 请求并将更复杂的任务委托给 **providers**。`Providers` 是纯粹的 `JavaScript` 类，在其类声明之前带有 `@Injectable()`装饰器。

* 将复杂的任务交给 provider 处理吧

一个官方小提示

> 由于 `Nest` 可以以更多的面向对象方式设计和组织依赖性，因此我们强烈建议遵循 [SOLID](https://en.wikipedia.org/wiki/SOLID) 原则。



## 服务

* 我们创建一个简单的 service 开始，该服务将负责数据的存储和检索，由一个 controller 使用，因此把它定义成为一个 provider，是一个很好的选择。

```typescript
import { Injectable } from '@nestjs/common';

// 使用 Injectable 装饰
@Injectable()
export class TestService {
  private readonly tests: Array<string> = [];

  public create(test: string): void {
    this.tests.push(test);
  }

  public getAll(): Array<string> {
    return this.tests;
  }
}
```

> 我们的 `CatsService` 是具有一个属性和两个方法的基本类。唯一的新特点是它使用 `@Injectable()` 装饰器。该 `@Injectable()` 附加有元数据，因此 `Nest` 知道这个类是一个 `Nest` provider
>
> todo 怎么实现的呢？了解一下，通过添加元数据就能知道？

* todo ts 私有的只读语法？？？也就是 construct 传入一个值就初始化了？

## 在 controller 中使用

> `CatsService` 是通过类构造函数注入的。注意这里使用了私有的只读语法。这意味着我们已经在同一位置创建并初始化了 `catsService `成员。

* 直接在 construct 中注入

```typescript
export class TestController {
  constructor(private readonly testService: TestService) {}
}
```

## 依赖注入

* 建议参考 angular

Nest 是建立在强大的设计模式，通常称为依赖注入。我们建议在官方的 [Angular文档](https://angular.cn/guide/dependency-injection)中阅读有关此概念的精彩文章。

* 说白了就是把依赖的对象传入。控制反转那一套

* 为什么是一个单例，很多控制器都能共享吗？todo 待实验

  他说的是一次请求实例化一个实例，因为js是单线程的，所以不会有多个线程共同操作实例

### ==重点==

* provider 能够注入到 controller 中使用，也能注入到 provider 中使用。

  因为 provider 可以跟随 module(载体) 到处传播，这才最大限度的发挥了 service 复用的能力！

## 作用域

* 没太明白？todo

> Provider 通常具有与应用程序生命周期同步的生命周期（“作用域”）。在启动应用程序时，必须解析每个依赖项，因此必须实例化每个提供程序。同样，当应用程序关闭时，每个 provider 都将被销毁

* provider 和应用程序的声明周期相同，应用程序启动时，必须解析每个依赖项。
* provider 的生命周期

但是，有一些方法可以改变 provider 生命周期的请求范围。您可以[在此处](https://docs.nestjs.cn/8/fundamentals?id=注射范围)详细了解这些技术。

## 自定义 provider

Nest 提供了一个内置的 IOC 容器，可以解决 providers 之间的关系。

`@Injectable()` 装饰器只是冰山一角, 并不是定义 providers 的唯一方法。相反，您可以使用普通值、类、异步或同步工厂。看看[这里](https://docs.nestjs.cn/8/fundamentals)找到更多的例子。

* 用 IOC 容器解决 provider 之间的关系
* @Injectable 并不是全部，只能装饰类。还有其他类型的值能够作为 provider

## 可选 provider

需要加装饰器 @optional

* 和自定义provider相关 todo 了解

## 基于属性的注入 ——todo

* 有些情况不一定要从 construct 注入，可能需要基于属性的注入。

nest 是可以基于属性注入的，nest 会赋值这个属性。

controller 例子

```typescript
@Controller()
class Controller {
  @Inject('FACTORY')
  private readonly factoryService: FactoryService;
}
```

## 手动实例化

到目前为止，我们已经讨论了 Nest 如何自动处理解决依赖关系的大多数细节。在某些情况下，您可能需要跳出内置的依赖注入系统，并手动检索或实例化提供程序。我们在下面简要讨论两个这样的主题。

* todo

## 全局 provider?

* 没有全局的 provider，provider 必须依附于 module 才能导出传递

# module

module 是具有 @Module() 装饰器的类，@Module() 装饰器提供了元数据，Nest用它来组织应用程序结构

![图1](https://docs.nestjs.com/assets/Modules_1.png)

问题：

1. 一个应用程序只有一个 module 吗？
2. 一个 controller 怎么样指定对应的 provider

> 每个 Nest 应用程序至少有一个模块，即根模块。根模块是 Nest 开始安排应用程序树的地方。事实上，根模块可能是应用程序中唯一的模块，特别是当应用程序很小时，但是对于大型程序来说这是没有意义的。在大多数情况下，您将拥有多个模块，每个模块都有一组紧密相关的**功能**。

* 至少有一个模块，那就是根模块
* 也可以有多个模块

## 使用

* 可以传入一下参数

| providers   | 由 Nest 注入器实例化的提供者，并且可以至少在整个模块中共享 |
| ----------- | ---------------------------------------------------------- |
| controllers | 必须创建的一组控制器                                       |
| imports     | 导入模块的列表，这些模块导出了此模块中所需提供者           |
| exports     | 由本模块提供并应在其他模块中可用的提供者的子集。           |

* module 会默认封装 providers，必须从 providers 获取 imports 中获取
* 导出的 provider 会视为 module 的api

## 共享module

实际上所有的 module 都能够共享，==因为任何模块只要创建都是一个单例==，那么就可以在任何模块中共享同一个模块中的 provider

* 只需要把 provider 在 exports 中导出

```typescript
@Module({
  imports: [CatsModule], // 只要导出module，就可以直接在 controller 中使用该 module 的所有 provider
  controllers: [AppController, TestController],
  providers: [],
  exports: ['new_provider'],
})
export class AppModule {}
```

* 可以轻松做到 module 的拆分

## module 导出

* module 不仅可以导出自己的 provider，还可以导出引入的 module

```typescript
@Module({
  imports: [CommonModule],
  exports: [CommonModule],
})
export class CoreModule {}
```

* 如果想使用 module 下的 provider，需要导出改 provider 并导入 module

## 模块的依赖注入

* provider 也可以注入到 module 中（例如，用于配置目的：todo不太清楚）

但是，由于[循环依赖](https://docs.nestjs.cn/8/fundamentals?id=circular-dependency)性，模块类不能注入到提供者中。

## 总结

* 模块作为 provider 和 controller的收口，所有想引入第三方 provider 的操作都需要通过 module，直接引入 module 即可。

## 全局模块

* 有一些场景需要一个全局的module，很多模块都可能需要，例如：helper、数据库连接等等。
* 使用 @Global 装饰器实现，不需要单独导入模块既能使用。
* 全局 module 也需要注册一次，最好由根或核心模块注册。在 module 中使用 import 导入

> `@Global` 装饰器使模块成为全局作用域。 全局模块应该只注册一次，最好由根或核心模块注册。

## 动态模块

`Nest` 模块系统包括一个称为动态模块的强大功能。此功能使您可以轻松创建可自定义的模块，这些模块可以动态注册和配置提供程序。

* 动态模块和动态 provider 是不是有些相似性呢？

  感觉module是一些列provider的集合

```typescript
import { Module, DynamicModule } from '@nestjs/common';

import {
  StaticProvider,
  createDatabaseProviders,
} from './dynamicModule.providers';

@Module({
  providers: [StaticProvider],
})
export class DatabaseModule {
  static forRoot(): Promise<DynamicModule> {
    const providers = createDatabaseProviders(); // createDatabaseProviders

    return new Promise((res, rej) => {
      res({
        module: DatabaseModule,
        providers,
        exports: providers,
        global: true, // 可以简单的导出全局模块
      });
    });
  }
}

```



# 中间件

* 中间件是在路由处理之前调用的函数。中间件函数可以访问请求对象和响应对象，以及应用程序请求响应周期中的 next 中间件函数。（和express一样）

定义 + 依赖注入 + 应用

## 定义

1. 函数形式，和express中间件一样，没有其他的约束
2. 类的形式，
   * @Injectable 装饰器
   * 实现 NestMiddleware 接口

## 依赖注入

nest 中间件完全支持依赖注入，就想 provider 和 controller 一样，能够注入属于同一模块的依赖项（通过constructor）

* 因为 mw 本身使用了 @Injectable 装饰了，因此可以在任何 module 的 provider 中使用。

## 应用

需要使用module 中的 config 方法去设置它，包含中间件的模块必须实现 NestModule

```typescript
// 该module应用了中间件
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer.apply(LoggerMiddleware).forRoutes('dogs');
  }
}
```

## forRoutes - 限制中间件应用范围

* 可以限定应用的路径和方法

  两个参数必须都配置

>我们还可以在配置中间件时将包含路由路径的对象和请求方法传递给`forRoutes()`方法。我们为之前在`CatsController`中定义的`/cats`路由处理程序设置了`LoggerMiddleware`。我们还可以在配置中间件时将包含路由路径的对象和请求方法传递给 `forRoutes()`方法，从而进一步将中间件限制为特定的请求方法。在下面的示例中，请注意我们导入了 `RequestMethod`来引用所需的请求方法类型。

## 路由通配符

* 和express看起来一样

## 中间件 consumer

MiddlewareConsumer 是一个帮助类，提供了几种内置的方法来管理中间件

* forRoutes 方法，用于限定作用的路由。可接受一个字符串、多个字符串、对象、一个控制器类甚至多个控制器类
* apply 方法，该 `apply()` 方法可以使用单个中间件，也可以使用多个参数来指定多个**多个中间件**。
* exclude 方法，排查某些路由，不可使用该中间件

中间件能够作用于某些路由，这些和express非常相似

## 函数中间件

* 如上

```typescript
import { NestMiddleware, Injectable } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';

// 函数中间件，不需要其他的限制
export const SomeMiddleware = (
  req: Request,
  res: Response,
  next: NextFunction,
) => {
  console.log('some middleware');
  next();
};

```

## 全局中间件

* 在 nest 工厂函数创建处根module后，直接使用use就可以应用全局中间件了

```typescript
async function bootstrap() {
  // 工厂函数需要传入一个model、option配置
  const app = await NestFactory.create(AppModule, {
    abortOnError: false, // 创建失败抛出错误
  });
  app.use((req, res, next) => {
    console.log('global middleware');
    next();
  });
  await app.listen(3000);
}
```



# 异常过滤器

内置的**异常层**负责处理整个应用程序中的所有抛出的异常。当捕获到未处理的异常时，最终用户将收到友好的响应。

## 基础异常类

`Nest`提供了一个内置的 `HttpException` 类，它从 `@nestjs/common` 包中导入。

* 对于典型的基于`HTTP` `REST/GraphQL` `API`的应用程序，最佳实践是在发生某些错误情况时发送标准HTTP响应对象。

  这是一种默认约定，发生错误的使用返回一个标准的 HTTP 响应对象

## 响应

* 规范：异常响应即需要 http code正确，也需要相应对象正确

  比如相应对象：

  ```typescript
  {"statusCode":403,"message":"Forbidden"}
  ```

* 在实践的过程中，也需要对该对象进行扩展。比如：

  ```typescript
  {"data":null,"success":false,"message":"Unknown Error.","code":404,"httpCode":404}
  ```

  * 需要自定义一套http code转换

## httpException

* 第一个参数可以是一个字符串，nest会自动拼接一个对象
* 第一个参数也可以是一个对象类型，直接对相应的对象进行定制，非常方便进行业务的定制

如下：

```typescript
throw new HttpException(
  {
    code: 403,
    message: 'forbidden',
    success: false,
  },
  HttpStatus.FORBIDDEN,
);
```

## 自定义异常

* 需要继承自 HttpException

比如如下，可以直接自定义一些数据结构

```typescript
import { HttpException, HttpStatus } from '@nestjs/common';

export class NestException extends HttpException {
  constructor(message: string, code: HttpStatus) {
    super(
      {
        message,
        success: false,
        code,
      },
      code,
    );
  }
}

```

## 内置的http异常

* nest 为了方便开发者，减少样板代码，提供了一些列继承自核心异常的 httpexception 的可用异常。都放在了 @nestjs/common 中

如下：

- `BadRequestException`
- `UnauthorizedException`
- `NotFoundException`
- `ForbiddenException`
- `NotAcceptableException`
- `RequestTimeoutException`
- `ConflictException`
- `GoneException`
- `PayloadTooLargeException`
- `UnsupportedMediaTypeException`
- `UnprocessableException`
- `InternalServerErrorException`
- `NotImplementedException`
- `BadGatewayException`
- `ServiceUnavailableException`
- `GatewayTimeoutException`

也可以基于这些进行异常定义，非常方便

## 异常过滤器

* 一个统一的异常处理过滤器，可以用来过滤、装饰、拦截http异常

  比如：对异常响应做一些日志收集

1. 用 @Catch 装饰器装饰，参数为异常类型。==可以定制特殊异常过滤==

2. 实现 ExceptionFilter 接口

   需要实现 catch 函数



> 虽然基本（内置）异常过滤器可以为您自动处理许多情况，但有时您可能希望对异常层拥有**完全控制权**，例如，您可能希望基于某些动态因素添加日志记录或使用不同的 `JSON` 模式。 **异常过滤器**正是为此目的而设计的。 它们使您可以控制精确的控制流以及将响应的内容发送回客户端。

* 需要通过express的原生对象进行处理（req，res等）

## ArgumentsHost

* 是一个功能强大的应用程序对象

> 让我们看一下该 `catch()` 方法的参数。该 `exception` 参数是当前正在处理的异常对象。该host参数是一个 `ArgumentsHost` 对象。 `ArgumentsHost` 是一个功能强大的实用程序对象，我们将在[应用上下文章节](https://docs.nestjs.cn/8/fundamentals?id=应用上下文) *中进一步进行研究。在此代码示例中，我们使用它来获取对 `Request` 和 `Response` 对象的引用，这些对象被传递给原始请求处理程序（在异常发生的控制器中）。在此代码示例中，我们使用了一些辅助方法 `ArgumentsHost` 来获取所需的 `Request` 和 `Response` 对象。`ArgumentsHost` 在[此处](https://docs.nestjs.cn/8/fundamentals?id=应用上下文)了解更多信息。

todo 后期深入了解

## 绑定过滤器

1. 单个路由绑定过滤器

使用 UseFilters 装饰器，传入过滤器类

```typescript
@Get('async')
@UseFilters(HttpExceptionFilter)
get() {}
```

2. 控制器范围内的过滤器

   直接用装饰器装饰控制器，控制器下所有的路由都会进行异常过滤

   ```typescript
   @Controller('test')
   // @Controller({
   //   host: 'my.site.com',
   // })
   @UseFilters(HttpExceptionFilter)
   export class TestController {}
   ```

3. 全局过滤器

   直接在根目录下使用 app 注册

   ```typescript
   app.useGlobalFilters(new HttpExceptionFilter());
   ```

   > 该 `useGlobalFilters()` 方法不会为网关和混合应用程序设置过滤器。

	* todo 网关和混合应用怎么做？

### 依赖注入

* 全局的过滤器不能注入依赖，因为它不属于任何 module，为了解决这个问题，你可以注册一个全局范围的过滤器直接为任何模块设置过滤器：

## 捕获异常

* 为了捕获内一个未处理的异常（不论异常类型如何），可以将 @Catch() 装饰器的参数列表设置为空

注意：目前还不能处理 Promise 错误

## 定制异常

* 需要继承 baseException，具体啥作用？不太清楚

  baseException 只是简单的处理了 http exception 返回500，一般情况下不需要对它进行增强

  参考：https://cloud.tencent.com/developer/section/1490171

```typescript
@Catch()
export class AllExceptionFilter2 extends BaseExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost): void {
    // console.log(exception, '----000---', host.switchToHttp);
    const ctx = host.switchToHttp();
    const response = ctx.getResponse();
    const request = ctx.getRequest();

    const status =
      exception instanceof HttpException
        ? exception.getStatus()
        : HttpStatus.INTERNAL_SERVER_ERROR;

    response.status(status).json({
      code: status,
      timestamp: new Date().toISOString(),
      path: request.url,
    });
    // super.catch(exception, host);
  }
}
```

```typescript
const { httpAdapter } = app.get(HttpAdapterHost);
  // 全局过滤器
  app.useGlobalFilters(
    new BaseExceptionFilter(),
    new AllExceptionFilter2(httpAdapter),
    new HttpExceptionFilter(),
  );
```

Todo 了解 http adaptor

# 管道

## 是什么

具有 @Injectable() 装饰器的类，应该实现 PipeTransform 接口

> `PipeTransform<T, R>` 是一个通用接口，其中 `T` 表示 `value` 的类型，`R` 表示 `transform()` 方法的返回类型。

## 类型

* 转换：将输入数据转换为所需的数据输出
* 验证：对输入数据进行验证，如果验证成功继续传递；验证失败则抛出异常

验证和转换的效果，也就是在路由处理参数之前进行处理

> 在这两种情况下, 管道 `参数(arguments)` 会由 [控制器(controllers)的路由处理程序](https://docs.nestjs.cn/8/controllers?id=路由参数) 进行处理. Nest 会在调用这个方法之前插入一个管道，管道会先拦截方法的调用参数,进行转换或是验证处理，然后用转换好或是验证好的参数调用原方法。

管道中发生异常，controller不会继续执行任何方法，有异常处理函数或者应用于当前上下文的异常过滤器处理。

## 内置 pipe

`Nest` 自带八个开箱即用的管道，即

- `ValidationPipe`
- `ParseIntPipe`
- `ParseBoolPipe`
- `ParseArrayPipe`
- `ParseUUIDPipe`
- `DefaultValuePipe`
- `ParseEnumPipe`
- `ParseFloatPipe`

他们从 `@nestjs/common` 包中导出。

## 为什么不用验证 中间件

* 如果用一个中间件去验证所有路由的输入，是不合适的，他们没有共同的参数

  中间件适合验证那些公共的验证，比如登录啥的

* 不建议直接在路由处理程序中进行参数校验，违反 SRP 单一职责

## 定义 pipe

每个管道必须提供 `transform()` 方法。 这个方法有两个参数：

- `value`
- `metadata`

`value` 是当前处理的参数，而 `metadata` 是其元数据。

元数据对象包含一些属性：

```typescript
export interface ArgumentMetadata {
  type: 'body' | 'query' | 'param' | 'custom';
  metatype?: Type<unknown>;
  data?: string;
}
```

| 参数     | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| type     | 告诉我们该属性是一个 body `@Body()`，query `@Query()`，param `@Param()` 还是自定义参数 [在这里阅读更多](https://docs.nestjs.cn/customdecorators)。 |
| metatype | 属性的元类型，例如 `String`。 如果在函数签名中省略类型声明，或者使用原生 JavaScript，则为 `undefined`。 |
| data     | 传递给装饰器的字符串，例如 `@Body('string')`。 如果您将括号留空，则为 `undefined`。 |

### 返回值

就像是前面说过的，`验证管道` 要么返回该值，要么抛出一个错误。

## 使用类定义类型

* 接口在编译的过程中会消失，建议使用类定义类型
* 也就是使用 dto

## 使用 joi 做参数验证

* 验证能力通过 ObjectSchema 的 validate 方法进行验证。schema 由初始化 pipe 时注入的 shcema 提供

```typescript
// 使用 hapi 提供的 validate 能力，直接验证。参考：https://joi.dev/api/?v=17.6.1
@Injectable()
export class ValidationPipe implements PipeTransform {
  constructor(private readonly schema: ObjectSchema) {}

  transform(value: any, metadata: ArgumentMetadata) {
    const { error } = this.schema.validate(value);
    if (error != null) {
      throw new NestException('Bad Request', HttpStatus.BAD_REQUEST);
    }

    return value;
  }
}
```

* 使用 pipe

```typescript
export class Dogs {
  // 加一下pipe验证
  @Get('create')
  @UsePipes(new ValidationPipe(CreateDogSchema)) // 使用pipe
  public createDog(@Param() createDogDto: CreateDogDto): string {
    console.log(createDogDto, '---params');
    return '创建成功';
  }
}
```

* 生成 schema

```typescript
import * as Joi from 'joi';

export class CreateDogDto {
  name: string;
  age: string;
  home: string;
}

export const CreateDogSchema = Joi.object({
  name: Joi.string().required(),
  age: Joi.string().required(),
  home: Joi.string().required(),
});

```

## 绑定 pipe

在 controller 中的路由方法上直接使用 @UsePipes() 装饰器创建一个管道实例，并注入验证 schema

* 可以绑定 controller 或者其 方法上

## 使用类验证器

nest 和 class-validator 配合的非常好

安装

```bash
npm i --save class-validator class-transformer
```

验证：

```typescript
// 使用 class-validator 来验证
@Injectable()
export class ValidationPipe2 implements PipeTransform {
  private toValidate(metatype: Type<any>): boolean {
    // 如果是下面几种，那就是普通的对象，不需要进一步验证，也无法验证
    const types: Type<any>[] = [String, Boolean, Number, Array, Object];
    return !types.includes(metatype);
  }

  async transform(value: any, metadata: ArgumentMetadata) {
    const { metatype } = metadata;
    if (!metatype || !this.toValidate(metatype)) {
      return value;
    }
    const object = plainToInstance(metatype, value);
    const errors = await validate(object);
    if (errors.length > 0) {
      throw new NestException('Bad Request', HttpStatus.BAD_REQUEST);
    }
    return value;
  }
}
```

* 注入

```typescript
@Get('create')
// @UsePipes(new ValidationPipe(CreateDogSchema))
@UsePipes(new ValidationPipe2())
public createDog(@Param() createDogDto: CreateDogDto): string {
  console.log(createDogDto, '---params');
  return '创建成功';
}
```



## 使用 pipe

* 支持异步的管道

  > 首先你会发现 `transform()` 函数是 `异步` 的, Nest 支持**同步**和**异步**管道。这样做的原因是因为有些 `class-validator` 的验证是[可以异步的](https://docs.nestjs.cn/typestack/class-validator?id=custom-validation-classes)(Promise)

## 范围

* 方法范围

  那就会对方法所有用控制器设置的类型参数进行验证，比如说有 @query 或者 @Param 都会分别验证。需要使用 @UsePipes 装饰器

* 控制器范围

* 全局范围

* 参数范围

  只对参数进行验证，参数层面的验证对验证一个指定的参数非常有用

## 设置全局管道

* 验证的 pipe 是可以全局使用的，只是传入的 dto 不一样，因此可以作为全局的管道使用

> 在 [混合应用](https://docs.nestjs.cn/8/faq?id=混合应用)中 `useGlobalPipes()` 方法不会为网关和微服务设置管道, 对于标准(非混合) 微服务应用使用 `useGlobalPipes()` 全局设置管道。

```typescript
app.useGlobalPipes(new ValidationPipe2());
```

* 使用了全局管道就不需要把每个参数进行单独的验证了

## 使用 provider 传递 pipe

* 好像没什么用，纯纯的 provider ，为啥不直接用 provider呢

## 转换 pipe —— 另一种使用方式

transform 函数返回的值会修改原先的参数

* 客户端传来的数据需要进行修改，比如一个字符串需要转换为数字。比如用户传入的 param id 是一个字符串，需要转换成数字
* 一些默认值，客户端没传，进行补全

### 转换一个 id 为number，去数据库中查找

```typescript
// 因为是做值转换，建议写输入的类型和输出的类型
export class ParseIntPipe implements PipeTransform<string, number> {
  transform(value: string, metadata: ArgumentMetadata): number {
    const val = parseInt(value);
    if (isNaN(val)) {
      throw new NestException('bad request', HttpStatus.BAD_REQUEST);
    }
    return val;
  }
}
```

其他场景：

* 或者直接用pipe去数据库中查找一个实体。（一个异步的pipe）
* ParseUUIDPipe 检验是否是 UUId 字符串

## 对客户端参数进行验证和处理

* 如上

## 内置验证管道

* 官方提供了一些内置的验证管道，不需要重写。

> `ValidationPipe` 需要同时安装 `class-validator` 和 `class-transformer` 包

# 守卫（guard）

## guard

守卫是一个使用 @Injectable() 装饰器的类，应该实现 CanActivate 接口。

## 为什么需要守卫

* 一般的鉴权都在中间件执行，但是中间件不知道调用 Next() 函数后会执行哪个程序。另一方面，守卫可以访问 `ExecutionContext` 实例，因此确切地知道接下来要执行什么。它们的设计与异常过滤器、管道和拦截器非常相似，目的是让您在请求/响应周期的正确位置插入处理逻辑，并以声明的方式进行插入。这有助于保持代码的简洁和声明性。

> 守卫有一个单独的责任。它们根据运行时出现的某些条件（例如权限，角色，访问控制列表等）来确定给定的请求是否由路由处理程序处理。 这通常称为授权。

> 守卫在每个中间件之后执行，但在任何拦截器或管道之前执行。

## 简单实现鉴权 guard

```typescript
// 实现一个鉴权守卫
import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common';
import { Observable } from 'rxjs'; // 自带rxjs
import { Request } from 'express';

@Injectable()
export class AuthGuard implements CanActivate {
  canActivate(
    context: ExecutionContext,
  ): boolean | Promise<boolean> | Observable<boolean> {
    // 可以返回这三种类型 nest 都能处理 // todo rxjs 尝试
    // 首先获取 request 对象
    const request = context.switchToHttp().getRequest<Request>();
    return validateRequest(request);
  }
}

const validateRequest = async (req: Request): Promise<boolean> => {
  const result = await Promise.resolve(true); // 后期需要实现 token 的解析
  return result;
};

```

两个todo

* rxjs 的实践（返回一个 Observer 数据）
* 鉴权 token 的定制

## ExecutionContext

ExecutionContext` 继承自 `ArgumentsHost。ArgumentsHost 是传递给原始处理程序的参数包装器，

> 是因为它 `ArgumentsHost` 可以在所有上下文中使用（例如，我们现在正在使用的 `HTTP` 服务器上下文，以及微服务和 `WebSocket` ）。在应用上下文章节中，我们将看到如何使用 `ArgumentsHost` 及其辅助函数访问任何应用上下文中相应的底层参数。这将使我们能够编写可在所有上下文中运行的通用异常过滤器。

* 借助 ArgumentsHost 能够拿到执行上下文中的底层参数。能够获取 request 或者 response 等底层参数

executionContext 对 argumentsHost 进行了扩展，提供了更多的功能，提供了有关当前执行过程的更多信息

```typescript
export interface ExecutionContext extends ArgumentsHost {
  getClass<T = any>(): Type<T>;
  getHandler(): Function;
}
```

* getClass 返回守卫作用与路由的 controller 类（不是实例）
* getHandler 返回当前路由的处理程序的引用

> `getHandler()`方法返回对将要调用的处理程序的引用。`getClass()`方法返回这个特定处理程序所属的 `Controller` 类的类型。例如，如果当前处理的请求是 `POST` 请求，目标是 `CatsController`上的 `create()` 方法，那么 `getHandler()` 将返回对 `create()` 方法的引用，而 `getClass()`将返回一个`CatsControllertype`(而不是实例)。

## 基于角色认证

* 实现一个 rolesGuard，这个守卫只允许具有特定角色的用户访问

## 绑定守卫

### 范围

* 控制器范围

  使用 @UseGuards 作用域控制器

* 方法范围

  使用 装饰器 @UseGuards

  ```typescript
  class Controller {
    @UseGuards(AuthGuard)
    @Get('create')
    // @UsePipes(new ValidationPipe(CreateDogSchema))
    // @UsePipes(new ValidationPipe2())
    public createDog(
      // @Param(new ValidationPipe2()) createDogDto: CreateDogDto,
      @Query() createDogDto: CreateDogDto,
      // @Param() createDogDto2: CreateDogDto,
    ): string {
      console.log(createDogDto, '---params');
      return '创建成功';
    }
  }
  ```

* 全局范围

  使用 app.useGlobalGuards()。每个控制器和每个路由处理程序都会应用。

  > 对于混合应用程序，`useGlobalGuards()` 方法不会为网关和微服务设置守卫。对于“标准”(非混合)微服务应用程序，`useGlobalGuards()`在全局安装守卫。

## 反射器

有一种场景，一个接口只对某些角色进行放开，路由对应不同的权限。（不同的路由提供不同的权限方案）

> `CatsController` 可以为不同的路由提供不同的权限方案。其中一些可能只对管理用户可用，而另一些则可以对所有人开放。我们如何以灵活和可重用的方式将角色与路由匹配起来?

那么久需要自定义元数据发挥作用的地方了（一种传递信息的方式，联系guard能够获取上下文信息）。nest 提供了通过 @SetMetadata() 装饰器将定制元数据附加到路由处理程序的能力，这些元数据提供了我们缺少的角色数据，而守卫需要这些数据来做出决策（路由对应什么角色）

### @SetMetadata

```typescript
class Controller {
  @Get('create')
  @SetMetadata('roles', ['admin']) 
  public createDog(
    // @Param(new ValidationPipe2()) createDogDto: CreateDogDto,
    @Query() createDogDto: CreateDogDto,
    // @Param() createDogDto2: CreateDogDto,
  ): string {
    console.log(createDogDto, '---params');
    return '创建成功';
  }
}
```

* 建议下面这种方式

```typescript
import { SetMetadata } from '@nestjs/common';

export const Roles = (...roles: string[]) => SetMetadata('roles', roles);
```

* reflector 获取到元数据信息

```typescript
@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(
    context: ExecutionContext, // 接收单参数 ExecutionContext 实例
  ): boolean | Promise<boolean> | Observable<boolean> {
    const roles = this.reflector.get<Array<string>>( // reflector get 能够获取元数据
      'roles',
      context.getHandler(),
    );
    if (roles == null) {
      return true;
    }
    const request = context.switchToHttp().getRequest<Request>();
    const cookies: string = request.headers.cookie;
    if (cookies != null && cookies.includes(roles[0])) {
      return true;
    }
    throw new NestException('Forbidden resource', 403);
  }
}
```



### 定制异常

一般情况下返回 false ，也可以直接抛出定制异常

实际上，返回 `false` 的守卫会抛出一个 `HttpException` 异常。如果您想要向最终用户返回不同的错误响应，你应该抛出一个异常。

```typescript
throw new UnauthorizedException();Copy to clipboardErrorCopied
```

由守卫引发的任何异常都将由异常层(全局异常过滤器和应用于当前上下文的任何异常过滤器)处理。



# 拦截器 interceptor

## 定义

* 使用装饰器 @Injectable() 进行装饰
* 应该实现 NestInterceptor 接口

![img](https://docs.nestjs.com/assets/Interceptors_1.png)

拦截器具有一系列有用的功能，这些功能受面向切面编程（AOP）技术的启发。它们可以：

- 在函数执行之前/之后绑定**额外的逻辑**
- 转换从函数返回的结果
- **转换**从函数抛出的异常
- 扩展基本函数行为
- 根据所选条件完全重写函数 (例如, 缓存目的)

### aop

* aop 技术启发

### intercept 方法

每个拦截器都有一个 intercept 方法，接受两个参数，第一个是 ExecutionContext 实例（与守卫的参数一模一样）。

> `ArgumentsHost` 是传递给原始处理程序的参数的一个包装 ，它根据应用程序的类型包含不同的参数数组。

* 不同类型的处理程序包含不同的参数数组？

### executionContext 

* 与 guard 一样

### callHandler

> 第二个参数是 `CallHandler`。如果不手动调用 `handle()` 方法，则主处理程序根本不会进行求值。这是什么意思？基本上，`CallHandler`是一个包装执行流的对象，因此推迟了最终的处理程序执行。

```typescript
@Injectable()
export class LoggingInterceptor implements NestInterceptor {
  intercept(
    context: ExecutionContext,
    next: CallHandler<any>,
  ): Observable<any> {
    console.log('before...');

    const now = Date.now();
    // 由于 handle() 返回一个RxJS Observable，我们有很多种操作符可以用来操作流。在上面的例子中，我们使用了 tap() 运算符，该运算符在可观察序列的正常或异常终止时调用函数。
    return next
      .handle()
      .pipe(tap(() => console.log(`After... ${Date.now() - now}ms`)));
  }
}
```

* 如上不主动执行 handle 方法，不会有响应。

## 截取切面

在函数执行之前或者之后添加额外的逻辑，我们要记录与应用程序交互时，它很有用

* 存储用户调用
* 异步调度事件
* 计算时间戳

主要是做一些统计的计算

## NestInterceptor 接口

> `NestInterceptor<T，R>` 是一个通用接口，其中 `T` 表示已处理的 `Observable<T>` 的类型（在流后面），而 `R` 表示包含在返回的 `Observable<R>` 中的值的返回类型。

## 绑定拦截器

### 范围

使用 `@UseInterceptors()` 装饰器

* 控制器范围
* 方法范围
* 全局范围

## 响应映射

* 对影响的数据进行修改

以下两个例子，一个是组装数据，另一个是转换数据

```typescript
import {
  CallHandler,
  ExecutionContext,
  Injectable,
  NestInterceptor,
} from '@nestjs/common';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';

// 实现一个响应转换器
@Injectable()
export class TransformInterceptor implements NestInterceptor {
  intercept(
    context: ExecutionContext,
    next: CallHandler<any>,
  ): Observable<any> {
    // 只是组装了一下数据
    return next.handle().pipe(
      map((value) => (value === null ? '' : value)),
      map((data) => ({ data })),
    );
  }
}
```

## 异常映射

* 看起来没啥用，ExceptionFilter 效果可能比这个更好

```typescript
@Injectable()
export class ExceptionInterceptor implements NestInterceptor {
  intercept(
    context: ExecutionContext,
    next: CallHandler<any>,
  ): Observable<any> {
    // 只是组装了一下数据
    return next
      .handle()
      .pipe(catchError((err) => throwError(new BadGatewayException())));
  }
}
```

## Stream 重写

> 有时我们可能希望完全阻止调用处理程序并返回不同的值 (例如, 由于性能问题而从缓存中获取), 这是有多种原因的。一个很好的例子是**缓存拦截器**，它将使用一些TTL存储缓存的响应

* 应用于某些缓存情况，直接返回数据，不经过路由处理程序

```typescript
// 实现一个响应转换器
@Injectable()
export class CacheInterceptor implements NestInterceptor {
  intercept(
    context: ExecutionContext,
    next: CallHandler<any>,
  ): Observable<any> {
    // 缓存
    const isCached = true;
    if (isCached) {
      // 如果命中缓存，直接修改响应流
      return of([]);
    }
    return next.handle();
  }
}
```

### TTl 缓存

> 1.80%的时间我们都在看那些20%的网站，这就是大名鼎鼎的80/20 Rule；
>
> 2.我们会在一个网站的不同网页之间跳转，也就是不断地访问同一个域名，类似程序访问的局部性原理。
>
> 这两条结论很容易让我们联想到**缓存机制**。如果我们将已经访问过的那些域名的解析结果缓存在自己的计算机上，那么下次访问的时候可以直接读取结果，不用再次重复DNS查询过程，给自己和域名服务器都节省了麻烦。
>
> ——https://www.dns.com/supports/594.html

TTl 详细：https://zhuanlan.zhihu.com/p/40372792

## 更多操作符

* 比如统一的 timeout 设置。这个场景还挺合适的。

```typescript
@Injectable()
export class ExceptionInterceptor implements NestInterceptor {
  intercept(
    context: ExecutionContext,
    next: CallHandler<any>,
  ): Observable<any> {
    // // 只是组装了一下数据
    // return next
    //   .handle()
    //   .pipe(catchError((err) => throwError(new BadGatewayException())));

    return next.handle().pipe(
      timeout(2000),
      catchError((err) => {
        if (err instanceof TimeoutError) {
          return throwError(new RequestTimeoutException());
        }
        return throwError(err);
      }),
    );
  }
}
```

## 个人理解

像是钩子，在路由处理程序之前或者之后，进行一些处理



# custom decorator

==这个是我们使用 nest 开发的重要封装区！！！==其他的地方封装的可能性较小

## 参数装饰器

* 官方提供了一些列非常实用的参数装饰器

  与 express 对应参考：

  https://docs.nestjs.cn/8/customdecorators?id=%e5%8f%82%e6%95%b0%e8%a3%85%e9%a5%b0%e5%99%a8

### 自定义装饰器

需要使用 createParamDecorator 函数

```typescript
import { createParamDecorator, ExecutionContext } from '@nestjs/common';

export const User = createParamDecorator(
  (data: unknown, context: ExecutionContext) => {
    const request = context.switchToHttp().getRequest();
    request.user = 111;
    return request.user;
  },
);
```

## 传递数据

* 自定义装饰器支持传递参数，会在 createParamDecorator 第一个参数体现

## 使用管道

你还可以直接将管道应用到自定义装饰器上

* 使用管道验证数据，需要设置 validateCustomDecorators 为true

```typescript
export class Controller {
  @Get('decorator')
  public getUser(
    @User(
      new ValidationPipe({
        validateCustomDecorators: true,
      }),
      'user',
    )
    user: UserDto,
  ): string {
    console.log(user, '----user');
    return 'user';
  }
}
```

## 装饰器聚合

主要解决多个装饰器太多，比较麻烦。提供一种方式把它们封装到一起，直接使用一个装饰器搞定

* 多个装饰器聚合成一个装饰器。使用方法 applyDecorators

```typescript
import { applyDecorators, Get } from '@nestjs/common';

export const GetDec = () => applyDecorators(Get('decorator'));
```





# 自定义 provider



* 上面学习了一种通过 构造函数（constructor）的依赖注入，并不是全部。

  依赖注入是一种基本的方式构建到了 Nest 内核中了。

  构造函数注入只是一种主要的模式，随着应用程序变得越来越复杂，您可能需要利用 DI 系统的所有特性

## 依赖注入

依赖注入式一种控制反转（IoC）技术，您可以将依赖的实例化委派给 IoC 容器。而不是在自己的代码中手动执行。

步骤：

1. 首先，我们定义一个提供者。`@Injectable()`装饰器将 `CatsService` 类标记为提供者。

   使用`@Injectable()`装饰器声明的类是一个可以由 Nest IoC 容器管理的类。

2. 在 controller 中注入（采用 constructor 注入）

3. 在 Nest IoC 容器中注册提供程序。

   主要是把 provider 传入providers 容器

总结

1. 定义一个 provider；2. 在 controller 中定义注入的 provider；3. 把 provider 传给IoC容器，让nest运行时传入。

### how

当 `Nest IoC` 容器实例化 `CatsController` 时，它首先查找所有依赖项*。 当找到 `CatsService` 依赖项时，它将对 `CatsService`令牌(`token`)执行查找，并根据上述步骤（上面的＃3）返回 `CatsService` 类。 假定单例范围（默认行为），`Nest` 然后将创建 `CatsService` 实例，将其缓存并返回，或者如果已经缓存，则返回现有实例。

* 实例化 controller 时查找依赖项
* 找到依赖项时，将对 provider token 执行查找（是利用 Reflect data吗？）
* 返回一个单例，如果是第二次创建，则返回已有的实例

## 自定 provider

* 为什么需要自定义 provider 呢？

  当你的要求超出标准 provider 的要求之后，你需要自定义provider。他们常常出现在以下场景：

  - 您要创建自定义实例，而不是让 `Nest` 实例化（或返回其缓存实例）类
  - 您想在第二个依赖项中重用现有的类
  - 您想使用模拟版本覆盖类进行测试

nest 支持自定义 provider 来解决以上问题。

实际上 provider 是由一个 token 加一个值组成的

* nest IoC 容器在查找 controller 依赖项的时候也是根据这个 provide (token)进行查找的。
* 在标准情况下，类名也就是 token
* provide 也就是 DI 令牌（token）

### useValue——修改provider的值，==给provider中动态注入参数！==

* 实际上 provider 是由一个 token 加一个值组成的

* `useValue` 语法对于注入常量值、将外部库放入 `Nest` 容器或使用模拟对象替换实际实现非常有用。假设您希望强制 `Nest` 使用模拟 `CatsService` 进行测试。

  * 模拟数据，引入外部库非常有用
  * 或者如果想注入一些常量，是非常有用的

  ```typescript
  @Module({
    imports: [CatsModule],
    providers: [
      {
        provide: CatsService,
        useValue: testService,
      },
    ],
  })
  export class AppModule {}
  ```

* useValue 需要和 token 拥有相同的接口类型

  ```typescript
  {
    providers: [
      {
        provide: ValueService,
        useValue: {
          getName() {
            return '22233';
          },
        },
      },
    ],
  }
  ```

这样也能够确保传入的值是一个单例（常量，对象字面量，直接初始化一个对象）



### 非类 provider——修改provider的key

* 我们已经使用了 类名 作为 token。有时我们希望灵活使用字符串或symbol 最为 DI token。

IoC容器注册

```typescript
[
  // 非类 provider
    {
      provide: 'CONNETION',
      useClass: NonClassService,
    },
]
```

Controller 注入

* 需要使用 Inject 装饰器，传入字符串 token

```typescript
constructor(
    @Inject('CONNETION') private readonly nonClassService: NonClassService,
  ) {}
```



### 类 provider —— useClass

* 使用 useClass 就能做到动态确定令牌应解析为的类。
* 说白了也就是一个接口



### 工厂 provider

* 灵活性更大，不是一个固定的，可以在函数中有分支

```typescript
[
  // 工厂provider。一定要返回具体的实例，而不是类。
    {
      provide: 'FACTORY',
      useFactory: (optionsProvider: FactoryService) => {
        console.log(optionsProvider, '----optionsProvider');
        if (true) {
          return new FactoryService();
        }
        return {
          test: '222',
        };
      },
      // 只能给工厂函数注入已经注册的 provider 的 token
      inject: ['CONNETION'],
      // useClass: FactoryService,
    },
]
```

* 由已经注册的 provider 进行新的provider计算。如果只想计算返回的值，建议使用 useValue，然后自定义一个新的工厂函数。
* 可以生产动态的 provider；也可以使用现有的 provider 生产新的 provider。

### 非类提供者

* 如上所述，provider也可能就是一个工厂函数生产的值

```typescript
const configFactory = {
  provide: 'CONFIG',
  useFactory: () => {
    return process.env.NODE_ENV === 'development'
      ? devConfig
      : prodConfig;
  },
};
```



### 导出自定义 provider

与任何提供程序一样，自定义提供程序的作用域仅限于其声明模块。要使它对其他模块可见，必须导出它。要导出自定义提供程序，我们可以使用其令牌或完整的提供程序对象。

* 使用 exports 导出token或者直接导出provider

  ```typescript
  @Module({
    exports: ['new_provider'],
  })
  ```

### 异步的 provider

* 有的时候，比如在使用 sql 服务的时候，我们希望在连接完成之前不要提供服务

  注意：有风险，在没有完成异步任务之前不能提供http服务，这是不合理的！会报错！

  这时候应该有一个前置的 nginx 守卫，去报告响应这个问题，告诉客户端服务不可用。

  ```typescript
  // async的provider。需要使用 工厂函数创建
  {
    provide: 'async_provider',
      useFactory: async (provider) => {
        const result = await new Promise((res) => {
          setTimeout(() => {
            res(provider);
          }, 6000);
        });
        return result;
      },
        inject: ['new_provider'],
  },
  ```

  



### 总结

* 说白了就是在 provider 注册阶段进行操作，不同的配置
* 目标就是能把代码组织起来，把依赖注入这个路径走通，注入指定的 provider

# 基本原理

## 动态模块

### 简介

* 模块定义了 provider 和 controller 这样的组件组，他们作为模块的一部分组合在模块中
* 模块为这些组件提供了执行上下文或范围（也就是模块内的组件能够共享数据），也就是说，组件内的 provider 对模块其他成员可见（controller和provider均可导入）。当provider需要再模块外可见时，他需要从主机模块导出，然后导入主机模块到其消费模块

### 主机模块

* 也就是 provider 的主体 module

### 消费模块

* 如题

### 配置模块

* 由动态模块引出，提供 api ，让消费模块在使用的时候根据 api 进行定制

> 换句话说，动态模块提供了一个 `API` ，用于将一个模块导入到另一个模块中，并在导入模块时定制该模块的属性和行为，而不是使用我们目前看到的静态绑定。

### 为什么不用动态模板获取环境变量，然后返回对应的服务？

* 服务端需要获取的变量过多，建议保存到全局的环境变量（比如：dbhos、eshost等）

* 全局环境变量不可能在启动服务的时候手动一个一个传入，因此一般情况下都是写一个 env 文件，动态读取文件。

  ==环境变量就是浏览器环境下的 window==

### 最佳实践

* env 文件的 interfaces 要定义在同文件夹下
* 常量要写在 constants.ts 中

## 注入作用域

> 来自不同语言背景的开发者,在学习Nest时可能预料不到在请求中几乎所有内容都是共享的。我们建立一个连接池到数据库,在全局状态下使用单例服务。 要记住Node.js并不遵循多线程下请求/响应的无状态模式。因此,在我们的应用中使用单例是安全的。

* nest 中在请求中所有的内容都是共享的。node 不遵循多线程下 请求/响应 的无状态模式，因此很多东西都是单例的。

  ==单例是nest中服务的特征==

> 然而,在需要考虑请求生命周期的情况下,存在边缘情况.例如,在GraphQL应用的预请求缓存中,以及请求追踪和多租户条件下,注入作用域提供了一个机制来获取需要的提供者生命周期行为.

* 如果想访问一个请求生命周期中的 provider 行为，需要提供一个注入作用域的能力
* todo 了解！

在 nest 中所谓的作用域，也就是说这个组件实例的范围是全局的、还是每次请求都实例一个、还是临时的（每次使用，单独实例一次）

### provider 范围

* 也就是说 provider 的作用域范围

有一下三种范围

* DEFAULT 每个provider 可以跨多个类共享。provider 生命周期严格绑定到应用程序生命周期（也就是应用程序启动的时候，才会进行实例化）。一旦应用程序启动，所有 provider 都已实例化。默认情况下使用单例范围
* REQUEST 在请求处理完成后，作为每个传入请求和垃圾收集专门创建提供者新实例。（每个请求对应一个新的 provider）
* TRANSIENT 临时 provider ，不能在provider 之间共享。每当其他的 provider 向容器请求特定的临时 provider 的时候，容器将创建一个新的专用实例

> Tips: 使用单例范围始终是推荐的方法。请求之间共享提供者可以降低内存消耗，从而提高应用程序的性能(不需要每次实例化类)。

* 推荐使用 单例范围 

### 使用

#### 默认配置

* Scope.DEFAULT 每次初始化的时候跟随程序一起初始化

#### 切换到请求范围内的作用域

* 每次请求都会重新进行实例

```typescript
@Injectable({
  scope: Scope.REQUEST,
})
export class ConfigService {}
```

* 只需要在 Injectable 装饰器中设置 scope，其他位置不需要变化

自定义 provider 的情况，需要再 provider 配置中添加设置

```typescript
{
  provide: 'dogsService',
  useClass: DogsService,
  scope: Scope.REQUEST,
}
```

> Tips: nest 做网关不能使用请求范围内的provider，因为每个网关都封装了一个 socket 并且不能多次实例化

### 控制器范围

* 需要在 Controller 装饰器中配置

```typescript
@Controller({
  path: 'cats',
  scope: Scope.REQUEST,
})
export class CatsController {}
```

### scope 层级

* 我们必须非常小心的使用请求范围的 provider，因为 scope 实际上是在==注入链==中冒泡的，如果设置了请求范围的 provider，则控制器实际上也是请求范围！！！

  因为 controller 要注入 provider，因此controller也要每次都重新实例

> 想象一下下面的链: `CatsController <- CatsService <- CatsRepository `。如果您的 `CatsService` 是请求范围的(从理论上讲，其余的都是单例)，那么 `CatsController` 也将成为请求范围的(因为必须将请求范围的实例注入到新创建的控制器中)，而 `CatsRepository` 仍然是单例的。

> 在这种情况下，循环依赖关系将导致非常痛苦的副作用，因此，您当然应该避免创建它们

* 这种副作用就是 controller 也变成了请求作用域

### 请求范围 provider——==在请求 provider中注入请求对象！==

* 在 http 应用程序中，当使用请求范围 provider 时，坑需要获取原始的请求对象。这通过注入 REQUEST 对象实现

```typescript
import { Injectable, Inject, Scope } from '@nestjs/common';
import path, { resolve } from 'path';
import * as fs from 'fs';
import * as dotenv from 'dotenv';
import { REQUEST } from '@nestjs/core';
import { Request } from 'express';

import { EnvConfig } from './interfaces';
import { CONFIG_OPTIONS } from './constants';

// 目的就是目录由外界注入
@Injectable({
  scope: Scope.REQUEST,
})
export class ConfigService {
  private readonly envConfig: EnvConfig;

  constructor(
    @Inject(CONFIG_OPTIONS) private options,
    @Inject(REQUEST) private request: Request,
  ) {
    console.log('开始实例', request);
  }
}
```

==也就是在provider中怎么注入请求对象！！！==

### 性能问题

* 使用请求范围的 provider 将明显影响应用程序性能！

> 使用请求范围的提供者将明显影响应用程序性能。即使 `Nest` 试图缓存尽可能多的元数据，它仍然必须为每个请求创建类的实例。因此，它将降低您的平均响应时间和总体基准测试结果。如果您的提供者不一定需要请求范围，那么您应该坚持使用单例范围。

## 循环依赖

当两个类互相依赖时就会出现循环依赖. 例如，当 `A` 类需要 `B` 类，而 `B` 类也需要 `A` 类时，就会产生**循环依赖**。`Nest` 允许在提供者( `provider` )和模块( `module` )之间创建循环依赖关系.

* nest 允许 provider 和 module 之间创建循环依赖，但是，尽量避免

> 建议尽可能避免循环依赖。但是有时候难以避免，Nest提供了两个方法来解决这个问题.本章中我们提供了两种技术，即`正向引用(forward reference)`和`模块引用(ModuleRef)`来从注入容器中获取一个提供者。

==如果循环引用不做处理，nest会报错！==

### 前向引用 （forward ref）

> **前向引用**允许 `Nest` 引用目前尚未被定义的引用。当`CatsService` 和 `CommonService` 相互依赖时，关系的双方都需要使用 `@Inject()` 和 `forwardRef()` ，否则 `Nest` 不会实例化它们，因为所有基本元数据都不可用。让我们看看下面的代码片段：

* 因为循环引用，会有一个鸡生蛋蛋生鸡的问题，谁先开始实例化。因此，nest 提供了前向引用，nest不会实例化它们。

```typescript
// 测试循环依赖
import { Injectable, forwardRef, Inject } from '@nestjs/common';

import { Service2 } from './service2.service';

@Injectable()
export class Service1 {
  constructor(
    @Inject(forwardRef(() => Service2))
    private service2: Service2,
  ) {}
}
```

### ModuleRef

todo

### 模块前向引用

* 上面讲了 provider 的前向引用，模块的前向引用

```typescript
@Module({
  imports: [forwardRef(() => CatsModule)],
})
export class CommonModule {}
```

> 注意：实例化的顺序是不确定的。不能保证哪个构造函数会被先调用。

## module ref

* nest 提供了一个 ModuleRef 类来导航到内部的 provider 列表，并且使用注入令牌作为查找键名来获取一个引用
* 可以用来动态处理 provider

### 获取实例

> `ModuleRef`实例(下文称为**模块引用**) 拥有`get()`方法。该方法获取一个提供者，控制器或者通过注入令牌/类名获取一个在当前模块中可注入对象(例如守卫或拦截器等)。

* 不能通过get 方法获取一个范围 provider （临时的或者请求范围的）

```typescript
@Injectable()
export class CatsService implements OnModuleInit {
  private service: Service;
  constructor(private moduleRef: ModuleRef) {}

  onModuleInit() {
    this.service = this.moduleRef.get(Service);
  }
}
```

* 为什么不直接注入呢？？？

### 获取全局的 provider

要从全局上下文获取一个提供者(例如，如果提供者在不同模块中注入)，向`get()`的第二个参数传递`{ strict: false }`选项。

```typescript
this.moduleRef.get(Service, { strict: false });
```

### 处理范围 provider

* 因为有可能没初始化，因此di不能直接注入。需要再 onmoduleinit 钩子中使用 moduleRef 获取

要动态处理一个范围提供者(瞬态的或请求范围的)，使用`resolve()`方法并将提供者的注入令牌作为参数提供给方法。

`resolve()`方法从其自身的注入容器树返回一个提供者的唯一实例。每个子树都有一个独一无二的上下文引用。因此如果你调用该方法一次以上并进行引用比较的话，结果是不同的。

> cats.service.ts

```typescript
@Injectable()
export class CatsService implements OnModuleInit {
  constructor(private moduleRef: ModuleRef) {}

  async onModuleInit() {
    const contextId = ContextIdFactory.create();
    const transientServices = await Promise.all([
      this.moduleRef.resolve(TransientService, contextId),
      this.moduleRef.resolve(TransientService, contextId),
    ]);
    console.log(transientServices[0] === transientServices[1]); // true
  }
}
```

### [动态实例化自定义类](https://docs.nestjs.cn/8/fundamentals?id=动态实例化自定义类)

要动态实例化一个之前未注册的类作为提供者，使用模块引用的`create()`方法。

> cats.service.ts

```typescript
@Injectable()
export class CatsService implements OnModuleInit {
  private catsFactory: CatsFactory;
  constructor(private moduleRef: ModuleRef) {}

  async onModuleInit() {
    this.catsFactory = await this.moduleRef.create(CatsFactory);
  }
}Copy to clipboardErrorCopied
```

该技术允许你在框架容器之外偶然实例化一个不同的类。

### 怎么样处理单例

* 怎么样每次生产的单例都是同一个呢？

要在不同的`resolve()`调用之间产生一个单例，并保证他们共享同样生成的DI容器子树，向`resolve()`方法传递一个上下文引用，使用`ContextIdFactory`类来生成上下文引用。该类提供了一个`create()`方法，返回一个合适的独一无二的引用。



* todo 后续，看使用情况



## ==懒加载模块==

* todo

## 应用上下文

> Nest提供了一些应用类来简化在不同应用上下文之间编写应用(例如Nest HTTP应用，微服务和WebSockets应用)。这些应用可以用于创建通用的守卫，过滤器和拦截器，可以工作在控制器，方法和应用上下文中。

* 不同类型的服务怎么实现通用的守卫、过滤器、拦截器
* 本章包括`ArgumentsHost`和`ExecutionContext`两个类.

### ArgumentsHost类

`ArgumentsHost`类提供了获取传递给处理程序的参数。

> `ArgumentsHost`简单地抽象为处理程序参数。例如，在HTTP应用中(使用`@nestjs/platform-express`时),host对象封装了Express的`[request, response, next] `数组,`reuest`是一个`request`对象，`response`是一个`response`对象，`next`是控制应用的请求响应循环的函数。此外，在GraphQL应用中，host包含`[root, args, context, info]`数组。

* ArgumentsHost 类一般提供在一些拦截作用的类中，一般的 provider 和 controller 都用不到（因为可以很方便的使用装饰器获取）

  目前已使用的有：守卫，过滤器和拦截器时

* 使用方式：

  1. 切换一种应用类型：http 、rpc、socket

  2. 获取请求对象

     比如http 是获取 request、response、next

### 获取当前应用上下文

* 不同的应用类型上下文 type 不一样

```typescript
if (host.getType() === 'http') {
  // do something that is only important in the context of regular HTTP requests (REST)
} else if (host.getType() === 'rpc') {
  // do something that is only important in the context of Microservice requests
} else if (host.getType<GqlContextType>() === 'graphql') {
  // do something that is only important in the context of GraphQL requests
}
```

* 确定类型后就能写通用组件了

### host 处理程序参数

* 要获取传递给处理程序的参数数组，可以使用 getArgs() 方法

  如下是 http 的请求参数

```typescript
if (type === 'http') {
  // http类型
  const [req, res, next] = host.getArgs();
}
```

* 还可以根据索引获取指定的参数

```typescript
// http 请求
const request = host.getArgByIndex(0);
const response = host.getArgByIndex(1);
```

* 官方并并不推荐使用 索引 的方式获取请求响应对象，==因为它将应用和特定的上下文耦合！==为了使代码的 鲁棒性 更好，更可复用，需要使用 host 对象的引用方法来切换合适的应用上下文！

```typescript
/**
 * Switch context to RPC.
 */
switchToRpc(): RpcArgumentsHost;
/**
 * Switch context to HTTP.
 */
switchToHttp(): HttpArgumentsHost;
/**
 * Switch context to WebSockets.
 */
switchToWs(): WsArgumentsHost;
```

#### 三个切换方法

>  `host.switchToHttp()`帮助方法调用一个HTTP应用的`HttpArgumentsHost`对象. `HttpArgumentsHost`对象有两个有用的方法，我们可以用来提取期望的对象。

* httpArgumentsHost

```typescript
const ctx = host.switchToHttp();
const request = ctx.getRequest<Request>();
const response = ctx.getResponse<Response>();
```

* WsArgumentsHost

  ```typescript
  export interface WsArgumentsHost {
    /**
     * Returns the data object.
     */
    getData<T>(): T;
    /**
     * Returns the client object.
     */
    getClient<T>(): T;
  }
  ```

* RpcArgumentsHost

```typescript
export interface RpcArgumentsHost {
  /**
   * Returns the data object.
   */
  getData<T>(): T;

  /**
   * Returns the context object.
   */
  getContext<T>(): T;
}
```

### 执行上下文类

ExecutionContext 扩展了 ArgumentsHost, ==提供了额外的当前运行线程信息==

* nest 在需要的时候提供了一个`ExecutionContext`的实例

```typescript
export interface ExecutionContext extends ArgumentsHost {
  /**
   * Returns the type of the controller class which the current handler belongs to.
   */
  getClass<T>(): Type<T>;
  /**
   * Returns a reference to the handler (method) that will be invoked next in the
   * request pipeline.
   */
  getHandler(): Function;
}
```

> `getHandler()`方法返回要调用的处理程序的引用。`getClass()`方法返回一个特定处理程序所属的控制器类。

* 拦截器和守卫都可能需要获取当前运行线程的信息，比如当前要处理的方法和所属的类

```typescript
// 测试执行上下文
context.getHandler().name; // 获取方法名
context.getClass().name; // 获取类名
```

> 能同时获取当前类和处理方法的引用的能力提供了极大的灵活性。最重要的是，它给我们提供了通过`@SetMetadata()`装饰器来操作守卫或拦截器元数据的方法。

* 参考下一节

### 反射和元数据

nest 提供了通过 @SetMetadata() 装饰器将自定义元数据附加在路径处理程序上的能力。我们可以在类中获取这些元数据来执行特定的决策。

* 使用

```typescript
@Post()
@SetMetadata('roles', ['admin'])
async create(@Body() createCatDto: CreateCatDto) {
  this.catsService.create(createCatDto);
}
```

* 我们将 roles 元数据关联到了 create() 方法，但是不推荐直接使用 @SetMetadata() 装饰器，而是应给封装成自定义的装饰器，只暴露一个值即可

  如下：

```typescript
import { SetMetadata } from '@nestjs/common';

export const Roles = (...roles: string[]) => SetMetadata('roles', roles);
```

* 这一方法更清晰，更刻度，并且是强类型的。这样就可以使用 ts 进行约束了！

==访问元数据==

要访问元数据需要使用官方提供的 Relector 辅助类，由nest提供，通过常规方式注入到类

```typescript
@Injectable()
export class RolesGuard {
  constructor(private reflector: Reflector) {}
}
```

* 使用 get() 方法获取处理程序的元数据

  get(key, function)

```typescript
const roles = this.reflector.get<string[]>('roles', context.getHandler());
```

> `Reflector#get`方法允许通过传递两个参数简单获取元数据：一个元数据key和一个context(装饰器对象)来获取元数据。在本例中，指定的key是`roles`(向上指回`roles.decorator.ts`以及在此处调用的`SetMetadata()`方法)。context 由`context.getHandler()`提供,用于从当前路径处理程序中获取元数据，`getHandler()`给了我们一个到路径处理函数的引用。

### 额外的两个快捷方法

```typescript
const roles = this.reflector.getAllAndOverride<string[]>('roles', [
  context.getHandler(),
  context.getClass(),
]);

const roles = this.reflector.getAllAndMerge<string[]>('roles', [
  context.getHandler(),
  context.getClass(),
]);
```



### 原理是什么，怎么获取的额

* todo



## 生命周期事件

* 所有使用nest创建的应用程序，都会有一个由nest管理的生命周期。nest提供了生命周期钩子，提供了关键生命时刻的可见性，以及在关键时刻发生时采取行动(在你的`module`，`injectable`或者`controller`中注册代码)的能力。

### 声明周期序列

分为三个阶段：

* 初始化
* 运行
* 终止

如下图，标黑色的就是事件

![生命周期钩子](https://docs.nestjs.com/assets/lifecycle-events.png)



### 生命周期事件

> 生命周期事件在应用初始化与终止时发生。Nest在`modules`，`injectables`和`controllers`的以下每个生命周期事件(首先要使能shutdown钩子，如下描述)中调用注册钩子方法。和上图所示的一样，Nest也调用合适的底层方法来监听连接，以及终止监听连接。

* modules、injectable、controller 都能使用

> 在下述表格中，`onModuleDestroy`, `beforeApplicationShutdown`和 `onApplicationShutdown`仅仅在显式调用`app.close()`或者应用收到特定系统信号(例如 SIGTERM)并且在初始化时(参见下表的应用`shutdown`部分)正确调用了`enableShutdownHooks`方法后被触发。

* 终止钩子调用的原因



| 生命周期钩子方法              | 生命周期时间触发钩子方法调用                                 |
| :---------------------------- | :----------------------------------------------------------- |
| `OnModuleInit()`              | 初始化主模块依赖处理后调用一次                               |
| `OnApplicationBootstrap()`    | 在应用程序完全启动并监听连接后调用一次                       |
| `OnModuleDestroy()`           | 收到终止信号(例如SIGTERM)后调用                              |
| `beforeApplicationShutdown()` | 在`onModuleDestroy()`完成(Promise被resolved或者rejected)；一旦完成，将关闭所有连接(调用app.close() 方法). |
| `OnApplicationShutdown()`     | 连接关闭处理时调用(app.close())                              |

* 注意：请求范围类不能绑定上述生命周期。他们为每个请求单独创建，并在响应发送后通过垃圾清理系统自动清理。

### 使用

* 需要实现相应的接口

>所有应用周期的钩子都有接口表示，接口在技术上是可选的，因为它们在 `TypeScript` 编译之后就不存在了。尽管如此，为了从强类型和编辑器工具中获益，使用它们是一个很好的实践。要使用合适的接口。例如，要注册一个方法在特定类(例如，控制器，提供者或者模块)初始化时调用，使用`OnModuleInit`接口，提供`onModuleInit()`方法，如下

```typescript
import { Injectable, OnModuleInit } from '@nestjs/common';

@Injectable()
export class UsersService implements OnModuleInit {
  onModuleInit() {
    console.log(`The module has been initialized.`);
  }
}
```

### 异步初始化

OnMoudleInit 和 OnApplicationBootstrap 钩子允许你延迟应用程序初始化过程（返回一个`Promise`或在方法主体中将方法标记为`async`和`await`异步方法）

```typescript
async onModuleInit(): Promise<void> {
  await this.fetch();
}
```

## 测试

自动化测试是成熟软件产品的重要组成部分！

# 安全

## 认证 authentication

身份验证是大多数现用应用程序的重要组成部分。有许多不同的方法、策略和方法来处理用户授权。任何项目采用的策略取决于其应用程序的要求。

### 认证工具

`passport` 是目前最流行的 `node.js` 认证库，为社区所熟知，并成功地应用于许多生产应用程序中。将这个库与使用 `@nestjs/passport` 模块的 `Nest` 应用程序集成起来非常简单。在较高级别，`Passport` 执行一系列步骤以：

- 通过验证用户的”证”(例如用户名/密码、`JSON Web`令牌( `JWT` )或身份提供者的身份令牌)来验证用户的身份。
- 管理经过身份验证的状态(通过发出可移植的令牌，例如 `JWT`，或创建一个 `Express` 会话)
- 将有关经过身份验证的用户的信息附加到请求对象，以便在路由处理程序中进一步使用

passport 提供了非常多的策略，不同的策略使用不同的包，我们只需要使用其中一个包

### 身份认证

需求：客户端首先使用用户名和密码进行身份验证；一旦通过身份盐城，服务器将发出 jwt，该 jwt 可以在后续的请求的授权头中作为 token 发送。创建一个受保护的路由，该路由仅对包含有效 jwt 请求可访问

安装软件包：

使用策略 Passport-local，它实现了一种 用户名/密码 身份验证机制

```shell
npm i @nestjs/passport passport passport-local
npm i @types/passport-local
```

* 选择任何 passport 策略都需要安装 @nestjs/passport 和 passport 包。
* 具体的策略还要安装策略包，比如：passport-jwt` 或 `passport-local。它们实现正在构建的身份验证策略
* 另外安装ts定义，比如 @types/passport-local

### passport

官方：http://www.passportjs.org/tutorials/password/

https://juejin.cn/post/6844904110588854285

https://www.w3cschool.cn/passport_js_note/qejxyozt.html

passport 是一个nodejs中的一个做登录验证的中间件，及其灵活、模块化，并且可与express无缝集成（说白了就是实现了express中间件）。

passport功能单一，即只能做登录验证，确非常强大，支持本地账号验证和第三方账号登录验证（OAuth和OpenID等）

* 优势就是省略了一些 username 和 password 的处理，省略了一些set cookie的操作

#### 策略

* passport 的唯一目的就是验证请求，通过一组称为 策略（strategy）的可扩展插件实现。只做认证不做任何路由或者数据库处理，不限制使用的框架

#### session

* 用来维持用户的身份验证状态（保持会话）

安装：

```shell
$ npm install express-session
```

#### 步骤

1. 配置 passport 策略
2. 在应用程序中使用（相当于一个全局对象）

#### 示例

* 查看some-practice



### passport 策略

将 passport 本身看做一个框架是有帮助的，它的优雅之初在于将身份验证过程抽象为几个基本步骤，可以根据实现的策略对这些步骤进行自定义。它类似于一个框架，因为您可以通过提供定制参数(作为 `JSON` 对象)和回调函数( `Passport` 在适当的时候调用这些回调函数)的形式来配置它

`@nestjs/passport` 模块将该框架包装在一个 `Nest` 风格的包中，使其易于集成到 `Nest` 应用程序中。

使用 @nestjs/passport，可以通过扩展 PassportStrategy 类来配置 passport 策略。通过调用子类中的 `super()` 方法传递策略选项(上面第1项)，可以选择传递一个 `options` 对象。通过在子类中实现 `validate()` 方法，可以提供`verify` 回调(上面第2项)。

### 前端密码加密

https://juejin.cn/post/7011306453373812744

* 一般使用加密单向哈希算法的 bcrypt 之类的库

### 内置 passport 守卫

守卫的章节确定了我们应该使用守卫确定是否由路由处理该请求

在使用 @nestjs/passport 模块的情况下，我们将讨论一个问题：从身份验证的角度来看，应用程序可以以两种状态存在

1， 用户/客户端未登录（未通过身份验证）

2. 用户/客户端已登录（已通过身份验证）

### session

* todo express-session

### 认证步骤总结

1. 编写 passport 认证策略
2. 在路由归属的 module 中引入该策略归属的module（这一项非常重要）

## JWT 功能

官方例子：https://github.com/nestjs/nest/tree/master/sample/19-auth-jwt

### 分析需求

- 允许用户使用用户名/密码进行身份验证，返回 `JWT` 以便在后续调用受保护的 `API` 端点时使用。我们正在努力满足这一要求。为了完成它，我们需要编写发出 `JWT` 的代码。
- 创建基于`token` 的有效`JWT` 的存在而受保护的API路由。

### 受保护的 api

* 注意 jwt 的应用范围
* 注意和公开的api区别

### 安装

```bash
$ npm install @nestjs/jwt passport-jwt
$ npm install @types/passport-jwt --save-dev
```

### 需要做

* 只有用户验证通过之后，才能调用路由处理程序
* req参数将包含一个 username 属性（passport-local 处理）

方案：

> 考虑到这一点，我们现在终于可以生成一个真正的 `JWT` ，并以这种方式返回它。为了使我们的服务保持干净的模块化，我们将在 `authService` 中生成 `JWT` 。在auth文件夹中添加 `auth.service.ts` 文件，并添加 `login()` 方法，导入`JwtService` 

### jwt 实现——怎么返回token

* 参考 nest auth 代码

### jwt 解析

* 主要是 `jwt.strategy.ts` 的实现，解析请求中的token

> `validate()` 方法值得讨论一下。对于 `JWT` 策略，`Passport` 首先验证 `JWT` 的签名并解码 `JSON `。然后调用我们的 `validate()` 方法，该方法将解码后的 `JSON` 作为其单个参数传递。根据 `JWT` 签名的工作方式，我们可以保证接收到之前已签名并发给有效用户的有效 `token` 令牌。

* 主要是 validate 方法的实现，在提取完成之后，怎么进行验证。

### 默认策略

* 如果使用内置守卫需要配置策略字符串，可以注册默认的策略，不传字符串就使用默认策略。jwt是使用最多的策略。

```typescript
@Module({
  imports: [
    UserModule,
    // jwt 相关
    PassportModule.register({
      // session: true,
      defaultStrategy: 'jwt', // 默认jwt策略，剩下在内置守卫中传入字符串
    }),
    // jwt 使用参考：https://github.com/nestjs/jwt/blob/master/README.md
    JwtModule.register({
      secret: JWTConstants.secret,
      signOptions: {
        expiresIn: '60s',
        // noTimestamp: true, // 是否拼接时间戳
      }, // 配置参考：https://github.com/auth0/node-jsonwebtoken#usage
    }),
  ],
  providers: [AuthService, LocalStrategy, JwtStrategy],
  exports: [AuthService],
})
export class AuthModule {}
```

### 与session关系

* session 是服务端的状态；jwt的功能反而不需要session来配合，用户信息被保存到了客户端。

### 请求范围策略

* 在 graphql 应用中可能会使用到请求范围的provider。
* 因此可能需要结合 ModuleRef 获取service

https://docs.nestjs.cn/8/security?id=%e8%af%b7%e6%b1%82%e8%8c%83%e5%9b%b4%e7%ad%96%e7%95%a5

## 权限（authorization）

* 权限是指确定一个用户可以做什么的过程。

> 权限是指确定一个用户可以做什么的过程。例如，管理员用户可以创建、编辑和删除文章，非管理员用户只能授权阅读文章。

权限和认证是相互独立的。但是权限需要依赖认证机制。

### 基础的 RBAC 实现

> 基于角色的访问控制（**RBAC**)是一个基于角色和权限等级的中立的访问控制策略。本节通过使用`Nest`[守卫](https://docs.nestjs.cn/8/guards)来实现一个非常基础的`RBAC`。

#### RBAC

*role-based access control* 基于角色的权限控制

### 角色装饰器

```typescript
import { SetMetadata } from '@nestjs/common';
import { Role } from './role.enum';

export const ROLES_KEY = 'roles';
// 自定义的角色装饰器。注意：角色可能是多个角色都能访问这个路径。
export const Roles = (...roles: Role[]) => SetMetadata(ROLES_KEY, roles);
```

### 角色守卫

> 该例子被称为“基础的”是因为我们仅仅在路径处理层面检查了用户权限。在实际项目中，你可能有包含不同操作的终端/处理程序，它们各自需要不同的权限组合。在这种情况下，你可能要在你的业务逻辑中提供一个机制来检查角色，这在一定程度上会变得难以维护，因为缺乏一个集中的地方来关联不同的操作与权限。

### 基于权利（claims）的权限

一个身份被创建后，可能关联来来自信任方的一个或者多个权利。权利是指一个表示对象可以做什么，而不是对象是什么的键值对。

要在Nest中实现基于权利的权限，你可以参考我们在`RBAC`部分的步骤，仅仅有一个显著区别：比较`许可(permissions)`而不是角色。每个用户应该被授予了一组许可，相似地，每个资源/终端都应该定义其需要的许可（例如通过专属的`@RequirePermissions()`装饰器）。



## 加密和散列

### 哈希

`哈希`是一个将给定值转换成另一个值的过程。哈希函数使用数学算法来创建一个新值。一旦哈希完成，是无法从输出值计算回输入值的。

* 将一个固定值转换成另一个值的过程，哈希函数使用数学算法来创建一个新的值，一旦哈希完成，是无法从输出值计算回输入值的。（单向的）



## 用 session 的方式做登录认证

* 参考：https://dev.to/nestjs/authentication-and-sessions-for-mvc-apps-with-nestjs-55a4

  需要结合 express-session

## OAuth2 & openid

https://juejin.cn/post/7146182173031137311

https://juejin.cn/post/6844903634094784520#heading-9

* Todo



# nest 依赖注入原理

## 元数据

参考 typescript 文档

* typescript（启动 emitDecoratorMetadata选项），会为注入如下三种元数据

  ```typescript
  console.log(Reflect.getMetadata('design:type', target, propertyKey), '---setFn');
  console.log(Reflect.getMetadata('design:paramtypes', target, propertyKey), '---setFn1');
  console.log(Reflect.getMetadata('design:returntype', target, propertyKey), '---setFn2');
  ```

  通过获取类型来实现依赖注入

* 元数据 实现



# https

* todo



# 总结

## 1. dto

* 因为nest大量使用 metadata 元数据，如果使用 interface 去定义接口类型，会在编译阶段进行去除，引入nest推荐使用 class 去定义类型，这样就不会再编译阶段去除，能够完美支持 metadata 

## 2. nest 是怎么实现返回同步和异步的都可以呢

```typescript
const fn = async() => {
    const aa = await true
    const bb = await Promise.resolve(true)
    console.log(aa)
}
// 用 await 实现是一样的
```



## 3. 怎么样把非ts文件加入到编译产出中

* 如下，比如加入 .env 文件

```typescript
{
  "$schema": "https://json.schemastore.org/nest-cli",
  "collection": "@nestjs/schematics",
  "sourceRoot": "src",
  "compilerOptions": {
    "assets": [
      "**/*.env"
    ]
  }
}
```





# what

## 1. 名词

token 是啥？

> 其中令牌用于请求同名类的实例。

### 组件

* 在 nest 中 provider 获取 service 也叫 组件

## 2. 混合应用

* 即处理接口，又充当网管、微服务？

## 3. 看来 rxjs 也使用了 tapable

* 如上，在rxjs 的依赖中能够查看

## 4. IOC 容器

* 在 nest 中 IOC容器指的是 providers 容器



# 参考列表：

1. [nestjs中文文档](https://docs.nestjs.cn/9/controllers)



# 思考

## 为什么Java需要Rx**

* 并发模型是多线程，会出现加锁的需求。js天生异步，支持 async await，非常方便操作。

## 为啥要分层

* 分层的思维本来就容易契合solid设计原则。人类的本质也是这样，干什么事都是分段进行，就想流水线一样，每一层（或者一个工位）负责一段事情。这样才有可能写出高内聚低耦合、符合设计原则，可扩展、维护性高的代码。

## 帮助类

* 从linux的helper命令出发，一切工具查询的类都命名为 helper 类