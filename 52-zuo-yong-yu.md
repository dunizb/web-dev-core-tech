# 第2节 作用域

JavaScript 有个特性称为作用域。尽管对于很多开发新手来说，作用域的概念不容易理解，我会尽可能地从最简单的角度向你解释它们。理解作用域能让你编写更优雅、错误更少的代码，并能帮助你实现强大的设计模式。

## 什么是作用域？

作用域是你的代码在运行时，各个变量、函数和对象的可访问性。换句话说，作用域决定了你的代码里的变量和其他资源在各个区域中的可见性。

## 为什么需要作用域？最小访问原则

那么，限制变量的可见性，不允许你代码中所有的东西在任意地方都可用的好处是什么？其中一个优势，是作用域为你的代码提供了一个安全层级。计算机安全中，有个常规的原则是：用户只能访问他们当前需要的东西。

想想计算机管理员吧。他们在公司各个系统上拥有很多控制权，看起来甚至可以给予他们拥有全部权限的账号。假设你有一家公司，拥有三个管理员，他们都有系统的全部访问权限，并且一切运转正常。但是突然发生了一点意外，你的一个系统遭到恶意病毒攻击。现在你不知道这谁出的问题了吧？你这才意识到你应该只给他们基本用户的账号，并且只在需要时赋予他们完全的访问权。这能帮助你跟踪变化并记录每个人的操作。这叫做最小访问原则。眼熟吗？这个原则也应用于编程语言设计，在大多数编程语言（包括 JavaScript）中称为作用域，接下来我们就要学习它。

在你的编程旅途中，你会意识到作用域在你的代码中可以提升性能，跟踪 bug 并减少 bug。作用域还解决不同范围的同名变量命名问题。记住不要弄混作用域和上下文。它们是不同的特性。

## JavaScript中的作用域

在 JavaScript 中有两种作用域

* 全局作用域
* 局部作用域

当变量定义在一个函数中时，变量就在局部作用域中，而定义在函数之外的变量则从属于全局作用域。每个函数在调用的时候会创建一个新的作用域。

### 全局作用域

当你在文档中（document）编写 JavaScript 时，你就已经在全局作用域中了。JavaScript 文档中（document）只有一个全局作用域。定义在函数之外的变量会被保存在全局作用域中。

```js
// the scope is by default global
var name = 'Hammad';
```

全局作用域里的变量能够在其他作用域中被访问和修改。

```js
var name = 'Hammad';
console.log(name); // logs 'Hammad'

function logName() {
    console.log(name); // 'name' is accessible here and everywhere else
}

logName(); // logs 'Hammad'
```

### 局部作用域

定义在函数中的变量就在局部作用域中。并且函数在每次调用时都有一个不同的作用域。这意味着同名变量可以用在不同的函数中。因为这些变量绑定在不同的函数中，拥有不同作用域，彼此之间不能访问。

```js
// Global Scope
function someFunction() {
    // Local Scope ##1
    function someOtherFunction() {
        // Local Scope ##2
    }
}
 
// Global Scope
function anotherFunction() {
    // Local Scope ##3
}
// Global Scope
```

## 块语句

块级声明包括if和switch，以及for和while循环，和函数不同，它们不会创建新的作用域。在块级声明中定义的变量从属于该块所在的作用域。

```js
if (true) {
    // this 'if' conditional block doesn't create a new scope
    var name = 'Hammad'; // name is still in the global scope
}

console.log(name); // logs 'Hammad'
```

ECMAScript 6 引入了let和const关键字。这些关键字可以代替var。

```js
var name = 'Hammad';

let likes = 'Coding';

const skills = 'Javascript and PHP';
```

和var关键字不同，let和const关键字支持在块级声明中创建使用局部作用域。

```js
if (true) {
    // this 'if' conditional block doesn't create a scope

    // name is in the global scope because of the 'var' keyword
    var name = 'Hammad';
    // likes is in the local scope because of the 'let' keyword
    let likes = 'Coding';
    // skills is in the local scope because of the 'const' keyword
    const skills = 'JavaScript and PHP';
}

console.log(name); // logs 'Hammad'
console.log(likes); // Uncaught ReferenceError: likes is not defined
console.log(skills); // Uncaught ReferenceError: skills is not defined
```

一个应用中全局作用域的生存周期与该应用相同。局部作用域只在该函数调用执行期间存在。

## 上下文

