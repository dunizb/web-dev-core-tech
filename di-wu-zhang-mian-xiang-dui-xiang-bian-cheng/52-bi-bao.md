# 第3节 闭包

## 一、概念

闭包的字面含义就是闭合，包起来，简单的来说，就是一个具有封闭功能与包裹功能的结构。所谓的闭包就是一个具有封闭的对外不公开的一种包裹的结构或空间。

函数就可以构成一个闭包。

**闭包就是一个具有封闭与包裹功能的结构，是为了实现具有私有访问空间的函数的。函数可以构成闭包是因为函数内部定义的数据函数外部无法访问，即函数具有封闭性；函数可以封装代码即具有包裹性，所以函数可以构成闭包**。

在接触一个新技术的时候，我首先会做的一件事就是找它的 demo。对于我们来说，看代码比自然语言更能理解一个事物的本质。其实，闭包无处不在，比如：jQuery、zepto的核心代码都包含在一个大的闭包中，所以下面我先写一个最简单最原始的闭包，以便让你在大脑里产生闭包的画面

例1：

```js
function A(){   
    function B(){   
       console.log("Hello Closure!");   
    }   
    return B;   
}   
var C = A();   
C();//Hello Closure!
```

这是最简单的闭包。

有了初步认识后，我们简单分析一下它和普通函数有什么不同，上面代码翻译成自然语言如下：

1. 定义普通函数 A
2. 在 A 中定义普通函数 B
3. 在 A 中返回 B
4. 执行 A, 并把 A 的返回结果赋值给变量 C
5. 执行 C 

把这5步操作总结成一句话就是：函数A的内部函数B被函数A外的一个变量 c 引用。

把这句话再加工一下就变成了**闭包的定义：当一个内部函数被其外部函数之外的变量引用时，就形成了一个闭包。**

因此，当你执行上述5步操作时，就已经定义了一个闭包！这就是闭包！

函数就可以构成闭包，要解决的问题就是如何访问到函数内部的数据

例2：

```js
function foo () {
    var num  = 123;
    return num;
}
var res = foo();
console.log( res );    // =>123
```

这里的确是访问到函数中的数据了。但是该数据不能第二次访问，因此第二次访问的时候又要调用一次foo，表示又有一个新的num = 123出来了。

在函数内的数据，不能直接在函数外部访问，那么在函数内如果定义一个函数，那么在这个函数内部中是可以直接访问的

例3：

```js
function foo() {
    var num = Math.random();
    function func() {
        return mun;
    }
    return func;
}
var f = foo();
// f 可以直接访问这个 num
var res1 = f();
var res2 = f();
```

我们使用前面学习的绘制作用域链结构图的方法来绘制闭包的作用域链结构图，如下：

## 二、闭包有什么用

闭包不允许外部访问，要解决的问题就是让外部间接访问函数内部的数据。事实上，通过使用闭包，我们可以做很多事情。比如模拟面向对象的代码风格；更优雅，更简洁的表达出代码；在某些方面提升代码的执行效率。利用闭包可以实现如下需求：

1. 匿名自执行函数。一个匿名的函数，并立即执行它，由于外部无法引用它内部的变量，因此在执行完后很快就会被释放，关键是这种机制不会污染全局对象。
2. 缓存。闭包正是可以做到这一点，因为它不会释放外部的引用，从而函数内部的值可以得以保留
3. 希望某些变量一直保存在内存中但又不会“污染”全局的变量

在了解闭包的作用之前，我们先了解一下 Javascript 中的GC机制：

> 在 Javascript 中，如果一个对象不再被引用，那么这个对象就会被 GC 回收，否则这个对象一直会保存在内存中。

在上述例子1中，B 定义在 A 中，因此 B 依赖于 A ,而外部变量 C 又引用了 B , 所以A间接的被 C 引用。

也就是说，A 不会被 GC 回收，会一直保存在内存中。为了证明我们的推理，上面的例子稍作改进：

例4：

```js
function A(){   
    var count = 0;   
    function B(){   
       count ++;   
       console.log(count);   
    }   
    return B;   
}   
var C = A();   
C();// 1   
C();// 2   
C();// 3
```

count 是函数A 中的一个变量，它的值在函数B 中被改变，函数B 每执行一次，count 的值就在原来的基础上累加 1 。因此，函数A中的 count 变量会一直保存在内存中。

**当我们需要在模块中定义一些变量，并希望这些变量一直保存在内存中但又不会“污染”全局的变量时，就可以用闭包来定义这个模块。**

## 三、闭包的基本模型

### 对象模式

函数内部定义个一个对象，对象中绑定多个函数（方法），返回对象，利用对象的方法访问函数内的数据

```js
function foo () {
    // 私有数据
    return {
         method : function(){
             // 操作私有数据
         }
    }
}
```

### 函数模式

函数内部定义一个新函数，返回新函数，用新函数获得函数内的数据

