## 什么是继承

有一个问题，是每次面试必问的；

有一个问题，是红宝书里写的最多的；

那就是继承啦！

废话不多说，直接进入主题，js中的继承怎么实现呢？

js中所谓的继承，是通过原型链实现的，参照[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)的说法:

```markdown
JavaScript 对象是动态的属性“包”（指其自己的属性）。JavaScript 对象有一个指向一个原型对象的链。当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾。

遵循ECMAScript标准，someObject.[[Prototype]] 符号是用于指向 someObject的原型。从 ECMAScript6 开始，[[Prototype]] 可以通过Object.getPrototypeOf()和Object.setPrototypeOf()访问器来访问。这个等同于 JavaScript 的非标准但许多浏览器实现的属性 __proto__。

但它不应该与构造函数 func 的 prototype 属性相混淆。被构造函数创建的实例对象的 [[prototype]] 指向 func 的 prototype 属性。Object.prototype 属性表示Object的原型对象。
```

那么我们要继承，根据上面的定义，就很简单了：

1.调用父构造函数，这步等同于es6中的super，很好理解

2.让子构造函数的prototype对象指向这样一个对象

```javascript
{
    __proto__: 父构造函数.prototype = {
        __proro__ : {},
        constructor: 父构造函数，
        ...这里是父prototype上的其他东东
    }
    constructor: 子构造函数
    ...这里是子prototype上的其他东东
}
```

就可以啦！

因此我们用object.create，是最方便的了。参照[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create)的文档：

## 语法

```html
Object.create(proto, [propertiesObject])
```

### 参数

`proto`

新创建对象的原型对象。

`propertiesObject`

可选。如果没有指定为 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined "undefined是全局对象的一个属性。也就是说，它是全局作用域的一个变量。undefined的最初值就是原始数据类型undefined。")，则是要添加到新创建对象的可枚举属性（即其自身定义的属性，而不是其原型链上的枚举属性）对象的属性描述符以及相应的属性名称。这些属性对应[`Object.defineProperties()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperties "Object.defineProperties() 方法直接在一个对象上定义新的属性或修改现有属性，并返回该对象。")的第二个参数。

### 返回值

```javascript
{
    __proto__: proto,
    ...propertiesObject
}
```

### 例外

如果`propertiesObject`参数不是 [`null`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/null "值 null 特指对象的值未设置。它是 JavaScript 基本类型 之一。")或一个对象，则抛出一个[`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError "TypeError（类型错误） 对象用来表示值的类型非预期类型时发生的错误。")异常。

所以，用三行代码来实现继承，就是这样的:

```javascript
// 子类(subclass)
function SubClass() {
  SuperClass.call(this); // call super constructor.
}
// 子类续承父类
SubClass.prototype = Object.create(SuperClass.prototype); 
SubClass.prototype.constructor = SubClass;
```

兼容写法

```javascript
function create(proto) { 
    function F() {}
    F.prototype = proto;
    return new F();
}
```
