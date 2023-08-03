# 跨页面通信方案

## 1. 同源页面间的通信

### 1.1 broadcast channel

* 参考：https://developer.mozilla.org/zh-CN/docs/Web/API/BroadcastChannel

**`BroadcastChannel`** 接口代理了一个命名频道，可以让指定 [origin](https://developer.mozilla.org/zh-CN/docs/Glossary/Origin) 下的任意 [browsing context](https://developer.mozilla.org/zh-CN/docs/Glossary/Browsing_context) 来订阅它。它允许同源的不同浏览器窗口，Tab 页，frame 或者 iframe 下的不同文档之间相互通信。通过触发一个 [`message`](https://developer.mozilla.org/zh-CN/docs/Web/API/BroadcastChannel/message_event) 事件，消息可以广播到所有监听了该频道的 `BroadcastChannel` 对象。

* 同源的不同浏览器窗口，通过message通信
* 只需要指定相同的频道就可以

```typescript
const bc = new BroadcastChannel('AlienZHOU');

bc.onmessage = function (e) {
    const data = e.data;
    const text = '[receive] ' + data.msg + ' —— tab ' + data.from;
    console.log('[BroadcastChannel] receive message:', text);
};

bc.postMessage(mydata);
```

### 1.2 service worker

* service worker 可以拿到向其发送信息的客户端实例。那么就可以利用这个实例进行广播了

### 1.3 localstorage

* 在一个页面可以监听另一个同源页面设置 storage 的事件。必须是非同一个页面，同一个页面不能监听到该事件

```typescript
window.addEventListener('storage', function (e) {
    if (e.key === 'ctc-msg') {
        const data = JSON.parse(e.newValue);
        const text = '[receive] ' + data.msg + ' —— tab ' + data.from;
        console.log('[Storage I] receive message:', text);
    }
});
```

### 1. 4 shared worker



## 2. 非同源页面通信

* 一般建议使用 iframe 作为 bridge
  * iframe 间符合同源策略，可以使用localstorage或者broadcastChannel进行通信
  * 页面与 iframe 可以直接使用 postMessage 进行通信