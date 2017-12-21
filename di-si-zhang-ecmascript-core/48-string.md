# 第8节 String

![](/assets/String.png)String对象是JavaScript原生提供的三个包装对象之一，用来生成字符串的包装对象。

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

两个用于访问字符串中特定字符的方法是：**charAt\(\)**和**charCodeAt\(\)**。这两个方法都接收一个参数化，即基于 0 的字符位置。其中，charAt\(\) 方法以单字符字符串的形式返回给定位置的那个字符（ECMAScript 中没有字符类型）。例如：

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
* toLowerCase\(\)、toLocaleLowerCase\(\)，转换为小写
* toUpperCase\(\)、toLocaleUpperCase\(\)，转换为大写

虽然concat\(\) 方法是专门用来拼接字符串的方法，但在实践中使用更多的是加号操作符（+）。而且，使用加号操作符在大多数情况下都比使用concat\(\) 方法要简便易行（特别是在拼接多个字符串的情况下）

ECMAScript 5 还提供了 3 个新的方法：

* slice\(\)，截取子字符串
* substr\(\)，截取子字符串
* substring\(\)，截取子字符串
* trim\(\)，去除字符串两端的空格

这三个方法都会返回被操作字符串的一个子字符串，而且也都接受一或两个参数。第一个参数指定子字符串的开始位置，第二个参数（在指定的情况下）表示子字符串到哪结束。

而 ES6 又提供了几个新的方法：

* padStart\(\)，字符串头部补全长度
* padEnd\(\) ，字符串尾部补全长度
* repeat\(\) ，返回一个新字符串，表示将原字符串重复n次

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

### 2.5 padStart\(\)，padEnd\(\)

ES2017 引入了字符串补全长度的功能。如果某个字符串不够指定长度，会在头部或尾部补全。padStart\(\)用于头部补全，padEnd\(\)用于尾部补全。

```js
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
```

上面代码中，padStart和padEnd一共接受两个参数，第一个参数用来指定字符串的最小长度，第二个参数是用来补全的字符串。

基本规则：

* 如果原字符串的长度，等于或大于指定的最小长度，则返回原字符串。
* 如果用来补全的字符串与原字符串，两者的长度之和超过了指定的最小长度，则会截去超出位数的补全字符串。
* 如果省略第二个参数，默认使用空格补全长度。

padStart的常见用途是为数值补全指定位数。下面代码生成 10 位的数值字符串。

```js
'1'.padStart(10, '0') // "0000000001"
'12'.padStart(10, '0') // "0000000012"
'123456'.padStart(10, '0') // "0000123456"
```

另一个用途是提示字符串格式。

```js
'12'.padStart(10, 'YYYY-MM-DD') // "YYYY-MM-12"
'09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"
```

### 2.6 repeat\(\)

repeat方法返回一个新字符串，表示将原字符串重复 n 次。

```js
'x'.repeat(3) // "xxx"
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""
```

使用规则：

* 参数如果是小数，会被取整。
* 参数是负数或者 Infinity，会报错。
* 如果参数是 0 到 -1 之间的小数，则等同于 0，这是因为会先进行取整运算。0 到 -1 之间的小数，取整以后等于 -0，repeat 视同为 0。
* 参数 NaN 等同于 0。
* 参数是字符串，则会先转换成数字。
  ```js
  'na'.repeat('na') // ""
  'na'.repeat('3') // "nanana"
  ```

## 三、字符串位置方法

ECMAScript 提供如下这两个方法用于确定一个字符串在另一个字符串中的位置

* indexOf
* lastIndexOf

都返回一个整数，表示匹配开始的位置。如果返回-1，就表示不匹配。两者的区别在于，indexOf从字符串头部开始匹配，lastIndexOf从尾部开始匹配。

它们还可以接受第二个参数，对于indexOf方法，第二个参数表示从该位置开始向后匹配；对于lastIndexOf，第二个参数表示从该位置起向前匹配。

```js
'hello world'.indexOf('o', 6) // 7
'hello world'.lastIndexOf('o', 6) // 4
```

