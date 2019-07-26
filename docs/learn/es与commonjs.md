参考了 https://juejin.im/post/5cf8e159f265da1b94213a63

添加了自己的理解

## commonjs 和 esm 的主要区别可以概括成以下几点

1.输出拷贝 vs 输出引用

2.esm 的 import read-only 特性

3.esm 存在 export/import 提升

其中：

1.commonjs是输出拷贝，es是输出引用，很好理解

2.import是只读的，也很好理解，参考const，如果修改，会报出如下错误：

Uncaught TypeError: Assignment to constant variable.

3.变量提升，什么意思，也可以类比const，把整个文件当做一个大的变量声明，在这个初始化没完成的时候，
如果你试图读取里面的值，会报和暂时性死区一样的错误：

Uncaught ReferenceError: Cannot access 'xxx' before initialization

典型代表是循环引用，参见： [![Edit static](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/static-emtj2?fontsize=14)

## babel转译

但是之上的问题3不会在babel转译中出现，因为babel没法模拟暂时性死区，例如下面的代码，在浏览器里报TDZ错误，但是babel转译可以顺利运行

```javascript
console.log(a);
const a = 1
```

问题2babel报的错和浏览器报的错也不一样，报一个只读错误，不过都会报错是真的

babel转码的本质也是利用commonjs模拟esm，如下代码，会被转移成

```javascript
import {b} from './b'
export let a = 1;
setTimeout(()=>{
  a = 2
},100)
```

```javascript
"use strict";

Object.defineProperty(exports, "__esModule", {
  value: true
});
exports.a = void 0;

var _b = require("./b");

var a = 1;
exports.a = a;
setTimeout(function () {
  exports.a = a = 2;
}, 100);
```

可见,babel也实现了变量提升，不过设置为了undefined，babel没有实现输出引用，但是在修改的时候会同步修改两个变量，来模拟输出引用



