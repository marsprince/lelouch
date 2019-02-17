## Attribute 和 Properties 的区别

今天看vue，讲到了一个修饰符，[.prop](https://cn.vuejs.org/v2/api/index.html)，可以将东东绑定到property上。

默认情况下，v-bind是绑定到attribute上的，那么，什么是attribute和property呢？

vue官网指向了一篇stackoverflow上的高赞[回答](https://stackoverflow.com/questions/6003819/what-is-the-difference-between-properties-and-attributes-in-html#answer-6004028)。其实很简单，attr是属于html的，而prop是属于js的。在w3的文档里，前者被叫做HTML attributes或者[content attributes](https://www.w3.org/TR/html5/dom.html#content-attributes)，后者被叫做IDL attributes。

举例：

```html
<input id="test" type="testType" value="name">
```

这段html就存在两个attr，type 和 value。当浏览器去解析dom的时候，就会将这段html渲染为一个[HTMLInputElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement)。根据标签的不同，渲染出的对象类型也不同，以后我会单独写这一块。这个对象上会包含若干属性，这就是prop。而html上的attr会被存在一个叫attributes的对象里。

那么这两个有什么联系呢，浏览器会根据你的attr去生成对象的一些prop。其中又分N多种情况：

1. 直接映射，如上面的id

2. 条件映射，如上面的type，大家都知道，input只有几种type，如果你写的不是合法值的话，浏览器会写一个默认值。

   ![5c6917009b169](https://i.loli.net/2019/02/17/5c6917009b169.png)

3. 我也不清楚该怎么讲，比如上面的value，只是一个input的默认值。接下来就和attr无关了。

4. 有一些attr，占了js的关键字，比如class，会被映射为className。

5. 还有一些attr，比如style，会从字符串映射为一个对象

以下省略若干

。

。

。

自己去w3[文档](https://www.w3.org/TR/html5/infrastructure.html#common-dom-interfaces)上翻吧，反正我

![5c691e8dd7a8b](https://i.loli.net/2019/02/17/5c691e8dd7a8b.jpg)
