# 第1节 原型与原型链

这一节您将看到以下内容：

* 传统构造函数的问题
* 一些相关概念
* 认识原型
* 构造、原型、实例三角结构图
* 对象的原型链
* 函数的构造函数Function

一句话说明什么是原型：原型就是一个JavaScript对象，原型能存储我们的方法，构造函数创建出来的实例对象能够引用原型中的方法。

## 一、传统构造函数的问题

有如下代码

```js
function Foo(){
    this.sayHello = function(){
     }
}
```

由于对象是调用new Foo\(\)所创建出来的，因此每一个对象在创建的时候，函数sayHello都会呗创建一次

那么有没一个对象都含有一个独立的，不同的，但是功能逻辑一样的函数，比如：{} == {}。

在代码中方法就会消耗性能，最典型的资源就越是内存

这里最好的方法就是将函数放在构造函数之外，那么在构造函数中引用该函数即可

```js
function sayHello () {}
function Foo () {
    this.say = sayHello；
}
```

会在开发中变得困难：引入框架危险，代码繁冗不好维护。解决方法就是如果外面的函数不占用其名字，而且在函数名下。

每一个函数在定义的时候，有一个神秘对象（就是原型对象，暂且这么称呼）被创建出来。

每一个由构造函数创建的对象都会默认的连接到该神秘对象上。

```js
var f1 = new Foo();
var f2 = new Foo();
f1.sayHello();    //如果f1没有sayHello那么就会在Foo.prototype中去找
```

由构造函数创建出来的众多对象共享一个对象就是：**构造函数.prototype**

只需要将共享的东西，重复会多占用内存的东西放到**构造函数.prototype**中，那么所有的对象就可以共享了。

```js
function Foo(){}
Foo.prototype.sayHello = function(){
    console.log("….");
}
var f1 = new Foo();
f1.sayHello();
var f2 = new Foo();
f2.sayHello();
console.log(f1.sayHello === f2.sayHello); // true
```

## 二、一些相关概念

**类class**：在JS中就是构造函数

* 在传统的面向对象语言中，使用一个叫类的东西定义模板，然后使用模板创建对象。
* 在构造方法中也具有类似的功能，因此也称其为类

**实例（instance）与对象（object）**

* 实例一般是指某一个构造函数创建出来的对象，我们称为XXXX 构造函数的实例
* 实例就是对象。对象是一个泛称
* 实例与对象是一个近义词

**键值对与属性和方法**

* 在JS中键值对的集合称为对象
* 如果值为数据（非函数），就称该键值对为属性
* 如果值为函数（方法），就称该键值对为方法method

**父类与子类（基类和派生类）**

* 传统的面向对象语言中使用类来实现继承那么就有父类、子类的概念
* 父类又称为基类，子类又称为派生类
* 在JS中没有类的概念，在JS中常常称为父对象，子对象，基对象，派生对象。

## 三、认识原型

在JavaScript中，**原型也是一个对象，通过原型可以实现对象的属性继承**，JavaScript的对象中都包含了一个\[\[Prototype\]\]内部属性，这个属性所对应的就是该对象的原型。

\[\[Prototype\]\]作为对象的内部属性，是不能被直接访问的。所以为了方便查看一个对象的原型，Firefox和Chrome中提供了\_\_proto\_\_这个**非标准**（不是所有浏览器都支持）的访问器（ECMA引入了标准对象原型访问器"Object.getPrototype\(object\)"）。

下面通过一个例子来看看原型相关概念：

```js
function Person() {}
// 神秘对象就是Person.prototype
//那么只有使用构造函数才可以访问它
var o = new Person();
//以前不能直接使用o来访问神秘对象
//现在有了__proto__后，
o.__proto__也可以直接访问神秘对象
//那么o.__proto__ === Person.prototype
```

**神秘对象**（原型）中都有一个属性constructor，翻译为 构造器 。表示该原型是与什么构造函数联系起来的。

\_\_proto\_\_有什么用?

* 可以访问原型
* 由于在开发中除非特殊要求，不要使用实例去修改原型的成员，因此该属性开发时使用较少。
* 但是在调试过程中非常方便，可以轻易的访问原型进行查看成员

如果在早期的浏览器中使用实例需要访问原型如何处理？

可以使用实例对象访问构造器，然后使用构造器访问原型

```js
var o = new Person();
o.constructor.prototype
```

如果给实例继承自原型的属性赋值

```js
function Foo();
Foo.prototype.name = "test";
var o1 = new Foo();
var o2 = new Foo();
o1.name = "张三";    // 不是修改原型中的name而是自己增加了一个name属性
console.log(o1.name + '，'+ o2.name);    // 张三，test
```

