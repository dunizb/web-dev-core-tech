# 第7节 创建对象的八种方式比较

JavaScript中没有类的概念，所以其在对象创建方面与面向对象语言有所不同。

JS中对象可以定义为”无序属性的集合”。其属性可以包含基本值，对象以及函数。对象实质上就是一组没有特定顺序的值，对象中每个属性、方法都有一个名字，每个名字都映射到了一个值，因此我们可以将对象想象称为一个散列表。

JS是一种基于对象的语言，对象的概念在JS体系中十分的重要，因此有必要清楚地了解一下JS中对象创建的常用方法及各自的局限性。

* 1.使用Object或对象字面量创建对象
* 2.工厂模式创建对象
* 3.构造函数模式创建对象
* 4.原型模式创建对象
* 5.构造与原型混合模式创建对象
* 6.寄生构造函数模式
* 7.稳妥构造函数模式
* 8.Object.create\(\)
* 总结几种方式的优缺点

## 一、使用Object或对象字面量创建对象

在说工厂模式创建对象之前，我们不妨回顾一下JS中最基本的创建对象的方法，比如说我想创建一个student对象怎么办？最简单地，new一个Object：

```js
var student = new Object();
student.name = "easy";
student.age = "20";
```

这样，一个 student 对象就创建完毕，拥有 2 个属性 name 以及 age，分别赋值为"easy"和20。

如果你嫌这种方法有一种封装性不良的感觉，我们也可以使用对象字面量的方式来创建student对象：

```js
var sutdent = {
  name : "easy",
  age : 20
};
```

这样看起来似乎就完美了。但是马上我们就会发现一个十分尖锐的问题：当我们要创建同类的student1，student2，…，studentn时，我们不得不将以上的代码重复 n 次。

```js
var sutdent1 = {
  name : "easy1",
  age : 20
};

var sutdent2 = {
  name : "easy2",
  age : 20
};

...

var sutdentn = {
  name : "easyn",
  age : 20
};
```

能不能像工厂车间那样，有一个车床就不断生产出对象呢？我们看”工厂模式”。

## 二、工厂模式创建对象

JS中没有类的概念，那么我们不妨就使用一种函数将以上对象创建过程封装起来以便于重复调用，同时可以给出特定接口来初始化对象：

```js
function createStudent(name, age) {
  var obj = new Object();
  obj.name = name;
  obj.age = age;
  return obj;
}

var student1 = createStudent("easy1", 20);
var student2 = createStudent("easy2", 20);
...
var studentn = createStudent("easyn", 20);
```

这样一来我们就可以通过createStudent函数源源不断地”生产”对象了。看起来已经高枕无忧了，但贪婪的人类总有不满足于现状的天性：我们不仅希望”产品”的生产可以像工厂车间一般源源不断，我们还想知道生产的产品究竟是哪一种类型的。

比如说，我们同时又定义了”生产”水果对象的createFruit\(\)函数：

```js
function createFruit(name, color) {
  var obj = new Object();
  obj.name = name;
  obj.color = color;
  return obj;
}

var v1 = createStudent("easy1", 20);
var v2 = createFruit("apple", "green");
```

对于以上代码创建的对象v1、v2，我们用 instanceof 操作符去检测，他们统统都是Object类型。我们的当然不满足于此，我们希望v1是Student类型的，而v2是Fruit类型的。为了实现这个目标，我们可以用自定义构造函数的方法来创建对象。

## 三、构造函数模式创建对象

在上面创建Object这样的原生对象的时候，我们就使用过其构造函数：

```js
var obj = new Object();
```

在创建原生数组Array类型对象时也使用过其构造函数：

```js
var arr = new Array(10);  //构造一个初始长度为10的数组对象
```

在进行自定义构造函数创建对象之前，我们首先了解一下**构造函数**和**普通函数**有什么区别。

其一，实际上并不存在创建构造函数的特殊语法，其与普通函数唯一的区别在于调用方法。对于任意函数，使用new操作符调用，那么它就是构造函数；不使用new操作符调用，那么它就是普通函数。

