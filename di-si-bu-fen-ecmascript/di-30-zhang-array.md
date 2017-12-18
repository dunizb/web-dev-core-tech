# 第30章 Array

![](/assets/Array.png)

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

转换方法主要有valueOf、toString、toLocaleString

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

## 四、栈方法

### 4.1 push

push方法用于在数组的末端添加一个或多个元素，并返回添加新元素后的数组长度。注意，**该方法会改变原数组**。

```js
var a = [];

a.push(1) // 1
a.push('a') // 2
a.push(true, {}) // 4
a // [1, 'a', true, {}]
```

上面代码使用push方法，先后往数组中添加了四个成员。

如果需要合并两个数组，可以这样写。

```js
var a = [1, 2, 3];
var b = [4, 5, 6];

Array.prototype.push.apply(a, b)
// 或者
a.push.apply(a, b)

// 上面两种写法等同于
a.push(4, 5, 6)

a // [1, 2, 3, 4, 5, 6]
```

push方法还可以用于向对象添加元素，添加后的对象变成类似数组的对象，即新加入元素的键对应数组的索引，并且对象有一个length属性。

```js
var a = {a: 1};

[].push.call(a, 2);
a // {a:1, 0:2, length: 1}

[].push.call(a, [3]);
a // {a:1, 0:2, 1:[3], length: 2}
```

### 4.2 pop\(\)

pop方法用于删除数组的最后一个元素，并返回该元素。注意，**该方法会改变原数组**。

对空数组使用pop方法，不会报错，而是返回undefined。

```js
[].pop() // undefined
```

push和pop结合使用，就构成了“后进先出”的栈结构（stack）。

## 五、队列方法

栈数据结构的访问规则是LIFO（先进后出），而队列数据结构是FIFO（先进先出）。队列在列表的末端添加项，从列表的前端移除项。由于 push\(\) 是向数组末端添加项的方法，因此需要模拟队列只需要一个从数组前端取得项的方法。实现这一操作的数组方法是 shift\(\) ，结合 shift\(\) 和 push\(\) 能够像使用队列一样使用数组。

### 5.1 shift\(\)

shift方法用于删除数组的第一个元素，并返回该元素。注意，**该方法会改变原数组**。

shift方法可以遍历并清空一个数组。

```js
var list = [1, 2, 3, 4, 5, 6];
var item;

while (item = list.shift()) {
  console.log(item);
}

list // []
```

### 5.2 unshift\(\)

unshift方法与 shift\(\) 相反，用于在数组的第一个位置添加元素，并返回添加新元素后的数组长度。注意，**该方法会改变原数组**。

同时使用 unshift\(\) 和 pop\(\) 方法可以相反的方向来模拟队列，即在数组的前端添加项，从数组的末端移除项。

unshift方法可以在数组头部添加多个元素。

```js
var arr = [ 'c', 'd' ];
arr.unshift('a', 'b') // 4
arr // [ 'a', 'b', 'c', 'd' ]
```

## 六、重排序方法

### 6.1 reverse\(\)

reverse方法用于颠倒数组中元素的顺序，返回改变后的数组。注意，**该方法将改变原数组**。

### 6.2 sort\(\)

sort方法对数组成员进行排序，默认是按照字典顺序排序。**排序后，原数组将被改变**。

```js
['d', 'c', 'b', 'a'].sort()
// ['a', 'b', 'c', 'd']

[4, 3, 2, 1].sort()
// [1, 2, 3, 4]

[11, 101].sort()
// [101, 11]

[10111, 1101, 111].sort()
// [10111, 1101, 111]
```

上面代码的最后两个例子，需要特殊注意。sort方法不是按照大小排序，而是按照对应字符串的字典顺序排序。也就是说，数值会被先转成字符串，再按照字典顺序进行比较，所以101排在11的前面。

如果想让sort方法按照自定义方式排序，可以传入一个函数作为参数，表示按照自定义方法进行排序。该函数本身又接受两个参数，表示进行比较的两个元素。如果返回值大于0，表示第一个元素排在第二个元素后面；其他情况下，都是第一个元素排在第二个元素前面。

