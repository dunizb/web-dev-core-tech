# 第4节 继承

JS面向对象知识中，继承是比较难比较抽象的一块内容，而且实现继承有很多种方法，每种方法又各有优缺点，更加的让人奔溃，这需要对面向对象知识中的对象、原型、原型链、构造函数等基础知识掌握透彻，否则《JS高程》里第六章继承也是看不明白的，网上也有N多的文章，看了这么多对继承依然不是很明白，所谓懂得不少道理但依然过不好这一生。

下面我结合自己的理解，和参考了《JS高程》和网上文章，总结一下实现继承的几种方法及优缺点，这篇文章适合出去面试前速记。

## 一、ES5继承方式

### 1.1 借用构造函数继承

```js
function Parent0(){
    this.name = "parent0";
    this.colors = ["red","blue","yellow"];
}
function Child0(){
    Parent0.call( this ); // 或apply
    this.type = "child0";
}
```

第6行，在子类（Child0）中执行父类（Parent0）的构造函数，通过这种调用，把父类构造函数的this指向为子类实例化对象引用，从而导致父类执行的时候父类里面的属性都会被挂载到子类的实例上去。

```js
new Child0().name; // Parent0
new Child0().colors; // (3) ["red", "blue", "yellow"]
```

但是通过这种方式，父类原型上的东西是没法继承的，因此函数复用也就无从谈起

```js
Parent0.prototype.sex = "男";
Parent0.prototype.say = function() {
    console.log(" Oh,My God! ");
}
new Child0().sex; // undefined
// Uncaught TypeError: (intermediate value).say is not a function
new Child0().say();
```

**缺点：**Child1无法继承Parent1的原型对象，并没有真正的实现继承（部分继承）

### 1.2 原型链式继承（借用原型链实现继承）

```js
function Parent1(){
    this.name = "parent1";
    this.colors = ["red","blue","yellow"];
}
function Child1(){
    this.name = "child1";
}
Child1.prototype = new Parent1();
```

这种方式能否解决借用构造函数继承的缺点呢？来看下面代码，我们依然为父类的原型添加sex属性和say方法：

```js
Parent1.prototype.sex = "男";
Parent1.prototype.say = function() {
    console.log(" Oh,My God! ");
}

new Child1().sex; //  男
new Child1().say(); // Oh,My God!
```

这种方式确实解决了上面借用构造函数继承方式的缺点。

但是，这种方式仍有缺点，我们来看如下代码：

```js
var s1 = new Child1();
s1.colors.push("black");
var s2 = new Child1();

s1.colors; // (4) ["red", "blue", "yellow", "balck"]
s2.colors; // (4) ["red", "blue", "yellow", "balck"]
```

我们实例化了两个Child1，在实例s1中为父类的colors属性push了一个颜色，但是s2也被跟着改变了。造成这种现象的原因就是原型链上中的原型对象它俩是共用的。

这不是我们想要的，s1和s2这个两个对象应该是隔离的，这是这种继承方式的缺点。

### 1.3 组合式继承

这里所谓的组合是指组合借用构造函数和原型链继承两种方式。

```js
function Parent2(){
    this.name = "parent2";
    this.colors = ["red","blue","yellow"];
}
function Child2(){
    Parent2.call(this);
    this.type = "child2";
}
Child2.prototype = new Parent2()
```

注意第6，9行，这种方式结合了借用构造函数继承和原型链继承的有点，能否解决上述两个实例对象没有被隔离的问题呢？

```js
var s1 = new Child2();
s1.colors.push("black");
var s2 = new Child2();

s1.colors; // (4) ["red", "blue", "yellow", "balck"]
s2.colors; // (3) ["red", "blue", "yellow"]
```

可以看到，s2和s1两个实例对象已经被隔离了。

但这种方式仍有缺点。父类的构造函数被执行了两次，第一次是Child2.prototype = new Parent2\(\)，第二次是在实例化的时候，这是没有必要的。

### 1.4 组合式继承优化1

直接把父类的原型对象赋给子类的原型对象

```js
function Parent3(){
    this.name = "parent3";
    this.colors = ["red","blue","yellow"];
}
Parent3.prototype.sex = "男";
Parent3.prototype.say = function(){console.log("Oh, My God！")}

function Child3(){
    Parent3.call(this);
    this.type = "child3";
}
Child3.prototype = Parent3.prototype;

var s1 = new Child3();
var s2 = new Child3();
console.log(s1, s2);
```

![](/assets/import-prototype.png)我们来看如下代码：

```js
console.log(s1 instanceof Child3); // true
console.log(s1 instanceof Parent3); // true
```

可以看到，我们无法区分实例对象s1到底是由Child3直接实例化的还是Parent3直接实例化的。用instanceof关键字来判断是否是某个对象的实例就基本无效了。

我们还可以用 .constructor 来观察对象是不是某个类的实例：

```js
console.log(s1.constructor.name); // Parent3
```

从这里可以看到，s1的构造函数居然是父类，而不是子类Child3，这显然不是我们想要的。

### 1.5 组合式继承优化2

这是继承的最完美方式

```js
function Parent4(){
    this.name = "parent4";
    this.colors = ["red","blue","yellow"];
}
Parent4.prototype.sex = "男";
Parent4.prototype.say = function(){console.log("Oh, My God！")}
function Child4(){
    Parent4.call(this);
    this.type = "child4";
}
Child4.prototype = Object.create(Parent4.prototype)；
Child4.prototype.constructor = Child4;
```

Object.create是一种创建对象的方式，它会创建一个中间对象

```js
var p = {name: "p"}
var obj = Object.create(p)
// Object.create({ name: "p" })
```

通过这种方式创建对象，新创建的对象obj的原型就是p，同时obj也拥有了属性name，这个新创建的中间对象的原型对象就是它的参数。

这种方式解决了上面的所有问题，是继承的最完美实现方式。

## 二、ES6方式继承

Class 可以通过extends关键字实现继承，这比 ES5 的通过修改原型链实现继承，要清晰和方便很多。

> 关于ES6 class 详细参看第8节内容

ES6继承代码：

```js
class Parent {
}
class Child1 extends Parent {
    constructor(x, y, colors) {
         super(x, y); // 调用父类的constructor(x, y)
         this.colors = colors;
    }
    toString() {
         return this.colors + ' ' + super.toString(); // 调用父类的toString()
    }
}
```

上面代码中，constructor方法和toString方法之中，都出现了super关键字，它在这里表示父类的构造函数，用来新建父类的this对象。

子类必须在constructor方法中调用super方法，否则新建实例时会报错。如果子类没有定义constructor方法，这个方法会被默认添加，不管有没有显式定义，任何一个子类都有constructor方法。

ES5 的继承，实质是先创造子类的实例对象this，然后再将父类的方法添加到this上面（Parent.apply\(this\)）。ES6 的继承机制完全不同，实质是先创造父类的实例对象this（所以必须先调用super方法），然后再用子类的构造函数修改this。

