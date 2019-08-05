```javascript
123 instanceof Number
Number(123) instanceof Number
new Number(123) 
```

首先，instanceof是用于对象的，也就是继承自object的，123是Number，是false。

其次，如果不用 new 运算符，把 Number() 作为一个函数来调用，它是将把自己的参数转换成一个原始的number类型数值，并且返回这个值。因此Number(123)的返回类型就是Number，和一一样，是false。

最后，new Number返回一个包装对象，可以找到原型链，因此返回true。

### tips

```javascript
let a = 123
```

这个a是有原型链的，我们用Object.getPrototypeOf或者\__proto__都能看到。

因此猜测instanceof开始对类型进行了判断，如果不是对象，直接返回false。