其二，按照惯例，我们约定构造函数名以大写字母开头，普通函数以小写字母开头，这样有利于显性区分二者。例如上面的new Array\(\)，new Object\(\)。

其三，使用new操作符调用构造函数时，会经历

* 1.创建一个新对象；
* 2.将构造函数作用域赋给新对象（使this指向该新对象）；
* 3.执行构造函数代码；
* 4.返回新对象；

了解了构造函数和普通函数的区别之后，我们使用构造函数将工厂模式的函数重写，并添加一个方法属性：

```js
function Student(name, age) {
  this.name = name;
  this.age = age;
  this.alertName = function(){
    alert(this.name)
  };
}

function Fruit(name, color) {
  this.name = name;
  this.color = color;
  this.alertName = function(){
    alert(this.name)
  };
}
```

这样我们再分别创建Student和Fruit的对象：

```js
var v1 = new Student("easy", 20);
var v2 = new Fruit("apple", "green");
```

这时我们再来用instanceof操作符来检测以上对象类型就可以区分出Student以及Fruit了：

```js
alert(v1 instanceof Student);  //true
alert(v2 instanceof Student);  //false
alert(v1 instanceof Fruit);  //false
alert(v2 instanceof Fruit);  //true

alert(v1 instanceof Object);  //true 任何对象均继承自Object
alert(v2 instanceof Object);  //true 任何对象均继承自Object
```

这样我们就解决了工厂模式无法区分对象类型的尴尬。那么使用构造方法来创建对象是否已经完美了呢？

我们知道在JS中，函数是对象。那么，当我们实例化不止一个Student对象的时候：

```js
var v1 = new Student("easy1", 20);
var v2 = new Student("easy2", 20);
...
var vn = new Student("easyn", 20);
```

其中共同的alertName\(\)函数也被实例化了n次，我们可以用以下方法来检测不同的Student对象并不共用alertName\(\)函数：

```js
alert(v1.alertName == v2.alertName);  //flase
```

这无疑是一种内存的浪费。我们知道，this对象是在运行时基于函数的执行环境进行绑定的。在全局函数中，this对象等同于window；在对象方法中，this指向该对象。在上面的构造函数中：

```js
this.alertName = function(){
    alert(this.name)
};
```

我们在创建对象（执行alertName函数之前）时，就将alertName\(\)函数绑定在了该对象上。我们完全可以在执行该函数的时候再这样做，办法是将对象方法移到构造函数外部：

```js
function Student(name, age) {
  this.name = name;
  this.age = age;
  this.alertName = alertName;
}

function alertName() {
  alert(this.name);
}

var stu1 = new Student("easy1", 20);
var stu2 = new Student("easy2", 20);
```

在调用"stu1.alert\(\)"时，this对象才被绑定到stu1上。

我们通过将alertName\(\)函数定义为全局函数，这样对象中的alertName属性则被设置为指向该全局函数的指针。由此stu1和stu2共享了该全局函数，解决了内存浪费的问题。

但是，通过全局函数的方式解决对象内部共享的问题，终究不像一个好的解决方法。如果这样定义的全局函数多了，我们想要将自定义对象封装的初衷便几乎无法实现了。更好的方案是通过原型对象模式来解决。

## 四、原型模式创建对象

原型模式创建对象又分为如下几种：

* 函数的原型对象
* 对象实例和原型对象的关联
* 使用原型模型创建对象
* 原型模型创建对象的局限性

### 4.1 函数的原型对象

在了解如何使用原型模式创建对象之前，有必要先搞清楚什么是原型对象。

我们创建的每一个函数都有一个prototype属性，该属性是一个指针，该指针指向了一个对象。对于我们创建的构造函数，该对象中包含可以由所有实例共享的属性和方法。如下如所示：

