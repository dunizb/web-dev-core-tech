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















