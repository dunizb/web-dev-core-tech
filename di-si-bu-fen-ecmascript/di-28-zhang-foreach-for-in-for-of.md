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

### 3.1 **为什么要引进 for-of？**

要回答这个问题，我们先来看看ES6之前的 3 种 for 循环有什么缺陷：

* forEach 不能 break 和 return；
* for-in 缺点更加明显，它不仅遍历数组中的元素，还会遍历自定义的属性，甚至原型链上的属性都被访问到。而且，遍历数组元素的顺序可能是随机的。

所以，鉴于以上种种缺陷，我们需要改进原先的 for 循环。但 ES6 不会破坏你已经写好的 JS 代码。目前，成千上万的 Web 网站依赖 for-in 循环，其中一些网站甚至将其用于数组遍历。如果想通过修正 for-in 循环增加数组遍历支持会让这一切变得更加混乱，因此，标准委员会在 ES6 中增加了一种新的循环语法来解决目前的问题，即 for-of 。

我们看一下它的for-of的语法：

```js
for (var value of myArray) {
  console.log(value);
}
```

for-of的语法看起来跟for-in很相似，但它的功能却丰富的多，它能循环很多东西。

### 3.2 **那 for-of 到底可以干什么呢？**

* 跟 forEach 相比，可以正确响应 break, continue, return。
* for-of 循环不仅支持数组，还支持大多数类数组对象，例如 DOM nodelist 对象。
* for-of 循环也支持字符串遍历，它将字符串视为一系列 Unicode 字符来进行遍历。
* for-of 也支持 Map 和 Set （两者均为 ES6 中新增的类型）对象遍历。

但需要注意的是，**for-of循环不支持普通对象**，但如果你想迭代一个对象的属性，你可以用 for-in 循环（这也是它的本职工作）。

最后要说的是，ES6 引进的另一个方式也能实现遍历数组的值，那就是 Iterator。

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

## 四、其他

但是 ES5 定义了一些其他有用的方法，下面是一部分：

* every: 循环在第一次 return false 后返回
* some: 循环在第一次 return true 后返回
* filter: 返回一个新的数组，该数组内的元素满足回调函数
* map: 将原数组中的元素处理后再返回
* reduce: 对数组中的元素依次处理，将上次处理结果作为下次处理的输入，最后得到最终结果。