![](http://7xroh5.com1.z0.glb.clouddn.com/jsPrototype-pic1.png)

在默认情况下，所有原型对象会自动包含一个constructor属性，该属性也是一个指针，指向prototype所在的函数：

![](http://7xroh5.com1.z0.glb.clouddn.com/jsPrototype-pic2.png)

### 4.2 对象实例和原型对象的关联

在调用构造函数创建新的实例时，该实例的内部会自动包含一个\[\[Prototype\]\]指针属性，该指针指便指向构造函数的原型对象。注意，这个指针关联的是实例与构造函数的原型对象而不是实例与构造函数：

![](http://7xroh5.com1.z0.glb.clouddn.com/jsPrototype-pic3.png)

### 4.3 使用原型模型创建对象

**直接在原型对象中添加属性和方法**

了解了原型对象之后，我们便可以通过在构造函数原型对象中添加属性和方法来实现对象间数据的共享了。例如：

```js
function Student() {
}

Student.prototype.name = "easy";
Student.prototype.age = 20;
Student.prototype.alertName = function(){
                                alert(this.name);
                              };

var stu1 = new Student();
var stu2 = new Student();

stu1.alertName();  //easy
stu2.alertName();  //easy

alert(stu1.alertName == stu2.alertName);  //true 二者共享同一函数
```

以上代码，我们在Student的protptype对象中添加了name、age属性以及alertName\(\)方法。但创建的stu1和stu2中并不包含name、age属性以及alertName\(\)方法，而只包含一个\[\[prototype\]\]指针属性。当我们调用stu1.name或stu1.alertName\(\)时，是如何找到对应的属性和方法的呢？

> 当我们需要读取对象的某个属性时，都会执行一次搜索。首先在该对象中查找该属性，若找到，返回该属性值；否则，到\[\[prototype\]\]指向的原型对象中继续查找。

由此我们也可以看出另外一层意思：如果对象实例中包含和原型对象中同名的属性或方法，则对象实例中的该同名属性或方法会屏蔽原型对象中的同名属性或方法。原因就是“首先在该对象中查找该属性，若找到，返回该属性值；”

拥有同名实例属性或方法的示意图：

![](http://7xroh5.com1.z0.glb.clouddn.com/jsPrototype-pic4.png)

上图中，我们在访问stu1.name是会得到”EasySir”：

```js
alert(stu1.name);  //EasySir
```

**通过对象字面量重写原型对象**

很多时候，我们为了书写的方便以及直观上的”封装性”，我们往往采用对象字面量直接重写整个原型对象：

```js
function Student() {
}

Student.prototype = {
  constructor : Student,
  name : "easy",
  age : 20,
  alertName : function() {
    alert(this.name);
  }
}；
```

要特别注意，我们这里相当于用对象字面量重新创建了一个Object对象，然后使Student的prototype指针指向该对象。该对象在创建的过程中，自动获得了新的constructor属性，该属性指向Object的构造函数。因此，我们在以上代码中，增加了constructor : Student使其重新指回Student构造函数。

**原型模型创建对象的局限性**

原型模型在对象实例共享数据方面给我们带来了很大的便利，但通常情况下不同的实例会希望拥有属于自己单独的属性。我们将构造函数模型和原型模型结合使用即可兼得数据共享和”不共享”。

## 五、构造与原型混合模式创建对象

我们结合原型模式在共享方法属性以及构造函数模式在实例方法属性方面的优势，使用以下的方法创建对象：

```js
//我们希望每个stu拥有属于自己的name和age属性
function Student(name, age) {
  this.name = name;
  this.age = age;
}

//所有的stu应该共享一个alertName()方法
Student.prototype = {
  constructor : Student,
  alertName : function() {
     alert(this.name);
  }
}

var stu1 = new Student("Jim", 20);
var stu2 = new Student("Tom", 21);

stu1.alertName();  //Jim  实例属性
stu2.alertName();  //Tom  实例属性

alert(stu1.alertName == stu2.alertName);  //true  共享函数
```

以上，在构造函数中定义实例属性，在原型中定义共享属性的模式，是目前使用最广泛的方式。通常情况下，我们都会默认使用这种方式来定义引用类型变量。

## 六、寄生构造函数模式

这种模式的基本思想就是创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后再返回新建的对象

```js
function Person(name, job) {
  var o = new Object()
  o.name = name
  o.job = job
  o.sayName = function() {
    console.log(this.name)
  }
  return o
}
var person1 = new Person('Jiang', 'student')
person1.sayName()
```

这个模式，除了使用`new`操作符并把使用的包装函数叫做构造函数之外，和工厂模式几乎一样

构造函数如果不返回对象，默认也会返回一个新的对象，通过在构造函数的末尾添加一个`return`语句，可以重写调用构造函数时返回的值

## 七、稳妥构造函数模式

首先明白稳妥对象指的是没有公共属性，而且其方法也不引用`this`。

稳妥对象最适合在一些安全环境中（这些环境会禁止使用`this`和`new`），或防止数据被其他应用程序改动时使用

稳妥构造函数模式和寄生模式类似，有两点不同:

* 一是创建对象的实例方法不引用`this`
* 而是不使用`new`操作符调用构造函数

```js
function Person(name, job) {
  var o = new Object()
  o.name = name
  o.job = job
  o.sayName = function() {
    console.log(name)
  }
  return o
}

var person1 = Person('Jiang', 'student')
person1.sayName()
```

和寄生构造函数模式一样，这样创建出来的对象与构造函数之间没有什么关系，`instanceof`操作符对他们没有意义.

## 八、Object.create\(\)

Object.create\(proto \[, propertiesObject \]\) 是E5中提出的一种新的对象创建方式，该方法会使用指定的原型对象及其属性去创建一个新的对象。

**语法**

```js
Object.create(proto[, propertiesObject])
```

**参数**

* proto：新创建对象的原型对象，如果不是一个子函数，可以传一个null。如果proto参数不是 null 或一个对象，则抛出一个 TypeError 异常。
* propertiesObject（可选）：对象的属性描述符。 **返回值**

在指定原型对象上添加新属性后的对象。

例如：

```js
function Car (desc) {
  this.desc = desc;
  this.color = "red";
}

Car.prototype = {
  getInfo: function() {
   return 'A ' + this.color + ' ' + this.desc + '.';
  }
};
//instantiate object using the constructor function
var car = Object.create(Car.prototype);
car.color = "blue";
alert(car.getInfo());
```

结果为:A blue undefined.

## 总结几种方式的优缺点

**1. 对象字面量**

* 优点：简单
  缺点：如果需要创建很多对象需要写很多代码

**2. 工厂模式**

* 优点：可以无数次调用这个工厂函数
* 缺点：没有解决对象识别问题，不能区分对象类型

**3. 构造函数模式**

* 优点：可以检测对象类型
* 缺点：使用构造函数创建对象，每个方法都要在每个实例上重新创建一次

**4. 原型模式**

* 优点：可以让所有的实例对象共享它所包含的属性和方法，不必在构造函数中定义对象实例信息。
* 缺点：所有的属性都将被共享，这种共享对于函数非常合适

**5. 组合使用构造函数模式和原型模式**  
优点：它可以解决上面那些模式的缺点，使用此模式可以让每个实例都会有自己的一份实例属性副本，但同时又共享着对方法的引用。这样的话，即使实例属性修改引用类型的值，也不会影响其他实例的属性值了

**6. 寄生构造函数模式**  
除了使用new操作符并把使用的包装函数叫做构造函数之外，和工厂模式几乎一样

---

**参考文章**

* [JavaScript构造函数及原型对象](http://blog.csdn.net/a153375250/article/details/51083245)
* [一种新的javascript对象创建方式Object.create\(\)](http://www.jb51.net/article/77127.htm)
* [JavaScript创建对象的七种方式](https://xxxgitone.github.io/2017/06/10/JavaScript创建对象的七种方式/)



