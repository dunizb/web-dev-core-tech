# 第29章 Object

## 一、Object\(\)

Object本身当作工具方法使用时，可以将任意值转为对象。这个方法常用于保证某个值一定是对象。如果参数是原始类型的值，Object方法返回对应的包装对象的实例

```js
Object() // 返回一个空对象
Object() instanceof Object // true

Object(undefined) // 返回一个空对象
Object(undefined) instanceof Object // true

Object(null) // 返回一个空对象
Object(null) instanceof Object // true

Object(1) // 等同于 new Number(1)
Object(1) instanceof Object // true
Object(1) instanceof Number // true

Object('foo') // 等同于 new String('foo')
Object('foo') instanceof Object // true
Object('foo') instanceof String // true

Object(true) // 等同于 new Boolean(true)
Object(true) instanceof Object // true
Object(true) instanceof Boolean // true
```

上面代码表示Object函数可以将各种值转为对应的构造函数生成的对象。

如果Object方法的参数是一个对象，它总是返回原对象。

```js
var arr = [];
Object(arr) // 返回原数组
Object(arr) === arr // true

var obj = {};
Object(obj) // 返回原对象
Object(obj) === obj // true

var fn = function () {};
Object(fn) // 返回原函数
Object(fn) === fn // true
```

利用这一点，可以写一个判断变量是否为对象的函数。

```js
function isObject(value) {
  return value === Object(value);
}

isObject([]) // true
isObject(true) // false
```

## 二、Object 对象的静态方法

所谓“静态方法”，是指部署在Object对象自身的方法。

### 2.1 Object.keys\(\)，Object.getOwnPropertyNames\(\)

Object.keys方法和Object.getOwnPropertyNames方法很相似，一般用来遍历对象的属性。它们的参数都是一个对象，都返回一个数组，该数组的成员都是对象自身的（而不是继承的）所有属性名。它们的区别在于，Object.keys方法只返回可枚举的属性（关于可枚举性的详细解释见后文），Object.getOwnPropertyNames方法还返回不可枚举的属性名。

```js
var o = {
  p1: 123,
  p2: 456
};

Object.keys(o)
// ["p1", "p2"]

Object.getOwnPropertyNames(o)
// ["p1", "p2"]
```

上面的代码表示，对于一般的对象来说，这两个方法返回的结果是一样的。只有涉及不可枚举属性时，才会有不一样的结果。

```js
var a = ["Hello", "World"];

Object.keys(a)
// ["0", "1"]

Object.getOwnPropertyNames(a)
// ["0", "1", "length"]
```

上面代码中，数组的length属性是不可枚举的属性，所以只出现在Object.getOwnPropertyNames方法的返回结果中。

由于JavaScript没有提供计算对象属性个数的方法，所以可以用这两个方法代替。

```js
Object.keys(o).length
Object.getOwnPropertyNames(o).length
```

一般情况下，几乎总是使用Object.keys方法，遍历数组的属性。

### 2.2 其他方法

除了上面提到的方法，Object还有不少其他方法，将在后文逐一详细介绍。

**对象属性模型的相关方法**

* Object.getOwnPropertyDescriptor\(\)：获取某个属性的attributes对象。
* Object.defineProperty\(\)：通过attributes对象，定义某个属性。
* Object.defineProperties\(\)：通过attributes对象，定义多个属性。
* Object.getOwnPropertyNames\(\)：返回直接定义在某个对象上面的全部属性的名称。

**控制对象状态的方法**

* Object.preventExtensions\(\)：防止对象扩展。
* Object.isExtensible\(\)：判断对象是否可扩展。
* Object.seal\(\)：禁止对象配置。
* Object.isSealed\(\)：判断一个对象是否可配置。
* Object.freeze\(\)：冻结一个对象。
* Object.isFrozen\(\)：判断一个对象是否被冻结。

**原型链相关方法**

* Object.create\(\)：该方法可以指定原型对象和属性，返回一个新的对象。
* Object.getPrototypeOf\(\)：获取对象的Prototype对象。

## 三、Object对象的实例方法

除了Object对象本身的方法，还有不少方法是部署在Object.prototype对象上的，所有Object的实例对象都继承了这些方法。

Object实例对象的方法，主要有以下六个。

* valueOf\(\)：返回当前对象对应的值。
* toString\(\)：返回当前对象对应的字符串形式。
* toLocaleString\(\)：返回当前对象对应的本地字符串形式。
* hasOwnProperty\(\)：判断某个属性是否为当前对象自身的属性，还是继承自原型对象的属性。
* isPrototypeOf\(\)：判断当前对象是否为另一个对象的原型。
* propertyIsEnumerable\(\)：判断某个属性是否可枚举。

本节介绍前两个方法，其他方法将在后文相关章节介绍。

### 3.1 Object.prototype.valueOf\(\)

valueOf方法的作用是返回一个对象的“值”，默认情况下返回对象本身。

```js
var o = new Object();
o.valueOf() === o // true
```

上面代码比较o.valueOf\(\)与o本身，两者是一样的。

valueOf方法的主要用途是，JavaScript自动类型转换时会默认调用这个方法。

```js
var o = new Object();
1 + o // "1[object Object]"
```

上面代码将对象o与数字1相加，这时JavaScript就会默认调用valueOf\(\)方法。所以，如果自定义valueOf方法，就可以得到想要的结果。

```js
var o = new Object();
o.valueOf = function (){
  return 2;
};

1 + o // 3
```

上面代码自定义了o对象的valueOf方法，于是1 + o就得到了3。这种方法就相当于用o.valueOf覆盖Object.prototype.valueOf。

### 3.2 Object.prototype.toString\(\)

toString方法的作用是返回一个对象的字符串形式，默认情况下返回类型字符串。

```js
var o1 = new Object();
o1.toString() // "[object Object]"

var o2 = {a:1};
o2.toString() // "[object Object]"
```

上面代码表示，对于一个对象调用toString方法，会返回字符串\[object Object\]，该字符串说明对象的类型。

字符串\[object Object\]本身没有太大的用处，但是通过自定义toString方法，可以让对象在自动类型转换时，得到想要的字符串形式。

```js
var o = new Object();

o.toString = function () {
  return 'hello';
};

o + ' ' + 'world' // "hello world"
```

上面代码表示，当对象用于字符串加法时，会自动调用toString方法。由于自定义了toString方法，所以返回字符串hello world。

数组、字符串、函数、Date对象都分别部署了自己版本的toString方法，覆盖了Object.prototype.toString方法。

```js
[1, 2, 3].toString() // "1,2,3"

'123'.toString() // "123"

(function () {
  return 123;
}).toString()
// "function () {
//   return 123;
// }"

(new Date()).toString()
// "Tue May 10 2016 09:11:31 GMT+0800 (CST)"
```

### 3.3 toString\(\)的应用：判断数据类型









