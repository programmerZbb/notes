# schema

* 简单理解一种数据模型吧

# 简介

https://juejin.cn/post/7038763751293583367

# 应用

## typescript 应用

### 类型

* 参考 webpack schema-utils 的类型定义

```typescript
export type JSONSchema4 = import("json-schema").JSONSchema4;
export type JSONSchema6 = import("json-schema").JSONSchema6;
export type JSONSchema7 = import("json-schema").JSONSchema7;
export type ErrorObject = import("ajv").ErrorObject;
export type Extend = {
  formatMinimum?: number | undefined;
  formatMaximum?: number | undefined;
  formatExclusiveMinimum?: boolean | undefined;
  formatExclusiveMaximum?: boolean | undefined;
  link?: string | undefined;
};
export type Schema = (JSONSchema4 | JSONSchema6 | JSONSchema7) & Extend;
```

### 支持的数据结构

* 参考 jsonSchema 的官方定义

```typescript
export type JSONSchema7TypeName =
    | 'string' //
    | 'number'
    | 'integer'
    | 'boolean'
    | 'object'
    | 'array'
    | 'null';
```





## 工具

### validate - 根据schema验证一个js对象

* 推荐使用 webpack schema-utils 可以很方便的做一些函数输入验证（尤其对于后端接口）

```typescript
import { validate } from 'schema-utils'
import { Schema } from 'schema-utils/declarations/validate'
// export type JSONSchema7 = import("json-schema").JSONSchema7;

const schema: Schema = {
    type: 'object',
    properties: {
        test: {
            type: 'string',
        },
    },
}

// 验证json schema，json schema 类型参考。验证未通过，直接报错
validate(schema, {
    test: 111
}, {
    name: 'Example Loader',
    baseDataPath: 'options',
});
// 会直接抛出错误，test 必须是 string
```





## 低代码



