# 简介

* 中文官网：https://puppeteer.bootcss.com/
* 使用参考：https://juejin.cn/post/7230757380819812407

> Puppeteer 是一个 Node 库，它提供了一个高级 API 来通过 [DevTools](https://chromedevtools.github.io/devtools-protocol/) 协议控制 Chromium 或 Chrome。Puppeteer 默认以 [headless](https://developers.google.com/web/updates/2017/04/headless-chrome) 模式运行，但是可以通过修改配置文件运行“有头”模式。

## 能做什么

你可以在浏览器中手动执行的绝大多数操作都可以使用 Puppeteer 来完成！ 下面是一些示例：

- 生成页面 PDF。
- 抓取 SPA（单页应用）并生成预渲染内容（即“SSR”（服务器端渲染））。
- 自动提交表单，进行 UI 测试，键盘输入等。
- 创建一个时时更新的自动化测试环境。 使用最新的 JavaScript 和浏览器功能直接在最新版本的Chrome中执行测试。
- 捕获网站的 [timeline trace](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/reference)，用来帮助分析性能问题。
- 测试浏览器扩展。

# 起步

## 安装

* 只需要安装内核就行，使用现有的浏览器

```bash
$ npm i puppeteer-core
```

## 使用

```typescript
import { launch } from 'puppeteer-core'

const run = async () => {
  // 可执行路径参考：https://stackoverflow.com/questions/64078449/how-to-load-chrome-in-puppeteer-on-mac
  const browser = await launch({
    // 本地Chrome路径
    executablePath: '/Applications/Google Chrome.app/Contents/MacOS/Google Chrome',
    // 是否为无头
    headless: false,
  });
  const page = await browser.newPage();
  // 设置屏幕大小
  await page.setViewport({
    width: 2240,
    height: 1323,
  })
  // 屏幕截图
  // await page.goto('https://www.programmerzbb.icu');
  // await page.screenshot({path: 'example.png'});
  // pdf
  // await page.goto('https://www.programmerzbb.icu', {waitUntil: 'networkidle2'});
  // await page.pdf({path: 'hn.pdf', format: 'A4'});

  // 执行脚本
  await page.goto('https://www.programmerzbb.icu')
  const dimensions = await page.evaluate(() => {
    // 这一句会在浏览器中执行
    console.log(navigator.userAgent)
    const test1 = 'hh'
    return {
      width: document.documentElement.clientWidth,
      height: document.documentElement.clientHeight,
      deviceScaleFactor: window.devicePixelRatio,
      // ua: navigator.userAgent,
      test1: test1,
    };
  });

  console.log('Dimensions:', dimensions);

  await browser.close();
}

run()
```

## 用户配置

* puppeteer 会创建自己的 chromium 用户配置，它会在每次运行时清理