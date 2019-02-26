## XMLHttpRequest

### 构造方法（参数已废弃）

### 参数

**readyState**：

返回一个当前状态，可以在prototype上找到对应的常量



UNSENT

XMLHttpRequest 代理已被创建， 但尚未调用 open() 方法。

OPENED

open() 方法已经被触发。在这个状态中，可以通过 [setRequestHeader()](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/setRequestHeader)方法来设置请求的头部， 可以调用[send()](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/send)方法来发起请求。

HEADERS_RECEIVED

send() 方法已经被调用，响应头也已经被接收。

LOADING

响应体部分正在被接收。如果[responseType](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/responseType)属性是“text”或空字符串，[responseText](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/responseText)将会在载入的过程中拥有部分响应数据。

DONE

请求操作已经完成。这意味着数据传输已经彻底完成或失败。

**response**：返回的正文，默认是text，除此之外还有arraybuffer（二进制），blob（文件流），document（html文档），json

**status**：状态码

**timeout**：超时时间，可以在ontimeout里捕获

**upload**：返回[XMLHttpRequestUpload](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequestUpload "此页面仍未被本地化, 期待您的翻译!")，文件上传用，会单独介绍

withCredentials：跨域请求时是否允许携带cookie等信息，后端必须设置Access-Control-Allow-Credentials:true，代表允许前端携带信息



onreadystatechange：state的回调

responseText：response的text

responseType:：resposne类型的枚举值

responseUrl：返回的url

responseXML：返回的XML

statusText：返回的状态+文本



## 方法

**open**：初始化请求

```javascript
open(method: string, url: string): void;
open(method: string, url: string, async: boolean, username?: string | null, password?: string | null): void;
```

其中async参数可以设置是否异步，默认为异步，true，设置为false是同步。

**send**：发送请求，如果是GET，传null

```javascript
send(body?: Document | BodyInit): void;
```

getResponseHeader：获得指定返回头

```javascript
getResponseHeader(name: string): string | null;
```

getAllResponseHeaders：获得所有返回头

注：不是所有头都可以拿到，浏览器只会返回安全头，参考https://stackoverflow.com/questions/7462968/restrictions-of-xmlhttprequests-getresponseheader

setResponseHeader： 

```javascript
setRequestHeader(name: string, value: string): void;
```

overrideMimeType：强制指定服务端返回的MIME类型，可将文本或图片解析为二进制，现在用responseType即可，没必要用它了

abort：终止请求



## 事件

https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequestEventTarget

继承自MLHttpRequestEventTarget，参考上述网址

注：onloadstart,ontimeout等均是从send调用后开始算



参考文献：

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/readyState）)

[W3C](https://www.w3.org/TR/XMLHttpRequest/)

https://segmentfault.com/a/1190000004322487#articleHeader13




