# 第24章 数据类型

## 一、数据类型介绍

最新的 ECMAScript 标准定义了 7 种数据类型:

* 6 种 原始类型

  * Boolean
  * Null
  * Number
  * String
  * Symbol\(ECMAScript 6 新定义\)

* 和 Object

**原始类型**

除 Object 以外的所有类型都是不可变的（值本身无法被改变）。例如，与 C 语言不同，JavaScript 中字符串是不可变的（译注：如，JavaScript 中对字符串的操作一定返回了一个新字符串，原始字符串并没有被改变）。我们称这些类型的值为“原始值”。

**对象**

通常，我们将数值、字符串、布尔值称为原始类型（primitive type）的值，即它们是最基本的数据类型，不能再细分了。而将对象称为合成类型（complex type）的值，因为一个对象往往是多个原始类型的值的合成，可以看作是一个存放各种值的容器。至于undefined和null，一般将它们看成两个特殊值。

对象又可以分成三个子类型。

* 狭义的对象（object）
* 数组（array）
* 函数（function）

狭义的对象和数组是两种不同的数据组合方式，而函数其实是处理数据的方法。JavaScript把函数当成一种数据类型，可以像其他类型的数据一样，进行赋值和传递，这为编程带来了很大的灵活性，体现了JavaScript作为“函数式语言”的本质。

> 这里需要明确的是，JavaScript的所有数据，都可以视为广义的对象。不仅数组和函数属于对象，就连原始类型的数据（数值、字符串、布尔值）也可以用对象方式调用。

## 二、null 和 undefined

null与undefined都可以表示“没有”，含义非常相似。将一个变量赋值为undefined或null，老实说，语法效果几乎没区别。

```js
var a = undefined;
// 或者
var a = null;
```

上面代码中，a变量分别被赋值为undefined和null，这两种写法的效果几乎等价。

在if语句中，它们都会被自动转为false，相等运算符（==）甚至直接报告两者相等。

1995年 JavaScript 诞生时，最初像Java一样，只设置了null作为表示”无”的值。根据C语言的传统，null被设计成可以自动转为0。

```js
Number(null) // 0
5 + null // 5
```

但是，JavaScript的设计者Brendan Eich，觉得这样做还不够，有两个原因。首先，null像在Java里一样，被当成一个对象。但是，JavaScript的值分成原始类型和合成类型两大类，Brendan Eich觉得表示”无”的值最好不是对象。其次，JavaScript的最初版本没有包括错误处理机制，发生数据类型不匹配时，往往是自动转换类型或者默默地失败。Brendan Eich觉得，如果null自动转为0，很不容易发现错误。

因此，Brendan Eich又设计了一个undefined。他是这样区分的：null是一个表示”无”的对象，转为数值时为0；undefined是一个表示”无”的原始值，转为数值时为NaN。

```js
Number(undefined) // NaN
5 + undefined // NaN
```

但是，这样的区分在实践中很快就被证明不可行。目前null和undefined基本是同义的，只有一些细微的差别。

null的特殊之处在于，JavaScript把它包含在对象类型（object）之中。

```js
typeof null // "object"
```

**注意**，**JavaScript的标识名区分大小写，所以undefined和null不同于Undefined和Null（或者其他仅仅大小写不同的词形），后者只是普通的变量名。**

---

**参考资料**

* [JavaScript 数据类型和数据结构](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Data_structures#对象) by MDN
* [JavaScript标准参考教程（alpha）by 阮一峰](http://javascript.ruanyifeng.com/grammar/types.html) [- 数据类型](http://javascript.ruanyifeng.com/grammar/types.html)



