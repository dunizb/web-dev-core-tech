# 第1节 原型与原型链

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

每一个函数在定义的时候，有一个神秘对象（暂且这么称呼）被创建出来。

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

jsabjashdas





---

**参考资料**

* [进击JavaScript之（五）原型与继承](https://blog.dunizb.com/2016/09/19/进击JavaScript之（五）原型与继承/)



