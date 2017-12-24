# 第10节 Math.random\(\)

## 一、Math.random\(\)

Math.random\(\) 这个方法相信大家都知道，是用来生成随机数的。不过一般的参考手册时却没有说明如何用这个方法来生成指定范围内的随机数。这次我就来详细的介绍一下 Math.random\(\)，以及如何用它来生成制定范围内的随机数。

Math.random会提供给我们一个\[0,1\]之间的随机数，但是如果我们要\[1,10\]范围随机整数的话，可以使用以下三个函数：

* Math.round
* Math.ceil
* Math.floor

他们的区别，对于浮点数，round会遵守四舍五入规则，ceil无论如何贪心进位+1，floor无论如何都小心翼翼的自断一臂-1。

利用 parseInt\(\)、Math.floor\(\) 或者 Math.ceil\(\)进行四舍五入处理，我们看到，直接使用Math.random\(\)方法，生成的是一个小于1的数，所以：

```js
Math.random()*5
```

得到的结果是一个小于5的随机数。而我们通常希望得到的是 0 - 5 之间的整数，所以我们需要对得到的结果四舍五入处理一下，从而得到我们期望的整数。parseInt\(\)、Math.floor\(\)和Math.ceil\(\)都可以起到四舍五入的作用。

```js
var randomNum = Math.random()*5;
console.log(randomNum); // 2.9045290905811183 
console.log(parseInt(randomNum,10)); // 2
console.log(Math.floor(randomNum)); // 2
console.log(Math.ceil(randomNum)); // 3 
```

由测试的代码我们可以看到，parseInt\(\)和Math.floor\(\)的效果是一样的，都是向下取整数部分。所以 parseInt\(Math.random\(\)\*5,10\)和Math.floor\(Math.random\(\)\*5\)都是生成的0-4之间的随机 数，Math.ceil\(Math.random\(\)\*5\) 则是生成的 1-5 之间的随机数。

## 二、生成指定范围数值随机数

**生成1到任意值的随机数**

```js
// max - 期望的最大值
parseInt(Math.random()*max,10)+1;
Math.floor(Math.random()*max)+1;
Math.ceil(Math.random()*max);
```

**生成0到任意值的随机数**

```js
// max - 期望的最大值
parseInt(Math.random()*(max+1),10);
Math.floor(Math.random()*(max+1));
```

**生成任意值到任意值的随机数**

```js
// max - 期望的最大值
// min - 期望的最小值
parseInt(Math.random()*(max-min+1)+min,10);
Math.floor(Math.random()*(max-min+1)+min);
```

怎么样？现在应该很清楚如何去生成你需要随机数了吧？！希望看完这篇文章对你的开发有帮助！这次就到这里了！



















