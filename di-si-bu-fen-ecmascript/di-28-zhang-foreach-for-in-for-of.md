# 第28章 forEach、for-in、for-of

## 一、forEach

自从JavaScript5起，我们开始可以使用内置的forEach方法：

```js
myArray.forEach(function (value) {
  console.log(value);
});
```

写法简单了许多，但也有短处：你不能中断循环\(使用break语句或使用return语句。

## 二、for–in

JavaScript里还有一种循环方法：for–in。

for-in循环实际是为循环”enumerable“对象而设计的：

```js
var obj = {a:1, b:2, c:3};

for (var prop in obj) {
  console.log("obj." + prop + " = " + obj[prop]);
}

// 输出:
// "obj.a = 1"
// "obj.b = 2"
// "obj.c = 3"
```

你也可以用它来循环一个数组：

```js
for (var index in myArray) {    // 不推荐这样
  console.log(myArray[index]);
}
```

不推荐用for-in来循环一个数组，因为，不像对象，数组的index跟普通的对象属性不一样，是重要的数值序列指标。

总之，for–in是用来循环带有字符串key的对象的方法。

for-in 循环的每次迭代都会产生更多开销，因此要比其他循环类型慢，一般速度为其他类型循环的 1/7。因此，除非明确需要迭代一个属性数量未知的对象，否则应避免使用 for-in 循环。如果需要遍历一个数量有限的已知属性列表，使用其他循环会更快。

## 三、for-of循环

JavaScript6里引入了一种新的循环方法，它就是for-of循环，它既比传统的for循环简洁，同时弥补了forEach和for-in循环的短板。

我们看一下它的for-of的语法：

```js
for (var value of myArray) {
  console.log(value);
}
```

for-of的语法看起来跟for-in很相似，但它的功能却丰富的多，它能循环很多东西。

**循环一个数组\(Array\)**

```js
let iterable = [10, 20, 30];

for (let value of iterable) {
  console.log(value);
}
// 10
// 20
// 30
```

我们可以使用const来替代let，这样它就变成了在循环里的不可修改的静态变量。

```js
let iterable = [10, 20, 30];

for (const value of iterable) {
  console.log(value);
}
// 10
// 20
// 30
```

**循环一个字符串**

```js
let iterable = "boo";

for (let value of iterable) {
  console.log(value);
}
// "b"
// "o"
// "o"
```

**循环一个类型化的数组**

```js
let iterable = new Uint8Array([0x00, 0xff]);

for (let value of iterable) {
  console.log(value);
}
// 0
// 255
```

**循环一个Map**

```js
let iterable = new Map([["a", 1], ["b", 2], ["c", 3]]);

for (let [key, value] of iterable) {
  console.log(value);
}
// 1
// 2
// 3

for (let entry of iterable) {
  console.log(entry);
}
// [a, 1]
// [b, 2]
// [c, 3]
```

**循环一个 Set**

```js
let iterable = new Set([1, 1, 2, 2, 3, 3]);

for (let value of iterable) {
  console.log(value);
}
// 1
// 2
// 3

```

**循环一个 DOM collection**

循环一个DOM collections，比如NodeList，之前我们讨论过如何循环一个NodeList，现在方便了，可以直接使用for-of循环：

```js
// Note: This will only work in platforms that have
// implemented NodeList.prototype[Symbol.iterator]
let articleParagraphs = document.querySelectorAll("article > p");

for (let paragraph of articleParagraphs) {
  paragraph.classList.add("read");
}
```

**循环一个拥有enumerable属性的对象**

for–of循环并不能直接使用在普通的对象上，但如果我们按对象所拥有的属性进行循环，可使用内置的Object.keys\(\)方法：

```js
for (var key of Object.keys(someObject)) {
  console.log(key + ": " + someObject[key]);
}
```

**循环一个生成器\(generators\)**

```js
function* fibonacci() { // a generator function
  let [prev, curr] = [0, 1];
  while (true) {
    [prev, curr] = [curr, prev + curr];
    yield curr;
  }
}

for (let n of fibonacci()) {
  console.log(n);
  // truncate the sequence at 1000
  if (n >= 1000) {
    break;
  }
}
```











