# reflect.construct

今天要讲的是reflect.construct，不知道reflect的请自行了解一下。

## construct

construct是模拟new操作运算符的，但是是函数式的。

```javascript
Reflect.construct(target:function, argumentsList:any[], newTarget?:function)
```

很容易理解的是第一个参数和第二个参数，分别对应new（target，...args），需要理解的是第三个参数，这个东西在new里面是没有的。

举例，来自mdn：

```javascript
// Calling this:
var obj1 = Reflect.construct(OneClass, args, OtherClass);

// ...has the same result as this:
var obj2 = Object.create(OtherClass.prototype);
OneClass.apply(obj2, args);
```

明白了吗？target指向的是你的构造函数，而newTarget代表你实际的原型指向，也就是说，obj2执行了OneClass的构造函数，但是原型是OtherClass。