很多开发者经常弄混作用域和上下文，似乎两者是一个概念。但并非如此。作用域是我们上面讲到的那些，而上下文通常涉及到你代码某些特殊部分中的this值。作用域指的是变量的可见性，而上下文指的是在相同的作用域中的this的值。我们当然也可以使用函数方法改变上下文，这个之后我们再讨论。在全局作用域中，上下文总是 Window 对象。

如果作用域定义在一个对象的方法中，上下文就是这个方法所在的那个对象。

```js
class User {
    logName() {
        console.log(this);
    }
}

(new User).logName(); // logs User {}
```

\(new User\).logName\(\)是创建对象关联到变量并调用logName方法的一种简便形式。通过这种方式你并不需要创建一个新的变量。

你可能注意到一点，就是如果你使用new关键字调用函数时上下文的值会有差异。上下文会设置为被调用的函数的实例。考虑一下上面的这个例子，用new关键字调用的函数。

```js
function logFunction() {
    console.log(this);
}

new logFunction(); // logs logFunction {}
```

当在严格模式（strict mode）中调用函数时，上下文默认是 undefined。

## 执行环境

为了解决掉我们从上面学习中会出现的各种困惑，“执行环境（context）”这个词中的“环境（context）”指的是作用域而并非上下文。这是一个怪异的命名约定，但由于 JavaScript 的文档如此，我们只好也这样约定。

JavaScript 是一种单线程语言，所以它同一时间只能执行单个任务。其他任务排列在执行环境中。当 JavaScript 解析器开始执行你的代码，环境（作用域）默认设为全局。全局环境添加到你的执行环境中，事实上这是执行环境里的第一个环境。

之后，每个函数调用都会添加它的环境到执行环境中。无论是函数内部还是其他地方调用函数，都会是相同的过程。

每个函数都会创建它自己的执行环境。

当浏览器执行完环境中的代码，这个环境会从执行环境中弹出，执行环境中当前环境的状态会转移到父级环境。浏览器总是先执行在执行栈顶的执行环境（事实上就是你代码最里层的作用域）。

**创建阶段**

第一阶段是创建阶段，是函数刚被调用但代码并未执行的时候。创建阶段主要发生了 3 件事。

* 创建变量对象
* 创建作用域链
* 设置上下文（this）的值

**变量对象**

变量对象（Variable Object）也称为活动对象（activation object），包含所有变量、函数和其他在执行环境中定义的声明。当函数调用时，解析器扫描所有资源，包括函数参数、变量和其他声明。当所有东西装填进一个对象，这个对象就是变量对象。

```js
'variableObject': {
    // contains function arguments, inner variable and function declarations
}
```

**作用域链**

在执行环境创建阶段，作用域链在变量对象之后创建。作用域链包含变量对象。作用域链用于解析变量。当解析一个变量时，JavaScript 开始从最内层沿着父级寻找所需的变量或其他资源。作用域链包含自己执行环境以及所有父级环境中包含的变量对象。

```js
'scopeChain': {
    // contains its own variable object and other variable objects of the parent execution contexts
}
```

**执行环境对象**

执行环境可以用下面抽象对象表示：

## 词法作用域

词法作用域的意思是在函数嵌套中，内层函数可以访问父级作用域的变量等资源。这意味着子函数词法绑定到了父级执行环境。词法作用域有时和静态作用域有关。

```js
function grandfather() {
    var name = 'Hammad';
    // likes is not accessible here
    function parent() {
        // name is accessible here
        // likes is not accessible here
        function child() {
            // Innermost level of the scope chain
            // name is also accessible here
            var likes = 'Coding';
        }
    }
}
```

你可能注意到了词法作用域是向前的，意思是子执行环境可以访问name。但不是由父级向后的，意味着父级不能访问likes。这也告诉了我们，在不同执行环境中同名变量优先级在执行栈由上到下增加。一个变量和另一个变量同名，内层函数（执行栈顶的环境）有更高的优先级。

---

原文：[https://mp.weixin.qq.com/s?\_\_biz=MzAxODE2MjM1MA==&mid=2651553488&idx=1&sn=52417242d04de09e5099bf96dfc1f322&chksm=8025a911b7522007e238341f1e85891994777949edc9f62e5118fd31723056a2e63d81b0d0f0&mpshare=1&scene=23&srcid=0112i246ti2vkpT1ljN5C4r3%23rd](https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651553488&idx=1&sn=52417242d04de09e5099bf96dfc1f322&chksm=8025a911b7522007e238341f1e85891994777949edc9f62e5118fd31723056a2e63d81b0d0f0&mpshare=1&scene=23&srcid=0112i246ti2vkpT1ljN5C4r3%23rd)





