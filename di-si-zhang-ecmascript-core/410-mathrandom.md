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

## 三、JS生成指定长度的随机字符串码

算是灵活运用随机数了

方法一

```js
function randomStrCode(len) {
    var d,
        e,
        b = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789",
        c = "";
        for (d = 0; len > d; d += 1){
            e = Math.random() * b.length, e = Math.floor(e), c += b.charAt(e);
        }
    return c.toLocaleUpperCase();
}
document.write(randomStrCode(4)); //3DF2
```

方法二

```js
function randomString(n) {  
  let str = 'abcdefghijklmnopqrstuvwxyz9876543210';
  let tmp = '',
      i = 0,
      l = str.length;
  for (i = 0; i < n; i++) {
    tmp += str.charAt(Math.floor(Math.random() * l));
  }
  return tmp;
}
document.write(randomString(4)); //3DF2
```

## 四、JS生成UUID

算法一

```js
function generateUUID() {
    var d = new Date().getTime();
    var uuid = 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g,function(c){
        var r = (d + Math.random()*16)%16 | 0;
        d = Math.floor(d/16);
        return (c=='x' ? r : (r&0x3|0x8)).toString(16);
    });
    return uuid;
};
```

这个方案下的碰撞率不及1/2^^122

算法二

```js
function guid() {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    var r = Math.random()*16|0, v = c == 'x' ? r : (r&0x3|0x8);
    return v.toString(16);
  });
}
```

算法三

```js
function guid() {
    function S4() {
        return (((1+Math.random())*0x10000)|0).toString(16).substring(1);
    }
    return (S4()+S4()+"-"+S4()+"-"+S4()+"-"+S4()+"-"+S4()+S4()+S4());
}
```

算法四

```js
function uuid(len, radix) {
  var chars = '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz'.split('');
  var uuid = [], i;
  radix = radix || chars.length;
  
  if (len) {
   // Compact form
   for (i = 0; i < len; i++) uuid[i] = chars[0 | Math.random()*radix];
  } else {
   // rfc4122, version 4 form
   var r;
  
   // rfc4122 requires these characters
   uuid[8] = uuid[13] = uuid[18] = uuid[23] = '-';
   uuid[14] = '4';
  
   // Fill in random data. At i==19 set the high bits of clock sequence as
   // per rfc4122, sec. 4.1.5
   for (i = 0; i < 36; i++) {
    if (!uuid[i]) {
     r = 0 | Math.random()*16;
     uuid[i] = chars[(i == 19) ? (r & 0x3) | 0x8 : r];
    }
   }
  }
  
  return uuid.join('');
}
```

这个可以指定长度和基数。比如

```js
// 8 character ID (base=2)
uuid(8, 2) // "01001010"
// 8 character ID (base=10)
uuid(8, 10) // "47473046"
// 8 character ID (base=16)
uuid(8, 16) // "098F4D35"
```

算法五

```js
function uuid() {
  var s = [];
  var hexDigits = "0123456789abcdef";
  for (var i = 0; i < 36; i++) {
    s[i] = hexDigits.substr(Math.floor(Math.random() * 0x10), 1);
  }
  s[14] = "4"; // bits 12-15 of the time_hi_and_version field to 0010
  s[19] = hexDigits.substr((s[19] & 0x3) | 0x8, 1); // bits 6-7 of the clock_seq_hi_and_reserved to 01
  s[8] = s[13] = s[18] = s[23] = "-";
  
  var uuid = s.join("");
  return uuid;
}
```

当然了，个人还是推荐算法一的，小伙伴们可以根据自己的需求来进行选择。

---

**推荐资料**

* 苏羊洋的博客 - [Math.random\(\)随机数的二三事](https://soulteary.com/2014/07/05/js-math-random-trick.html)
* 淘宝FED - [Math.random\(\) 二三事](http://taobaofed.org/blog/2015/12/07/some-thing-about-random/)
* 凹凸实验室 - [JavaScript中Math.random的种子设定方法](https://aotu.io/notes/2016/04/14/math-random/index.html)



