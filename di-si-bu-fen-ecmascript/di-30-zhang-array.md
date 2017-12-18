# 第30章 Array

## 一、构造函数

Array是JavaScript的内置对象，同时也是一个构造函数，可以用它生成新的数组。

```js
var arr = new Array(2);
arr.length // 2
arr // [ undefined x 2 ]
```

上面代码中，Array构造函数的参数2，表示生成一个两个成员的数组，每个位置都是空值。

如果没有使用new，运行结果也是一样的。

```js
var arr = new Array(2);
// 等同于
var arr = Array(2);
```

Array构造函数有一个很大的问题，就是不同的参数，会导致它的行为不一致。

```js
// 无参数时，返回一个空数组
new Array() // []

// 单个正整数参数，表示返回的新数组的长度
new Array(1) // [ undefined ]
new Array(2) // [ undefined x 2 ]

// 非正整数的数值作为参数，会报错
new Array(3.2) // RangeError: Invalid array length
new Array(-3) // RangeError: Invalid array length

// 单个非正整数参数（比如字符串、布尔值、对象等），
// 则该参数是返回的新数组的成员
new Array('abc') // ['abc']
new Array([1]) // [Array[1]]

// 多参数时，所有参数都是返回的新数组的成员
new Array(1, 2) // [1, 2]
new Array('a', 'b', 'c') // ['a', 'b', 'c']
```

从上面代码可以看到，Array作为构造函数，行为很不一致。因此，不建议使用它生成新数组，**直接使用数组字面量是更好的做法**。

注意，如果参数是一个正整数，返回数组的成员都是空位。虽然读取的时候返回undefined，但实际上该位置没有任何值。虽然可以取到length属性，但是取不到键名。

```js
var arr = new Array(3);
arr.length // 3

arr[0] // undefined
arr[1] // undefined
arr[2] // undefined

0 in arr // false
1 in arr // false
2 in arr // false
```

上面代码中，arr是一个长度为3的空数组。虽然可以取到每个位置的键值undefined，但是所有的键名都取不到。

## 二、检测数组

### 2.1 instanceof

自从ECMAScript 3做出了规定后，就出现了确定某个对象是否是数组的经典问题。对于一个网页，或者一个全局作用域而言，使用instanceof 操作符就能得到满意的结果：

```js
var a = [1,23,3]
a instanceof Array //true
```

instanceof 操作符的问题在于，它假定单一的全局执行环节。如果网页中包含多个框架，那实际上就存在连个以上不同的全局执行环节，从而存在两个以上不同版本的 Array 构造函数。如果你从一个框架向另一个框架传入一个数组，那么传入的数组与第二个框架中原生创建的数组分别具有各自不同的构造函数。

为了解决这个问题，ECMAScript 5 新增了 Array.isArray\(\) 方法。

### 2.2 Array.isArray\(\)

Array.isArray方法用来判断一个值是否为数组，而不管他是在哪个全局执行环境中创建的。它可以弥补typeof运算符的不足。

```js
var a = [1, 2, 3];

typeof a // "object"
Array.isArray(a) // true
```

上面代码中，typeof运算符只能显示数组的类型是Object，而Array.isArray方法可以对数组返回true。

## 三、转换方法

### 3.1 valueOf\(\)，toString\(\)

valueOf方法返回数组本身。

```js
var a = [1, 2, 3];
a.valueOf() // [1, 2, 3]
```

toString方法返回数组的字符串形式。

```js
var a = [1, 2, 3];
a.toString() // "1,2,3"

var a = [1, 2, 3, [4, 5, 6]];
a.toString() // "1,2,3,4,5,6"
```

3.2 push\(\)

