## 深入深出面试题之——a.x是什么?

相信大家都遇到过这样一道面试题

```javascript
let a = {
    x: function () {
        alert(this)
    }
}
let b = a
a.x = a = {
    y:function () {
         alert(this)
    }
}
// a = ?
// b = ?



// 如果你知道了上面的答案，那么下面应该也很容易答出了
// a.y()
// b.x.y()
```

答案

```javascript
a = {
   y: function() {
       alert(this)
   }
}
b = {
    x : {
        y : function () {
            alert(this)
        }
    }
}
```

这里涉及到几个概念：

1.运算符优先级，.结合是比=赋值优先级高的，这个百度一下就知道了，所以运算是先计算a.x，再赋值

2.=运算的顺序，从右至左

3.a.x 如何理解？

想要理解这个，首先介绍一个概念：[引用类型](https://tc39.github.io/ecma262/#sec-reference-specification-type)，这个引用类型不是我们常说的引用传递，引用类型，不是一个概念。

它是一种用来解释语言行为的内部类型。

以下摘抄自es官方文档：

---

NOTE

The Reference type is used to explain the behaviour of such operators as`delete`,`typeof`, the assignment operators, the`super`keyword and other language features. For example, the left-hand operand of an assignment is expected to produce a reference.

A Reference consists of three components, the base value component, the referenced name component, and the Boolean-valued strict reference flag

左表达式预期就会产生一个引用类型（左表达式解释起来比较复杂，下次有机会说）。

引用类型生成完毕后，就和原变量无关，可以理解为一个新的内部对象。它包含三部分：基础值（可以理解为类似为内存地址），引用名，是否严格引用。

因此，在执行赋值表达式之前，内存是这样的：

![5c74013b869b7](https://i.loli.net/2019/02/25/5c74013b869b7.png)



赋值自右向左执行：

先将a指向新变量001，再修改000的x，最后a返回新的变量，b指向修改后的变量

![5c74021fa75e9](https://i.loli.net/2019/02/25/5c74021fa75e9.png)



引用类型还有一个特性，就是在执行函数运算时，会把this指向地址内的对象，这也是为什么a.x里面的this指向a的原因。



进阶题：

```javascript
var a=1
function A(){
    a={i:0,c:2}
    return a
}

var b={c}=(A()).i=a.d=2

console.log(a)
console.log(b)
console.log(c)



//{ i: 2, c: 2, d: 2 }
//2
//undefined
```

本题来自于知乎网友：[链接](https://zhuanlan.zhihu.com/p/25428739)