## 四、构造、原型、实例三角结构图

对于如下代码：

```js
function Person(){}
var p = new Person()

console.log(Person.prototype.constructor); //function Person(){}
console.log(Person.prototype.constructor.name); //Person
console.log(typeof Person.prototype.constructor); //function

console.log(p.__prop__);
console.log(p.__prop__ === Person.prototype);//true
```

于是他们的关系图如下：

![](http://img.imooc.com/57d226440001674106910470.png)

## 五、对象的原型链

**凡是对象就有原型，原型也是对象。因此凡是给定一个对象，那么就可以找到他的原型，原型还有原型，那么如此下去，就构成一个对象的序列，称该结构为原型链。**

问题：

1. 原型链到底到什么时候是一个头？
2. 一个默认的原型链结构是怎样的？
3. 原型链结构对已知语法的修正

### 5.1 原型链的结构

凡是使用构造函数，创建出对象，并且没有利用赋值的方式修改原型，就说该对象保留默认的原型链。

默认原型链结构是什么样子呢？

```js
function Person(){}
var p = new Person();
//p 具有默认的原型链
```

默认的原型链结构就是：

```
当前对象 -> 构造函数.prototype -> Object.prototype -> null
```

![](/assets/__prop__6.png)

在实现继承的时候，有时候会利用替换原型链结构的方式实现原型继承，那么原型链结构就会发送改变

```js
function DunizbCollection(){}
DunizbCollection.prototype = [];
var arr = new DunizbCollection();
// arr -> [] -> Array.prototype -> Object.prototype -> null
```

### ![](/assets/__prop__7.png)

## 六、函数的构造函数Function

在JS中使用Function可以实例化函数对象 。也就是说在JS中函数与普通对象一样，也是一个对象类型。函数是JS中的一等公民。

1. 函数是对象，就可以使用对象的动态特性
2. 函数是对象，就有构造函数创建函数
3. 函数是对象，可以创建其它对象
4. 函数是唯一可以限定变量作用域的结果

要解决的问题

1. Function 如何使用
2. Function 与函数的关系
3. 函数的原型链结构

### 6.1 函数是Function的实例

语法

```js
new Function( arg0,arg1,arg1,….argN, body );
```

Function 中的参数全部是字符串

该构造函数的作用是将参数链接起来组成函数

* 如果参数只有一个，那么表示函数体
* 如果参数有多个，最后一个参数表示函数体，前面的所有参数表示函数的参数
* 如果没有参数，表示创建一个空函数

举例：创建一个打印一句话的函数

```js
// 传统的
function foo () {
    console.log( '你好' );
}
//Function
var func = new Function( 'console.log( "你好" );' );
// 功能上，这里foo 与 func 等价
```

在比如，创建一个空函数

```js
//传统
function foo () {}
//Function
var func = new Function();
func();
```

传入函数内一个数字，打印该函数

```js
//传统
function foo ( num ) {
    console.log( num );
}
//Function
var func = new Function( "num" ,"console.log( num )" );
func();
```

### 6.2 函数的原型链结构

任意的一个函数，都是相当于Function的实例，类似于{}与new Object\(\)的关系。

```js
function foo () {}
```

上面的代告诉解析器，有一个对象叫foo，它是一个函数；相当于new Function（）得到一个函数对象

* 函数应该有什么属性？答：\_\_proto\_\_
* 函数的构造函数是什么？答：Function
* 函数应该继承自Function.prototype
* Function.prototype继承自Object.prototype

对于Function，我们还必须知道

* Object函数是Function的一个实例

* Object作为对象是继承自Function.prototype的，又“Function.prototype”继承自Object.prototype

  ```js
  foo.prototype.__proto__ === Object.prototype // true
  ```

* Function是自己的构造函数

* 在JS 中任何对象的老祖宗就是Object.prototype

* 在JS中任何函数的老祖宗就是Function.prototype

下面绘制出 Function 的构造原型实例三角形结构

![](/assets/__prop__8.png)

### 6.3 为什么要使用Function？

Function是使用字符串构建函数，那么就可以在程序运行过程中构建函数.

以前的函数必须一开始就写好，再经过预解析，一步一步的运行

假定从服务器里拿到“\[1,2,3,4,5\]”，将数组形式的字符串转换成数组对象

```js
var arr = ( new Function( 'return ' + str + ' ;' ) )();
```

---

**参考资料**

* [进击JavaScript之（五）原型与继承](https://blog.dunizb.com/2016/09/19/进击JavaScript之（五）原型与继承/)



