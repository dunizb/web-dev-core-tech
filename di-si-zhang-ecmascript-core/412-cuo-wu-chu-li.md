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