```js
[10111, 1101, 111].sort(function (a, b) {
  return a - b;
})
// [111, 1101, 10111]

[
  { name: "张三", age: 30 },
  { name: "李四", age: 24 },
  { name: "王五", age: 28  }
].sort(function (o1, o2) {
  return o1.age - o2.age;
})
// [
//   { name: "李四", age: 24 },
//   { name: "王五", age: 28  },
//   { name: "张三", age: 30 }
// ]
```

> 跟多请参考：[《模拟JavaScript的Array.sort方法》](https://dunizb.com/2016/07/07/模拟JavaScript的Array.sort%28%29方法/)

## 七、操作方法

### 7.1 concat\(\)

concat方法用于多个数组的合并。它将新数组的成员，添加到原数组成员的后部，然后返回一个新数组，**原数组不变**。

```js
['hello'].concat(['world'])
// ["hello", "world"]

['hello'].concat(['world'], ['!'])
// ["hello", "world", "!"]
```

除了接受数组作为参数，concat也可以接受其他类型的值作为参数。它们会作为新的元素，添加数组尾部。

```js
[1, 2, 3].concat(4, 5, 6)
// [1, 2, 3, 4, 5, 6]

// 等同于
[1, 2, 3].concat(4, [5, 6])
[1, 2, 3].concat([4], [5, 6])
```

如果不提供参数，concat方法返回当前数组的一个浅拷贝。**所谓“浅拷贝”，指的是如果数组成员包括复合类型的值（比如对象），则新数组拷贝的是该值的引用。**

```js
var obj = { a:1 };
var oldArray = [obj];

var newArray = oldArray.concat();

obj.a = 2;
newArray[0].a // 2
```

上面代码中，原数组包含一个对象，concat方法生成的新数组包含这个对象的引用。所以，改变原对象以后，新数组跟着改变。事实上，只要原数组的成员中包含对象，concat方法不管有没有参数，总是返回该对象的引用。

concat方法也可以用于将对象合并为数组。

```js
[].concat({a: 1}, {b: 2})
// [{ a: 1 }, { b: 2 }]

[].concat({a: 1}, [2])
// [{a: 1}, 2]

[2].concat({a: 1})
// [2, {a: 1}]
```

### 7.2 slice\(\)

slice方法用于提取原数组的一部分，返回一个新数组，**原数组不变**。

它的第一个参数为起始位置（从0开始），第二个参数为终止位置（但该位置的元素本身不包括在内）。如果省略第二个参数，则一直返回到原数组的最后一个成员。

如果slice方法的参数是负数，则表示倒数计算的位置。

```js
var a = ['a', 'b', 'c'];
a.slice(-2) // ["b", "c"]
a.slice(-2, -1) // ["b"]
```

上面代码中，-2表示倒数计算的第二个位置，-1表示倒数计算的第一个位置。

如果参数值大于数组成员的个数，或者第二个参数小于第一个参数，则返回空数组。

```js
var a = ['a', 'b', 'c'];
a.slice(4) // []
a.slice(2, 1) // []
```

slice方法的一个重要应用，是将类似数组的对象转为真正的数组。

```js
Array.prototype.slice.call({ 0: 'a', 1: 'b', length: 2 })
// ['a', 'b']

Array.prototype.slice.call(document.querySelectorAll("div"));
Array.prototype.slice.call(arguments);
```

上面代码的参数都不是数组，但是通过call方法，在它们上面调用slice方法，就可以把它们转为真正的数组。

### 7.3 splice\(\)

splice方法用于删除原数组的一部分成员，并可以在被删除的位置添加入新的数组成员，返回值是被删除的元素。注意，**该方法会改变原数组。**

splice\(\) 方法有很多种用法，如下

**删除**

可以删除任意数量的项，只需要指定 2 个参数：要删除的第一项的位置和要删除的项数。例如：splice\(0,2\)会删除数组中的前两项。

**插入**

可以向指定位置插入任意数量的项，只需要提供 3 个参数：起始位置、0（要删除的项数）和要插入的项。如果要插入多项，可以再传入第四、第五，以至任意多个项。例如，splice\(2,0,"red","green"\)会从当前数组的位置 2 开始插入字符串“red”和“green”。

**替换**

可以向指定位置插入任意数量的项，且同时删除任意数量的项，只需要指定  3 个参数：起始位置、要删除的项数和要插入的任意数量的项。插入的项数不必与删除的项数相等。例如：splice\(2,1,"red","green"\)会删除当前数组位置 2 的项，然后从位置 2 开始插入字符串“red”和“green”。

### 7.4 join\(\)

join方法以参数作为分隔符，将所有数组成员组成一个字符串返回。如果不提供参数，默认用逗号分隔。

```js
var a = [1, 2, 3, 4];

a.join(' ') // '1 2 3 4'
a.join(' | ') // "1 | 2 | 3 | 4"
a.join() // "1,2,3,4"
```

如果数组成员是undefined或null或空位，会被转成空字符串。

```js
[undefined, null].join('#')
// '#'

['a',, 'b'].join('-')
// 'a--b'
```

通过call方法，这个方法也可以用于字符串。

```js
Array.prototype.join.call('hello', '-')
// "h-e-l-l-o"
```

join方法也可以用于类似数组的对象。

```js
var obj = { 0: 'a', 1: 'b', length: 2 };
Array.prototype.join.call(obj, '-')
// 'a-b'
```

## 八、位置方法

ECMAScript 5 为数组实例添加了两个位置方法：indexOf\(\)和lastIndexOf\(\)。这两个方法都接收两个参数：要查找的项和（可选的）表示查找起点位置索引。其中indexOf\(\) 方法从数组的开头（位置0）开始向后查找，lastIndexOf\(\) 方法则从数组的末尾开始向前超找。

这两个方法都返回要查找的项在数组中的位置，或者在没有找到的情况下返回 -1 。在比较第一个参数与数组中的每一项时，会使用全等操作符；也就是所，要求查找的项必须严格相等。

## 九、迭代方法

ECMAScript 5 为数组定义了 7 个迭代方法。每个方法都接收两个参数：要在每一项上运行的函数和（可选的）运行改函数的作用域对象——影响 this 的值。传入这些方法中的函数会接收三个参数：数组项的值、该项在数组中的位置和数组对象本身。

以下是这 7 个 迭代方法的作用。

* every\(\)，对数组中的每一项运行给定的函数，如果该函数对每一项都返回 true ，则返回 true。
* filter\(\)，对数组中的每一项运行给定函数，返回该函数会返回 true 的项组成的数组。
* forEach\(\)，对数组中的每一项运行给定函数。这个fan刚发没有返回值。
* map\(\)，对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。
* some\(\)，对数组中的每一项运行给定函数，如果该函数对任一项返回 true ，则返回 true。
* reduce\(\)，依次处理数组的每个成员，最终累计为一个值。
* reduceRight\(\)，依次处理数组的每个成员，最终累计为一个值。

以上方法都不会修改数组中的包含的值。

ES6 提供三个新的方法用于遍历数组。

* entries\(\)
* keys\(\)
* values\(\)

它们都返回一个遍历器对象，可以用for...of循环进行遍历，唯一的区别是keys\(\)是对键名的遍历、values\(\)是对键值的遍历，entries\(\)是对键值对的遍历。

### 9.1 some\(\)，every\(\)

这两个方法类似“断言”（assert），用来判断数组成员是否符合某种条件。

它们接受一个函数作为参数，所有数组成员依次执行该函数，返回一个布尔值。该函数接受三个参数，依次是当前位置的成员、当前位置的序号和整个数组。

some方法是只要有一个数组成员的返回值是true，则整个some方法的返回值就是true，否则false。

```js
var arr = [1, 2, 3, 4, 5];
arr.some(function (elem, index, arr) {
  return elem >= 3;
});
// true
```

上面代码表示，如果存在大于等于3的数组成员，就返回true。

every方法则是所有数组成员的返回值都是true，才返回true，否则false。

**注意，对于空数组，some方法返回false，every方法返回true，回调函数都不会执行。**

```js
function isEven(x) { return x % 2 === 0 }

[].some(isEven) // false
[].every(isEven) // true
```

some和every方法还可以接受第二个参数，用来绑定函数中的this关键字。

### 9.2 map\(\)

map方法对数组的所有成员依次调用一个函数，根据函数结果返回一个新数组。

```js
var numbers = [1, 2, 3];

numbers.map(function (n) {
  return n + 1;
});
// [2, 3, 4]

numbers
// [1, 2, 3]
```

上面代码中，numbers数组的所有成员都加上1，组成一个新数组返回，原数组没有变化。

map方法接受一个函数作为参数。该函数调用时，map方法会将其传入三个参数，分别是当前成员、当前位置和数组本身。

```js
[1, 2, 3].map(function(elem, index, arr) {
  return elem * index;
});
// [0, 2, 6]
```

map方法不仅可以用于数组，还可以用于字符串，用来遍历字符串的每个字符。但是，不能直接使用，而要通过函数的call方法间接使用，或者先将字符串转为数组，然后使用。

```js
var upper = function (x) {
  return x.toUpperCase();
};

[].map.call('abc', upper)
// [ 'A', 'B', 'C' ]

// 或者
'abc'.split('').map(upper)
// [ 'A', 'B', 'C' ]
```

其他类似数组的对象（比如document.querySelectorAll方法返回DOM节点集合），也可以用上面的方法遍历。

map方法还可以接受第二个参数，表示回调函数执行时this所指向的对象。

```js
var arr = ['a', 'b', 'c'];

[1, 2].map(function(e){
  return this[e];
}, arr)
// ['b', 'c']
```

上面代码通过map方法的第二个参数，将回调函数内部的this对象，指向arr数组。

**如果数组有空位，map方法的回调函数在这个位置不会执行，会跳过数组的空位。**

```js
var f = function(n){ return n + 1 };

[1, undefined, 2].map(f) // [2, NaN, 3]
[1, null, 2].map(f) // [2, 1, 3]
[1, , 2].map(f) // [2, , 3]
```

上面代码中，map方法不会跳过undefined和null，但是会跳过空位。

```js
Array(2).map(function (){
  console.log('enter...');
  return 1;
})
// [, ,]
```

上面代码中，map方法根本没有执行，直接返回了Array\(2\)生成的空数组。

### 9.3 forEach\(\)

forEach方法与map方法很相似，也是遍历数组的所有成员，执行某种操作，但是forEach方法一般不返回值，只用来操作数据。如果需要有返回值，一般使用map方法。

forEach方法也可以接受第二个参数，用来绑定回调函数的this关键字。

```js
var out = [];

[1, 2, 3].forEach(function(elem) {
  this.push(elem * elem);
}, out);

out // [1, 4, 9]
```

上面代码中，空数组out是forEach方法的第二个参数，结果，回调函数内部的this关键字就指向out。这个参数对于多层this非常有用，因为多层this通常指向是不一致的。

```js
var obj = {
  name: '张三',
  times: [1, 2, 3],
  print: function () {
    this.times.forEach(function (n) {
      console.log(this.name);
    });
  }
};

obj.print()
// 没有任何输出
```

上面代码中，obj.print方法有两层this，它们的指向是不一致的。外层的this.times指向obj对象，内层的this.name指向顶层对象window。这显然是违背原意的，解决方法就是使用forEach方法的第二个参数固定this。

```js
var obj = {
  name: '张三',
  times: [1, 2, 3],
  print: function () {
    this.times.forEach(function (n) {
      console.log(this.name);
    }, this);
  }
};

obj.print()
// 张三
// 张三
// 张三
```

**注意，forEach方法无法中断执行，总是会将所有成员遍历完。如果希望符合某种条件时，就中断遍历，要使用for循环。**

forEach方法会跳过数组的空位。

forEach方法也可以用于类似数组的对象和字符串。

```js
var obj = {
  0: 1,
  a: 'hello',
  length: 1
}

Array.prototype.forEach.call(obj, function (elem, i) {
  console.log( i + ':' + elem);
});
// 0:1

var str = 'hello';
Array.prototype.forEach.call(str, function (elem, i) {
  console.log( i + ':' + elem);
});
// 0:h
// 1:e
// 2:l
// 3:l
// 4:o
```

### 9.4 filter\(\)

filter方法的参数是一个函数，所有数组成员依次执行该函数，返回结果为true的成员组成一个新数组返回。该方法不会改变原数组。

```js
[1, 2, 3, 4, 5].filter(function (elem) {
  return (elem > 3);
})
// [4, 5]
```

上面代码将大于3的原数组成员，作为一个新数组返回。

再看一个例子。

```js
var arr = [0, 1, 'a', false];

arr.filter(Boolean)
// [1, "a"]
```

上面例子中，通过filter方法，返回数组arr里面所有布尔值为true的成员。

filter方法还可以接受第二个参数，指定测试函数所在的上下文对象（即this对象）。

### 9.5 entries\(\)，keys\(\) 和 values\(\)

keys\(\)是对键名的遍历、values\(\)是对键值的遍历，entries\(\)是对键值对的遍历。

```js
for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"
```

如果不使用for...of循环，可以手动调用遍历器对象的next方法，进行遍历。

```js
let letter = ['a', 'b', 'c'];
let entries = letter.entries();
console.log(entries.next().value); // [0, 'a']
console.log(entries.next().value); // [1, 'b']
console.log(entries.next().value); // [2, 'c']
```

### 9.6 reduce\(\)，reduceRight\(\)

reduce方法和reduceRight方法依次处理数组的每个成员，最终累计为一个值。

它们的差别是，reduce是从左到右处理（从第一个成员到最后一个成员），reduceRight则是从右到左（从最后一个成员到第一个成员），其他完全一样。

这两个方法的第一个参数都是一个函数。该函数接受以下四个参数。

1. 累积变量，默认为数组的第一个成员
2. 当前变量，默认为数组的第二个成员
3. 当前位置（从0开始）
4. 原数组

这四个参数之中，只有前两个是必须的，后两个则是可选的。

下面的例子求数组成员之和。

```js
[1, 2, 3, 4, 5].reduce(function(x, y){
  console.log(x, y)
  return x + y;
});
// 1 2
// 3 3
// 6 4
// 10 5
//最后结果：15
```

上面代码中，第一轮执行，x是数组的第一个成员，y是数组的第二个成员。从第二轮开始，x为上一轮的返回值，y为当前数组成员，直到遍历完所有成员，返回最后一轮计算后的x。

利用reduce方法，可以写一个数组求和的sum方法。

```js
Array.prototype.sum = function (){
  return this.reduce(function (partial, value) {
    return partial + value;
  })
};

[3, 4, 5, 6, 10].sum()
// 28
```

如果要对累积变量指定初值，可以把它放在reduce方法和reduceRight方法的第二个参数。

```js
[1, 2, 3, 4, 5].reduce(function(x, y){
  return x + y;
}, 10);
// 25
```

上面代码指定参数x的初值为10，所以数组从10开始累加，最终结果为25。注意，这时y是从数组的第一个成员开始遍历。

第二个参数相当于设定了默认值，处理空数组时尤其有用。

```js
function add(prev, cur) {
  return prev + cur;
}

[].reduce(add)
// TypeError: Reduce of empty array with no initial value
[].reduce(add, 1)
// 1
```

上面代码中，由于空数组取不到初始值，reduce方法会报错。这时，加上第二个参数，就能保证总是会返回一个值。

下面是一个reduceRight方法的例子。

```js
function substract(prev, cur) {
  return prev - cur;
}

[3, 2, 1].reduce(substract) // 0
[3, 2, 1].reduceRight(substract) // -4
```

上面代码中，reduce方法相当于3减去2再减去1，reduceRight方法相当于1减去2再减去3。

由于reduce方法依次处理每个元素，所以实际上还可以用它来搜索某个元素。比如，下面代码是找出长度最长的数组元素。

```js
function findLongest(entries) {
  return entries.reduce(function (longest, entry) {
    return entry.length > longest.length ? entry : longest;
  }, '');
}

findLongest(['aaa', 'bb', 'c']) // "aaa"
```

## 十、链式使用

上面这些数组方法之中，有不少返回的还是数组，所以可以链式使用。

```js
var users = [
  {name: 'tom', email: 'tom@example.com'},
  {name: 'peter', email: 'peter@example.com'}
];

users
.map(function (user) {
  return user.email;
})
.filter(function (email) {
  return /^t/.test(email);
})
.forEach(alert);
// 弹出tom@example.com
```

上面代码中，先产生一个所有Email地址组成的数组，然后再过滤出以t开头的Email地址。

---

**参考资料**

* JavaScript高级程序设计（第三版）
* [JavaScript标准参考教程（alpha） - Array](http://javascript.ruanyifeng.com/stdlib/array.html#)
* [ECMAScript 6 入门 - 数组的扩展](http://es6.ruanyifeng.com/#docs/array#数组实例的-entries，keys-和-values)



