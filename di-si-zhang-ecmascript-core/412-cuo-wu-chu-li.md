# 第12节 错误处理

在执行JavaScript代码的时候，有些情况下会发生错误。

错误分两种，**一种是程序写的逻辑不对**，导致代码执行异常。例如：

```js
var s = null;
var len = s.length; // TypeError：null变量没有length属性
```

对于这种错误，要修复程序。

**一种是执行过程中，程序可能遇到无法预测的异常情况而报错**，例如，网络连接中断，读取不存在的文件，没有操作权限等。

对于这种错误，我们需要处理它，并可能需要给用户反馈。

错误处理是程序设计时必须要考虑的问题。对于C这样贴近系统底层的语言，错误是通过错误码返回的：

```c
int fd = open("/path/to/file", O_RDONLY);
if (fd == -1) {
    printf("Error when open file!");
} else {
    // TODO
}
```

通过错误码返回错误，就需要约定什么是正确的返回值，什么是错误的返回值。上面的open\(\)函数约定返回-1表示错误。

显然，这种用错误码表示错误在编写程序时十分不便。

因此，高级语言通常都提供了更抽象的错误处理逻辑try ... catch ... finally，JavaScript也不例外。

## 一、try ... catch ... finally

使用try ... catch ... finally处理错误时，我们编写的代码如下：

```
var r1, r2, s = null;
try {
    r1 = s.length; // 此处应产生错误
    r2 = 100; // 该语句不会执行
} catch (e) {
    console.log('出错了：' + e);
} finally {
    console.log('finally');
}
console.log('r1 = ' + r1); // r1应为undefined
console.log('r2 = ' + r2); // r2应为undefined

// 出错了：TypeError: s is null
// finally
// r1 = undefined
// r2 = undefined
```

运行后可以发现，输出提示类似“出错了：TypeError: Cannot read property 'length' of null”。

我们来分析一下使用try ... catch ... finally的执行流程。

当代码块被try { ... }包裹的时候，就表示这部分代码执行过程中可能会发生错误，一旦发生错误，就不再继续执行后续代码，转而跳到catch块。catch \(e\) { ... }包裹的代码就是错误处理代码，变量e表示捕获到的错误。**最后，无论有没有错误，finally一定会被执行。**

所以，有错误发生时，执行流程像这样：

1. 先执行try { ... }的代码；
2. 执行到出错的语句时，后续语句不再继续执行，转而执行catch \(e\) { ... }代码；
3. 最后执行finally { ... }代码。

而没有错误发生时，执行流程像这样：

1. 先执行try { ... }的代码；
2. 因为没有出错，catch \(e\) { ... }代码不会被执行；
3. 最后执行finally { ... }代码。

最后请注意，**catch和finally可以不必都出现**。也就是说，try语句一共有三种形式：

完整的try ... catch ... finally：

```js
try {
    ...
} catch (e) {
    ...
} finally {
    ...
}
```

只有try ... catch，没有finally：

```js
try {
    ...
} catch (e) {
    ...
}
```

只有try ... finally，没有catch：

```js
try {
    ...
} finally {
    ...
}
```













