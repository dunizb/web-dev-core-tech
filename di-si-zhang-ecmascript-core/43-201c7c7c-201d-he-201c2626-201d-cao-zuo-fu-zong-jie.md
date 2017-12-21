# 第3节 “\|\|”和“&&”操作符总结

&&和\|\|操作符链接的两个值最后取哪个值的问题，有点模糊和不好理解，比如下面的表达式输出什么？如果你能答对说明你对这个问题就掌握了没什么问题。

```js
var val1 = 123 && 234; 
var val2 = 0 && 1;
var val3 = 1 && 0;
var val4 = 1 && ""; 
var val5 = "" && 1; 
var val6 = "" && 0; 
var val7 = 0 && "";
```

&&和\|\|操作符两边不是布尔类型时，系统会转换成布尔类型值再计算\(空字符串、null、0都会被转成false\)，结果本身不变。上述表达式的结果为：

```js
var val1 = 123 && 234;    //234
var val2 = 0 && 1;    //0
var val3 = 1 && 0;    //0
var val4 = 1 && "";    //""
var val5 = "" && 1;    //""
var val6 = "" && 0;    //""
var val7 = 0 && "";    //0
```

你都答对了吗？

**&&操作符总结：只要一个false就取false的值，都是true取后面，都是false取前面。**

**助记：一F即F取F，都F取前。**

\|\|操作符跟&&操作符相反，那么如下表示式 的结果是什么？

```js
var val1 = 1 || 2;    //1
var val2 = 0 || 1;    //1
var val3 = 1 || 0;    //1
var val4 = 1 || "";    //1
var val5 = 0 || "";    //""
var val6 = "" || 0;    //0
var val7 = 0 || "";    //""
```

**\|\|操作符总结：只要一个是true就取true的值，都是true取前面，都是false取后面。**

这个两个操作符需要注意的是，只有一边是false和true的情况，和都是false或true的情况。

这个连个操作符在DOM编程中经常使用，比如：

```js
var obj = document.body.scrollTop  ||  document.documentElement.scrollTop;
```

只需要记住其中一个操作符的特点即可。

