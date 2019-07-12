# 事件详解

## 事件的传递

事件的传递分为三个阶段：

捕获 => 目标 => 冒泡



Q1：所有的事件都有这三个阶段吗？

不是，参考[MDN事件说明](https://developer.mozilla.org/zh-CN/docs/Web/Events)。例如focus，blur就没有冒泡，推荐使用focusin，focusout代替！



Q2：都是先捕获再冒泡吗？多个事件执行顺序如何？

先捕获再冒泡，注意区别在target上没有捕获和冒泡区别，按照添加顺序执行，先添加先触发。



Q3：e.target 和 e.currentTarget 有何区别？

e.target 是触发这个事件的源

e.currentTarget 是当前的元素，等于this



Q4：如何手动创建一个事件？

```javascript
var ev = new Event("look", {"bubbles":true, "cancelable":false});
document.dispatchEvent(ev);
```

以上例子参见

[https://codesandbox.io/s/strange-dewdney-1qmr4wvn2l](https://codesandbox.io/s/strange-dewdney-1qmr4wvn2l)


