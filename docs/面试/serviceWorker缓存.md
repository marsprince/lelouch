## service-worker

Web worker的一种

### ServiceWorkerGlobalScope下的事件

install: 如果浏览器之前没有安装过，或者强制update，或者service-worker文件进行了更改，就会触发这一流程

activate: 激活，当您更新 Service Worker 文件 ( /sw.js) 时，浏览器会在开发者工具中显示文件变化，新的 Service Worker 是 '等待激活' 状态。等你关闭标签再打开就激活了。

fetch: 每次任何被 service worker 控制的资源被请求到时，都会触发 fetch 事件，这些资源包括了指定的 scope 内的文档，和这些文档内引用的其他任何资源

sync: 后台同步

push: 推送事件，就是浏览器展示接受通知的那个事件

message: 传输消息

### API

ServiceWorkerContainer：navigator.serviceWorker

ServiceWorkerRegistration: navigator.serviceWorker.register的回调

ServiceWorkerGlobalScope: service-worker的全局对象，继承自WorkerGlobalScope


