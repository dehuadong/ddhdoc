## 全局安装electron:

```
npm install electron -g
```

当前项目安装 自动更新功能的实现依赖 electron-builder 和 electron-updater

```
npm install  electron-builder --save-dev
yarn add electron-builder --dev

npm install electron-updater --save
yarn add electron-updater
```

## 主进程

```js
// main.js
const { app, BrowserWindow } = require('electron')

app.on('ready', () => {
  let win = new BrowserWindow({
    width: 500,
    height: 300
  })
  win.loadFile('index.html')
})
```

整个 *main.js* 这个脚本是主进程，而主进程拥有 new 一个 window 的能力。

*BrowserWindow *类用来创建一个页面，也就是一个渲染进程，但是它只能在主进程中使用。

所有的页面（渲染进程）都应该由主进程来统一管理调度。

## 渲染器进程

> 每个页面都是一个渲染器进程，所以上面的 BrowserWindow 创建的页面就是跑在渲染器进程上的。

> 在 Electron 中，主进程只有一个，渲染器进程存在多个，所以大致可以把进程之间的通讯划分为以下 3 类：

1. 渲染器进程调用主进程
2. 主进程调用渲染器进程
3. 渲染器进程之间的交互

## 渲染器进程调用主进程

> ipcMain 从主进程到渲染进程的异步通信。当在主进程中使用时，它处理从渲染器进程（网页）发送出来的异步和同步信息。 从渲染器进程发送的消息将被发送到该模块。

> ipcRenderer 从渲染器进程到主进程的异步通信。 可以使用它提供的一些方法从渲染进程 (web 页面) 发送同步或异步的消息到主进程。 也可以接收主进程回复的消息。

**ipcRenderer.invoke(channel, ...args)方法**

> 需要在渲染器进程中来发起通讯，并返回使用来自主进程的Promise异步响应进行解析。

```js
ipcRenderer.invoke('channel', ...args).then((result) => {
	console.log('invoke result-->',result)
}).catch(error=>{
	console.log('error-->',error)
})
```

> 并且在主进程中来监听渲染器进程发起的通讯,并且返回

```js
ipcMain.handle('channel', (event, ...args) => {
	console.log('args--->',args)
  return args[0]
})
```

## 主进程调用渲染器进程

> webContents 在主进程负责渲染和控制网页, 是 BrowserWindow 对象的一个属性。

在 Electron 中，主进程调用渲染器进程 需要首先在主进程中找到渲染器进程的实例对象（比如 win1），然后通过webContents.send方法向渲染进程发送异步消息

```js
win1.webContents.send('channel', ...args)
```

对应的还需要在渲染器进程中通过ipcRenderer.on方法来监听。

```js
ipcRenderer.on('channel', (event, message) => {
   console.log(message)  
})
```

## 渲染器进程之间的交互

参考：[Electron：主进程与渲染器进程](https://segmentfault.com/a/1190000021843332)

## vue插件

[Vue CLI Plugin Electron Builder ](https://nklayman.github.io/vue-cli-plugin-electron-builder/)