```js
function foo(){
    // 私有数据
    return function(){
         // 可以操作私有数据
    }
}
```

### 沙箱模式

**沙箱的概念**

沙盘与盒子，就可以在一个笑笑的空间内模拟显示世界，特点是执行效果与现实世界一模一样，但是在沙箱中模拟与现实无关.

沙箱模式就是一个自调用函数，代码写到函数中一样会执行，但是不会与外界有任何的影响

例如，在jQuery中

```js
(function () {
   var jQuery = function () { // 所有的算法 }
   // .... // .... jQuery.each = function () {}
   window.jQuery = window.$ = jQuery;
})();
$.each( ... )
```

### 带有缓存功能的函数

以 Fibonacci 数列为例，改进传统计算斐波那契数列方法

我们来回顾一下传统递归方式求斐波那契数列方法，我们定义一个count变量来查看递归了多少次：

```js
var count = 0;
function fibo( n ){
    count++;
    if( n ==0 || n == 1 ) return 1;
    return fibo( n - 1 ) + fibo( n - 2 );
}
fib1( 20 );
console.log( count1 );
// 5: 15
// 6: 25
// ...
// 20: 21891
```

当 n = 5 式，count = 15，当时当 n = 20 的时候，count就达到惊人的21891次，性能太低了

性能低的原因是 重复计算。如果每次将计算的结果存起来

* 那么每次需要的时候先看看有没有存储过该数据，如果有，直接拿来用。
* 如果没有再递归，但是计算的结果需要再次存储起来，以便下次使用

改进版：

```js
var data = [ 1, 1 ];
var count = 0;
function fibo( n ) {
    count++;
    var v = data[ n ];
    if( v === undefined ){
         v = fibo( n - 1 ) + fibo( n - 2 );
         data[ n ] = v;
    }
    return v;
}
fibo( 100 );
console.log( count );    // 199
```

改进之后， n = 100的时候也才199次，大大提高了性能。

## 四、闭包的性能问题

闭包的缺点就是常驻内存，会增大内存使用量，使用不当很容易造成内存泄露。

函数执行需要内存，那么函数中定义的变量，会在函数执行结束后自动回收，凡是因为闭包结构的，被引出的数据，如果还有变量引用这些数据的话，那么这些数据就不会被回收。

因此在使用闭包的时候如果不使用某些数据了，一定要赋值一个null

```js
var f = (function () {
    var num = 123;
    return function () {
        return num;
    };
})();
// f 引用着函数，函数引用着变量num
// 因此在不适用该数据的时候，最好写上
f = null;
```

## 五、闭包的高级写法

上面的写法其实是最原始的写法，而在实际应用中，会将闭包和匿名函数联系在一起使用。下面就是一个闭包常用的写法：

```js
(function(document){   
    var viewport;   
    var obj = {   
        init:function(id){   
           viewport = document.querySelector("#"+id);   
        },   
        addChild:function(child){   
            viewport.appendChild(child);   
        },   
        removeChild:function(child){   
            viewport.removeChild(child);   
        }   
    }   
    window.jView = obj;   
})(document);
```

这个组件的作用是：初始化一个容器，然后可以给这个容器添加子容器，也可以移除一个容器。

功能很简单，但这里涉及到了另外一个概念：立即执行函数。 简单了解一下就行，需要重点理解的是这种写法是如何实现闭包功能的。

可以将上面的代码拆分成两部分：\(function\(\){}\) 和 \(\) , 第1个\(\) 是一个表达式，而这个表达式本身是一个匿名函数，所以在这个表达式后面加 \(\) 就表示执行这个匿名函数。

因此这段代码执行执行过程可以分解如下：

```js
var f = function(document){   
    var viewport;   
    var obj = {   
        init:function(id){   
            viewport = document.querySelector("#"+id);   
        },   
        addChild:function(child){   
            viewport.appendChild(child);   
        },   
        removeChild:function(child){   
            viewport.removeChild(child);   
        }   
    }   
    window.jView = obj;   
};   
f(document);
```

在这段代码中似乎看到了闭包的影子，但 f 中没有任何返回值，似乎不具备闭包的条件，注意这句代码：

```js
window.jView = obj;
```

obj 是在函数 f 中定义的一个对象，这个对象中定义了一系列方法， 执行window.jView = obj 就是在 window 全局对象定义了一个变量 jView，并将这个变量指向 obj 对象，即全局变量 jView 引用了 obj . 而 obj 对象中的函数又引用了函数 f 中的变量 viewport ,因此函数 f 中的 viewport 不会被 GC 回收，viewport 会一直保存到内存中，所以这种写法满足了闭包的条件。

---

参考文章

* [让你分分钟理解 JavaScript 闭包](http://www.cnblogs.com/onepixel/p/5062456.html)
* [进击JavaScript之（三）玩转闭包](https://blog.dunizb.com/2016/08/28/进击JavaScript之（三）玩转闭包/)



