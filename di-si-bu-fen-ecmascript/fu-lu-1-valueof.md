# 附录1：valueOf\(\)

说实话这个方法存在感很低，在JS中对数据进行字符串转型，通常都用toString\(\)方法，或者直接在变量后面加上空字符串。valueOf\(\)方法的返回值通常与toString\(\)都是一样的。但是，在Object上，他们两个表现出了截然不同的形式，在对一个对象类型（Object、Array）进行valueOf\(\)时，valueOf\(\)直接返回原对象，而toString\(\)则返回\[object Object\]。

**在《JavaScript高级程序设计（第三版）》中，作者说valueOf\(\)返回与toString\(\)相同的值，即对Array调用valueOf\(\)返回字符串表现形式，我在多个现代浏览器中（Chrome、Egde）和IE8文档模式下测试均返回原数组对象。所以看到这里的读者要注意了，书中部分内容到现在可能并不准确了。**

```js
var a = { "a":"123", "b": "456" };
// Object {a: "123", b: "456"}
a.valueOf()
// [object object]
a.toString()
```

对于数组也一样

```js
var b = [1,2,3,45,6];
// (5) [1, 2, 3, 45, 6]
b.valueOf()
// 1,2,3,45,6
b.toString() 
```



