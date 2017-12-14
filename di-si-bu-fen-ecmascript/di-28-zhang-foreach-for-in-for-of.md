# 第28章 forEach、for-in、for-of

自从JavaScript5起，我们开始可以使用内置的forEach方法：

```js
myArray.forEach(function (value) {
  console.log(value);
});
```

写法简单了许多，但也有短处：你不能中断循环\(使用break语句或使用return语句。

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