ES6 又提供了三种新方法。

* includes\(\)：返回布尔值，表示是否找到了参数字符串。
* startsWith\(\)：返回布尔值，表示参数字符串是否在原字符串的头部。
* endsWith\(\)：返回布尔值，表示参数字符串是否在原字符串的尾部。

```js
let s = 'Hello world!';

s.startsWith('Hello') // true
s.endsWith('!') // true
s.includes('o') // true
```

这三个方法都支持第二个参数，表示开始搜索的位置。

```js
let s = 'Hello world!';

s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false
```

上面代码表示，使用第二个参数n时，endsWith的行为与其他两个方法有所不同。它针对前n个字符，而其他两个方法针对从第n个位置直到字符串结束。

## 四、模式匹配方法

String 类型定义了几个用于在字符串中匹配模式的方法。

* match\(\)，用于确定原字符串是否匹配某个子字符串
* search\(\)，用法等同于match，但是返回值为匹配的第一个位置
* replace\(\)，替换匹配的子字符串
* split\(\)，按照给定规则分割字符串

### 4.1 match\(\)、search\(\)

**match** 方法用于确定原字符串是否匹配某个子字符串，返回一个数组，成员为匹配的第一个字符串。如果没有找到匹配，则返回null。

```js
'cat, bat, sat, fat'.match('at') // ["at"]
'cat, bat, sat, fat'.match('xt') // null
```

返回数组还有 index 属性和 input 属性，分别表示匹配字符串开始的位置和原始字符串。

```js
var matches = 'cat, bat, sat, fat'.match('at');
matches.index // 1
matches.input // "cat, bat, sat, fat"
```

match方法还可以使用正则表达式作为参数，详细请参考正在表达式相关内容。

**search** 方法的用法等同于 match，但是返回值为匹配的第一个位置。如果没有找到匹配，则返回 -1。

```js
'cat, bat, sat, fat'.search('at') // 1
```

search 方法还可以使用正则表达式作为参数，详细请参考正在表达式相关内容。

### 4.2 replace\(\)

replace 方法用于替换匹配的子字符串，一般情况下只替换第一个匹配（除非使用带有 g 修饰符的正则表达式）。

```js
'aaa'.replace('a', 'b') // "baa"
```

replace方法还可以使用正则表达式作为参数

### 4.3 split\(\)

split方法按照给定规则分割字符串，返回一个由分割出来的子字符串组成的数组。

```js
'a|b|c'.split('|') // ["a", "b", "c"]
```

如果分割规则为空字符串，则返回数组的成员是原字符串的每一个字符。

```js
'a|b|c'.split('') // ["a", "|", "b", "|", "c"]
```

如果省略参数，则返回数组的唯一成员就是原字符串。

```js
'a|b|c'.split() // ["a|b|c"]
```

如果满足分割规则的两个部分紧邻着（即中间没有其他字符），则返回数组之中会有一个空字符串。

```js
'a||c'.split('|') // ['a', '', 'c']
```

如果满足分割规则的部分处于字符串的开头或结尾（即它的前面或后面没有其他字符），则返回数组的第一个或最后一个成员是一个空字符串。

```js
'|b|c'.split('|') // ["", "b", "c"]
'a|b|'.split('|') // ["a", "b", ""]
```

split方法还可以接受第二个参数，限定返回数组的最大成员数。

```js
'a|b|c'.split('|', 0) // []
'a|b|c'.split('|', 1) // ["a"]
'a|b|c'.split('|', 2) // ["a", "b"]
'a|b|c'.split('|', 3) // ["a", "b", "c"]
'a|b|c'.split('|', 4) // ["a", "b", "c"]
```

上面代码中，split方法的第二个参数，决定了返回数组的成员数。

split方法还可以使用正则表达式作为参数。

---

**参考资料**

* [JavaScript标准参考教程（alpha） ](http://javascript.ruanyifeng.com/stdlib/string.html#)
* [ECMAScript 6 入门，字符串的扩展](http://es6.ruanyifeng.com/#docs/string)



