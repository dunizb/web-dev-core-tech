# 第12节 变量声明提升

如下代码输出的结果是？

```js
var num = 123;
function foo1(){
    console.log( num );    //undefined
    var num = 456;
   console.log( num );    //456
}
foo1();
```

Javascript代码执行分为两个大步：

* 预解析的过程
* 代码的执行过程

## 一、预解析与变量声明提升

程序在执行过程中，会先将代码读取到内存中检查，会将所有的声明在此进行标记，所谓的标记就是让JS解析器知道有这个名字，后面在使用名字的时候不会出现未定义的错误。这个标记过程就是提升。

**声明**

名字的声明，标识符声明（变量名声明）

* 名字的声明就是让解析器知道有这个名字
* 名字没有任何数据与之对应

函数的声明，函数声明包含两部分，函数声明与函数表达式有区别，函数声明是单独写在一个结构中，不存在任何语句，逻辑判断等结构中

```js
function f() {
function func() {// 函数声明
} 
if ( true ) {
    function func2 () {} //函数表达式
}
var f = function func3 () {}; //函数表达式
this.sayHello = function () {}; //函数表达式
var i= 1；
function func4 () {}    // 函数声明
    var j = 2;
}
```

首先函数声明告诉解析器有这个名字存在，该阶段与名字声明一样

告诉解析器，这个名字对应的函数体是什么

```js
var num = 1;
function num () {
    alert( num );
}
num();    // 报错
```

**分析**

* 预解析代码，提示名字

  * 首先提升名字num
  * 再提升函数名，但是名字已经存在，因此只做第二部，让名字与函数体对应上
  * 结论就是 代码中已经有一个函数 num 了

* 开始执行代码，第一句话从赋值语句开始执行

  * 给num赋值为1
  * 覆盖了函数

* 调用num，由于num中存储的是数组1，因此报错

## 二、代码分析举例

**程序1**

```js
var num = 123;
function foo1(){
    console.log( num );    //undefined
    var num = 456;
    console.log( num );    //456
}
foo1();
```

**分析**

* 预解析，提升 num 名字和 foo1 函数
* 执行第一句话：num = 123;
* 执行函数调用
  * 函数调用进入函数的一瞬间也要进行预解析，此时解析的是变量名 num
  * 在函数内部是一个独立的空间，允许使用外部的数据，但是现在 num 声明同名，即覆盖外面的
  * 执行第一句 打印num，没有数据，undefined
  * 执行第二句 赋值：num = 456；
  * 执行第三句 打印num，结果456

**程序2**

```js
if ( ！ 'a' in window ) {
    var a = 123;
}
console.log( a ); //undefined
```

**分析**

* 预解析，读取提升a，有一个名字a 存在了
* in 运算符：判断某一个字符串描述的属性名是否在对象中
  * var o = { name:'jim' }; 'name' in o，'age' in o
  * 执行第一个判断：! 'a' in window
    * 'a' in window 结果为真
    * ！得到假
  * if内部的赋值不进行
* 打印结果 a 的值为 undefined

**程序3**

```js
if ( false ) {
    function f1 () {
        console.log( 'true' );
     }
} else {
    function f1 () {
        console.log( 'false' );
     }
}
f1();
```

**分析**

* 预解析：提升 f1 函数，只保留提升后的内容，所以打印是 false
* 执行代码，第一句话就是一个空的if结构
  ```js
  if ( true ) {
  } else {
  }
  ```

* 执行函数调用，得到 false

## 三、问题

如下代码

```js
function foo(){}
var foo = function(){};
```

上面的语法是声明，可以提升，因此在函数上方也可以调用

下面的语法是函数表达式，函数名就是foo ，他会提升，提升的不是函数体

函数表达式也是支持名字语法

```js
var foo = function func1 () {};
func();
```

函数有一个属性name，表示的是函数名，只有带有名字的函数定义，才会有name属性值，否则是“”

* 但是，函数表达式的名字，只允许在函数内部使用，IE8可以访问
* （）可以将数据转化为表达式

新的浏览器中，写在if、while、do..while结构中的函数，都会将函数的声明转换成特殊的函数表达式 

将代码

```js
if (…) {
    function foo () { … }
}
```

转换成

```js
if (…) {
    var foo = function foo () { …. }
}
```







