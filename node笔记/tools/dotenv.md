* 一个一个传入node环境变量很繁琐，可以定义一个 .env 文件，使用 dotenv 去处理，这样用起来更优雅
* 参考：https://juejin.cn/post/6993224664705138702

```typescript
import dotenv from 'dotenv'
import path from 'path'
import fs from 'fs'

dotenv.config({
    path: path.resolve('./config.env'),
})

const config = dotenv.parse(fs.readFileSync('./config.env'))

console.log(process.env.NAME, config)

```

* 常用的就是 config 和 parse 方法