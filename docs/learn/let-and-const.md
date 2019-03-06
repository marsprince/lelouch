## 论var let const

先参考[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/let)

let 与 var 最主要的区别是：

1.var声明的变量的作用域是整个封闭函数，即var没有块级作用域的概念，最常见的用法，以let代替循环内的闭包。

```javascript
var list = document.getElementById("list");

for (let i = 1; i <= 5; i++) {
  var item = document.createElement("LI");
  item.appendChild(document.createTextNode("Item " + i));

  let j = i;
  item.onclick = function (ev) {
    console.log("Item " + j + " is clicked.");
  };
  list.appendChild(item);
}
```

let实际上是在内部声明了一个新的变量，参考 http://www.ecma-international.org/ecma-262/6.0/#sec-for-statement-runtime-semantics-labelledevaluation

2.在程序或者函数的顶层，let并不会像一样在全局对象上创造一个属性

```javascript
var x = 'global';
let y = 'global';
console.log(this.x); // "global"
console.log(this.y); // undefined
```

3.let变量的解析也会提前，但是不会像var一样，初始化为Undefined

```javascript
let x = 'global'
{
  console.log(x) // Uncaught ReferenceError: x is not defined
  let x = 1
}
```

这是由js运行机制决定的，let x = 1，js会先解析左边的部分，发现当前作用域里没有x，则添加一个变量x。不同的是，这个变量是不可访问的（或者说没有被赋值undefined），参考你不知道的js：

如果 RHS 查询在嵌套的*作用域*的任何地方都找不到一个值，这会导致*引擎*抛出一个`ReferenceError`。必须要注意的是这个错误的类型是`ReferenceError`。

因此在试图console.log(x)的时候，实际上是一个RHS 查询，它在当前作用域了找到了x变量，因此不会继续向上找到global。在试图访问的时候失败了，因此抛出了`ReferenceError`。

还有一个问题可以反映这个结论：

```javascript
let x = m;
```

很明显，上面这句会报错，m不存在，OK。当我再次试图

```javascript
let x = 1
```

的时候失败了，原因是x已经被声明了，我试图

```javascript
x = 1
```

的时候也失败了，因为x不能访问。这个时候的x就处于一个被声明但找不到的状态。除非我们

```javascript
window.x = 1
```

就会成功，因为这相当于绕过了声明的语法重新搞了一个变量出来，是可以的。

`const`和`let`完全一样，区别在于const声明一个引用不可变的对象（内容可修改，即可修改const的对象和数组，但不可重写）。
