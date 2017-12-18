# 第31章 String

String对象是JavaScript原生提供的三个包装对象之一，用来生成字符串的包装对象。

```js
var s1 = 'abc';
var s2 = new String('abc');

typeof s1 // "string"
typeof s2 // "object"

s2.valueOf() // "abc"
```

实际上，字符串的包装对象是一个类似数组的对象（即很像数组，但是实质上不是数组）。

```js
new String("abc")
// String {0: "a", 1: "b", 2: "c", length: 3}
```

除了用作构造函数，String对象还可以当作工具方法使用，将任意类型的值转为字符串。

```js
String(true) // "true"
String(5) // "5"
```

## 一、字符方法

两个用于访问字符串中特定字符的方法是：charAt\(\)和charCodeAt\(\)。这两个方法都接收一个参数化，即基于 0 的字符位置。其中，charAt\(\) 方法以单字符字符串的形式返回给定位置的那个字符（ECMAScript 中没有字符类型）。例如：

```js
var stringValue = "hello world";
// "e"
alert(stringValue.charAt(1));
```

如果你想得到的不是字符而是字符编码，那么就要像下面这样使用 charCodeAt\(\) 了

```js
var stringValue = "hello world";
//输出“101”
alert(stringValue.charCodeAt(1));
```

ECMAScript 5 还定义了另外一个访问个别字符额方法。可以使用方括号加数字索引来访问字符串中的特点字符

```js
var stringValue = "hello world";
// "e"
alert(stringValue[1]);
```

## 二、字符串操作方法

关于字符串有关的几个方法

* concat\(\)，用于将一个或多个字符串拼接起来，返回新得到的新字符串

虽然concat\(\) 方法是专门用来拼接字符串的方法，但在实践中使用更多的是加号操作符（+）。而且，使用加号操作符在大多数情况下都比使用concat\(\) 方法要简便易行（特别是在拼接多个字符串的情况下）

ECMAScript 5 还提供了 3 个新的方法：

* slice\(\)
* substr\(\)
* substring\(\)
* trim\(\)

这三个方法都会返回被操作字符串的一个子字符串，而且也都接受一或两个参数。第一个参数指定子字符串的开始位置，第二个参数（在指定的情况下）表示子字符串到哪结束。

### 2.1 slice\(\)

slice方法用于从原字符串取出子字符串并返回，不改变原字符串。

它的第一个参数是子字符串的开始位置，第二个参数是子字符串的结束位置（不含该位置）。

```js
'JavaScript'.slice(0, 4) // "Java"
```

如果省略第二个参数，则表示子字符串一直到原字符串结束。

如果参数是负值，表示从结尾开始倒数计算的位置，即该负值加上字符串长度。

```js
'JavaScript'.slice(-6) // "Script"
'JavaScript'.slice(0, -6) // "Java"
'JavaScript'.slice(-2, -1) // "p"
```

如果第一个参数大于第二个参数，slice方法返回一个空字符串。

### 2.2 substring\(\)

substring方法用于从原字符串取出子字符串并返回，不改变原字符串。它与slice作用相同，但有一些奇怪的规则，因此不建议使用这个方法，优先使用slice。

如果第二个参数大于第一个参数，substring方法会自动更换两个参数的位置。

```js
'JavaScript'.substring(10, 4) // "Script"
// 等同于
'JavaScript'.substring(4, 10) // "Script"
```

上面代码中，调换substring方法的两个参数，都得到同样的结果。

如果参数是负数，substring方法会自动将负数转为0。

```js
'Javascript'.substring(-3) // "JavaScript"
'JavaScript'.substring(4, -3) // "Java"
```

上面代码的第二个例子，参数-3会自动变成0，等同于'JavaScript'.substring\(4, 0\)。由于第二个参数小于第一个参数，会自动互换位置，所以返回Java。

### 2.3 substr\(\)

substr方法用于从原字符串取出子字符串并返回，不改变原字符串。

substr方法的第一个参数是子字符串的开始位置，第二个参数是子字符串的长度。

如果第一个参数是负数，表示倒数计算的字符位置。如果第二个参数是负数，将被自动转为0，因此会返回空字符串。

```js
'JavaScript'.substr(-6) // "Script"
'JavaScript'.substr(4, -1) // ""
```

上面代码的第二个例子，由于参数-1自动转为0，表示子字符串长度为0，所以返回空字符串。

### 2.4 trim\(\)

trim方法用于去除字符串两端的空格，返回一个新字符串，不改变原字符串。

该方法去除的不仅是空格，还包括制表符（\t、\v）、换行符（\n）和回车符（\r）。

```js
'\r\nabc \t'.trim() // 'abc'
```

## 三、字符串位置方法



