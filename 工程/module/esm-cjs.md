# esm 和 commonjs

## 纯esm项目

* 最近很多包都开始完全使用esm替代 commonjs，不再支持commonjs了。关于配置的更改参考：

  https://gist.github.com/sindresorhus/a39789f98801d908bbc7ff3ecc99d99c

## typescript 配置

主要是三步：

1. package.json 中添加 `type: "module"`
2. tsconfig `"module": "commonjs",` -> `"module": "ESNext", `
3. 不再使用 ts-node，使用 ts-node-esm