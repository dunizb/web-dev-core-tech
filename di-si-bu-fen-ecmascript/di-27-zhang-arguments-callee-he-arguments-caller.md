# 第27章 arguments.callee和arguments.caller

在函数内部，有两个特殊的对象：arguments和this。

虽然**arguments**的主要用途是保存函数参数，但这个对象还有一个名叫**callee**的属性，该属性是一个指针，指向拥有这个arguments对象的函数。请看下面这个非常经典的阶乘函数。

```js
function factorial(num){
    if(num <= 1) {
        return 1;
    }else{
        return num * factorial(num - 1);
    }
}
```

定义阶乘函数一般都要用到递归算法；如上面的代码所示，在函数有名字，而且名字以后也不会变的情况下，这样定义没有问题。但问题是这个函数的执行与函数名factorial紧紧耦合在了一起。为了消除这种紧密耦合的现象，可以像下面这样使用arguments.callee。

```js
function factorial(num){
    if(num <= 1) {
        return 1;
    }else{
        return num * arguments.callee(num - 1);
    }
}
```

在这个重写后的factorial\(\)函数的函数体内，没有再引用函数名factorial。这样，无论引用函数时使用的是什么名字，都可以保证正常完成递归调用。

ECMAScript 5也规范化了另一个函数对象的属性：caller。除了Opera的早期版本不支持，其他浏览器都支持这个ECMAScript 3并没有定义的属性。这个属性中保存着调用当前函数的函数的引用，如果是在全局作用域中调用当前函数，它的值为null。例如：

