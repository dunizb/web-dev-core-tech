# 第32章 RegExp

正则表达式（regular expression）是一种表达文本模式（即字符串结构）的方法，有点像字符串的模板，常常用作按照“给定模式”匹配文本的工具。比如，正则表达式给出一个 Email 地址的模式，然后用它来确定一个字符串是否为 Email 地址。**JavaScript 的正则表达式体系是参照 Perl 5 建立的。**

## 一、新增正则表达式

新建正则表达式有两种方法。一种是使用字面量，以斜杠表示开始和结束。

```js
var regex = /xyz/;
```

另一种是使用 RegExp 构造函数。

```js
var regex = new RegExp('xyz');
```

上面两种写法是等价的，都新建了一个内容为xyz的正则表达式对象。**它们的主要区别是，第一种方法在编译时新建正则表达式，第二种方法在运行时新建正则表达式。**

RegExp 构造函数还可以接受第二个参数，表示修饰符（详细解释见下文）。

```js
var regex = new RegExp('xyz', "i");
// 等价于
var regex = /xyz/i;
```

这两种写法——字面量和构造函数——在运行时有一个细微的区别。采用字面量的写法，正则对象在代码载入时（即编译时）生成；采用构造函数的方法，正则对象在代码运行时生成。**考虑到书写的便利和直观，实际应用中，基本上都采用字面量的写法。**

正则对象生成以后，有两种使用方式：

* 正则对象的方法：将字符串作为参数，比如regex.test\(string\)。
* 字符串对象的方法：将正则对象作为参数，比如string.match\(regex\)。

## 二、正则对象的属性和方法

### 2.1 属性

正则对象的属性分成两类。

一类是修饰符相关，返回一个布尔值，表示对应的修饰符是否设置。

* ignoreCase：返回一个布尔值，表示是否设置了i修饰符，该属性只读。
* global：返回一个布尔值，表示是否设置了g修饰符，该属性只读。
* multiline：返回一个布尔值，表示是否设置了m修饰符，该属性只读。

```js
var r = /abc/igm;

r.ignoreCase // true
r.global // true
r.multiline // true
```

另一类是与修饰符无关的属性，主要是下面两个。

* lastIndex：返回下一次开始搜索的位置。该属性可读写，但是只在设置了g修饰符时有意义。
* source：返回正则表达式的字符串形式（不包括反斜杠），该属性只读。

```js
var r = /abc/igm;

r.lastIndex // 0
r.source // "abc"
```

### 2.2 **test\(\)**

正则对象的test方法返回一个布尔值，表示当前模式是否能匹配参数字符串。

```js
/cat/.test('cats and dogs') // true
```

如果正则表达式带有g修饰符，则每一次test方法都从上一次结束的位置开始向后匹配。

带有g修饰符时，可以通过正则对象的lastIndex属性指定开始搜索的位置。

```js
var r = /x/g;
var s = '_x_x';

r.lastIndex = 4;
r.test(s) // false
```

lastIndex属性只对同一个正则表达式有效，所以下面这样写是错误的。

```js
var count = 0;
while (/a/g.test('babaa')) count++;
```

上面代码会导致无限循环，因为while循环的每次匹配条件都是一个新的正则表达式，导致lastIndex属性总是等于0。

如果正则模式是一个空字符串，则匹配所有字符串。

```js
new RegExp('').test('abc')
// true
```

### 2.3 exec\(\)

正则对象的exec方法，可以返回匹配结果。如果发现匹配，就返回一个数组，成员是每一个匹配成功的子字符串，否则返回null。

```js
var s = '_x_x';
var r1 = /x/;
var r2 = /y/;

r1.exec(s) // ["x"]
r2.exec(s) // null
```

如果正则表示式包含圆括号（即含有“组匹配”），则返回的数组会包括多个成员。第一个成员是整个匹配成功的结果，后面的成员就是圆括号对应的匹配成功的组。也就是说，第二个成员对应第一个括号，第三个成员对应第二个括号，以此类推。整个数组的length属性等于组匹配的数量再加1。

```js
var s = '_x_x';
var r = /_(x)/;

r.exec(s) // ["_x", "x"]
```

上面代码的exec方法，返回一个数组。第一个成员是整个匹配的结果，第二个成员是圆括号匹配的结果。

exec方法的返回数组还包含以下两个属性：

* input：整个原字符串。
* index：整个模式匹配成功的开始位置（从0开始计数）。

```js
var r = /a(b+)a/;
var arr = r.exec('_abbba_aba_');

arr // ["abbba", "bbb"]

arr.index // 1
arr.input // "_abbba_aba_"
```

上面代码中的index属性等于1，是因为从原字符串的第二个位置开始匹配成功。

如果正则表达式加上 g 修饰符，则可以使用多次 exec 方法，下一次搜索的位置从上一次匹配成功结束的位置开始。

```js
var r = /a(b+)a/g;

var a1 = r.exec('_abbba_aba_');
a1 // ['abbba', 'bbb']
a1.index // 1
r.lastIndex // 6

var a2 = r.exec('_abbba_aba_');
a2 // ['aba', 'b']
a2.index // 7
r.lastIndex // 10

var a3 = r.exec('_abbba_aba_');
a3 // null
a3.index // TypeError: Cannot read property 'index' of null
r.lastIndex // 0

var a4 = r.exec('_abbba_aba_');
a4 // ['abbba', 'bbb']
a4.index // 1
r.lastIndex // 6
```

上面代码连续用了四次exec方法，前三次都是从上一次匹配结束的位置向后匹配。当第三次匹配结束以后，整个字符串已经到达尾部，正则对象的lastIndex属性重置为0，意味着第四次匹配将从头开始。

利用g修饰符允许多次匹配的特点，可以用一个循环完成全部匹配。

```js
var r = /a(b+)a/g;
var s = '_abbba_aba_';

while(true) {
  var match = r.exec(s);
  if (!match) break;
  console.log(match[1]);
}
// bbb
// b
```

正则对象的lastIndex属性不仅可读，还可写。一旦手动设置了lastIndex的值，就会从指定位置开始匹配。但是，这只在设置了g修饰符的情况下，才会有效。

如果正则对象是一个空字符串，则exec方法会匹配成功，但返回的也是空字符串。













