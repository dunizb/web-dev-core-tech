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



