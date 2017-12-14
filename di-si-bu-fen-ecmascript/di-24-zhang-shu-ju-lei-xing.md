# 第24章 数据类型

## 一、数据类型介绍

最新的 ECMAScript 标准定义了 8 种数据类型:

* 7 种 原始类型

  * Boolean
  * Null
  * Undefined
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

## 三、布尔值

如果JavaScript预期某个位置应该是布尔值，会将该位置上现有的值自动转为布尔值。转换规则是除了下面六个值被转为false，其他值都视为true。

* undefined
* null
* false
* 0
* NaN
* ""或''（空字符串）

需要特别注意的是，空数组（\[\]）和空对象（{}）对应的布尔值，都是true。

```js
if ([]) {
  console.log(true);
}
// true

if ({}) {
  console.log(true);
}
// true
```

## 四、typeof运算符

JavaScript有三种方法，可以确定一个值到底是什么类型。

* typeof运算符
* instanceof运算符
* Object.prototype.toString方法

typeof运算符可以返回一个值的数据类型，可能有以下结果。

**1.原始类型**

数值、字符串、布尔值分别返回number、string、boolean。

**2.函数**

函数返回function。

```js
function f() {}
typeof f
// "function"
```

**3.undefined**

undefined返回undefined。

```js
typeof undefined
// "undefined"
```

利用这一点，typeof可以用来检查一个没有声明的变量，而不报错。

实际编程中，这个特点通常用在判断语句。

```js
// 错误的写法
if (v) {
  // ...
}
// ReferenceError: v is not defined

// 正确的写法
if (typeof v === "undefined") {
  // ...
}
```

**4.其他**

除此以外，其他情况都返回object。

```js
typeof window // "object"
typeof {} // "object"
typeof [] // "object"
typeof null // "object"
```

从上面代码可以看到，空数组（\[\]）的类型也是object，这表示在JavaScript内部，数组本质上只是一种特殊的对象。

另外，null的类型也是object，这是由于历史原因造成的。这并不是说null就属于对象，本质上null是一个类似于undefined的特殊值。

既然typeof对数组（array）和对象（object）的显示结果都是object，那么怎么区分它们呢？instanceof运算符可以做到。

```js
var o = {};
var a = [];

o instanceof Array // false
a instanceof Array // true
```







 

 

---

**参考资料**

* [JavaScript 数据类型和数据结构](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Data_structures#对象) by MDN
* [JavaScript标准参考教程（alpha）by 阮一峰](http://javascript.ruanyifeng.com/grammar/types.html) [- 数据类型](http://javascript.ruanyifeng.com/grammar/types.html)



