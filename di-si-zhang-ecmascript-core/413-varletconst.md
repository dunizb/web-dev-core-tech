# 第14节 var、let、const

## 一、var

JavaScript脚本都可以省略var。

但是，某些时候，日过我们要强调是新声明了一个变量，那就必须使用var了。

用var和不用var的区别：

* 加var: 重新定义一个新的变量。 
* 不加var: 代表赋值，它会判断如果这个变量存在就赋值，不存在就定义一个新变量\(而且是window的属性\(全局的\)\). 
* 变量搜索范围：首先看当前函数体内有没有局部变量，没有得话就到window中找，如果window中也没有就返回undefined

> 特别注意：在一个函数体内定义一个变量，那它的作用域对整个函数体有效！

## 二、let是更完美的var

在ES6之前的时代，JavaScript存在两个问题

1. JS没有块级作用域
2. 循环内变量过度共享

比如如下经典场景：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
    <script type="text/javascript">
        //面试经典问题:

        function onMyLoad(){
            /*
            抛出问题:
                此题的目的是想每次点击对应目标时弹出对应的数字下标 0~4,但实际是无论点击哪个目标都会弹出数字5
            问题所在:
                arr 中的每一项的 onclick 均为一个函数实例(Function 对象),这个函数实例也产生了一个闭包域,
                这个闭包域引用了外部闭包域的变量,其 function scope 的 closure 对象有个名为 i 的引用,
                外部闭包域的私有变量内容发生变化,内部闭包域得到的值自然会发生改变
            */
            var arr = document.getElementsByTagName("p");
            for(var i = 0; i < arr.length;i++){
                arr[i].onclick = function(){
                    alert(i);
                }
            }
        }
    </script>
</head>
<body onload="onMyLoad()">
    <p>产品一</p>
    <p>产品二</p>
    <p>产品三</p>
    <p>产品四</p>
    <p>产品五</p>
</body>
</html>
```

解决办法一：增加若干个对应的闭包域空间\(这里采用的是匿名函数\),专门用来存储原先需要引用的内容\(下标\),不过只限于基本类型\(基本类型值传递,对象类型引用传递\)

```js
for(var i = 0;i<arr.length;i++){

    //声明一个匿名函数,若传进来的是基本类型则为值传递,故不会对实参产生影响,
    //该函数对象有一个本地私有变量arg(形参) ,该函数的 function scope 的 closure 对象属性有两个引用,一个是 arr,一个是 i
    //尽管引用 i 的值随外部改变 ,但本地私有变量(形参) arg 不会受影响,其值在一开始被调用的时候就决定了.
    (function (arg) {
        arr[i].onclick = function () {  //onclick函数实例的 function scope 的 closure 对象属性有一个引用 arg,
            alert(arg);                 //只要 外部空间的 arg 不变,这里的引用值当然不会改变
        }
    })(i);                              //立刻执行该匿名函数,传递下标 i(实参)
}
```

解决办法二：将下标作为对象属性\(name:"i",value:i的值\)添加到每个数组项\(p对象\)中

```js
for(var i = 0;i<arr.length;i++){
    //为当前数组项即当前 p 对象添加一个名为 i 的属性,值为循环体的 i 变量的值,
    //此时当前 p 对象的 i 属性并不是对循环体的 i 变量的引用,而是一个独立p 对象的属性,属性值在声明的时候就确定了
    //(基本类型的值都是存在栈中的,当有一个基本类型变量声明其等于另一个基本变量时,此时并不是两个基本类型变量都指向一个值,而是各自有各自的值,但值是相等的)
    arr[i].i = i;
    arr[i].onclick = function () {
        alert(this.i);
    }
}
```

> 更多解决方法请参看：[ 用9种办法解决 JS 闭包经典面试题之 for 循环取 i](http://blog.csdn.net/u013243347/article/details/52134643)

let相比var的优点如下：

* let声明的变量拥有块级作用域,let声明仍然保留了提升的特性，但不会盲目提升。
* let声明的全局变量不是全局对象的属性。不可以通过window.变量名的方式访问
* 形如for \(let x…\)的循环在每次迭代时都为x创建新的绑定
* let声明的变量直到控制流到达该变量被定义的代码行时才会被装载，所以在到达之前使用该变量会触发错误。
* 用let重定义变量会抛出一个语法错误（SyntaxError）

在那些不同之外，let和var几乎很相似了。举个例子，它们都支持使用逗号分隔声明多重变量，它们也都支持解构特性。

> 注意，class类声明的行为与var不同而与let一致。如果你加载一段包含同名类的脚本，后定义的类会抛出重定义错误。

## 三、const

const声明的变量与let声明的变量类似，它们的不同之处在于，const声明的变量只可以在声明时赋值，不可随意修改，否则会导致SyntaxError（语法错误）。

const更多是用来定义一个常量值，不可以重新赋值，但是如果值是一个对象，可以改变对象里的属性值

```js
const OBJ = {"a":1, "b":2};
OBJ.a = 3;
OBJ = {};// 重新赋值，报错！
console.log(OBJ.a); // 3
```



