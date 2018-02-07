# 第16节 箭头函数

箭头符号在JavaScript诞生时就已经存在，当初第一个JavaScript教程曾建议在HTML注释内包裹行内脚本，这样可以避免不支持JS的浏览器误将JS代码显示为文本。你会写这样的代码：

```html
<script language="javascript">
<!--
  document.bgColor = "brown";  // red
// -->
</script>
```

老式浏览器会将这段代码解析为两个不支持的标签和一条注释，只有新式浏览器才能识别出其中的JS代码。

为了支持这种奇怪的hack方式，浏览器中的JavaScript引擎将`<!--`这四个字符解析为单行注释的起始部分，我没开玩笑，这自始至终就是语言的一部分，直到现在仍然有效，这种注释符号不仅出现`<script>`标签后的首行，在JS代码的每个角落你都有可能见到它，甚至在Node中也是如此。

碰巧，[这种注释风格](http://people.mozilla.org/~jorendorff/es6-draft.html#sec-html-like-comments)首次在ES6中被标准化了，但在新标准中箭头被用来做其它事情。

箭头序列`-->`同样是单行注释的一部分。古怪的是，在HTML中`-->`之前的字符是注释的一部分，而在JS中`-->`之后的部分才是注释。

你一定感到陌生的是，只有当箭头在行首时才会注释当前行。这是因为在其它上下文中，`-->`是一个JS运算符：“趋向于”运算符！

```js
function countdown(n) {
  while (n --> 0)  // "n goes to zero"
    alert(n);
  blastoff();
}
```

上面这段代码[可以正常运行](http://codepen.io/anon/pen/oXZaBY?editors=001)，循环会一直重复直到n趋于0，这当然不是ES6中的新特性，它只不过是将两个你早已熟悉的特性通过一些误导性的手段结合在一起。你能理解么？通常来说，类似这种谜团都可以在[Stack Overflow](http://stackoverflow.com/questions/1642028/what-is-the-name-of-the-operator)上找到答案。

当然，同样地，小于等于操作符`<=`也形似箭头，你可以在JS代码、隐藏的图片样式中找到更多类似的箭头，但是我们就不继续寻找了，你应该注意到我们漏掉了一种特殊的箭头。

* `<!--`，单行注释
* `-->`，“趋向于”操作符
* `<=`，小于等于
* `=>`，这又是什么？

`=>`到底是什么？我们今天就来一探究竟。

首先，我们谈论一些有关函数的事情。

## 一、函数表达式无处不在

JavaScript中有一个有趣的特性，无论何时，当你需要一个函数时，你都可以在想添加的地方输入这个函数。

举个例子，假设你尝试告诉浏览器用户点击一个特定按钮后的行为，你会这样写：

```js
$("#confetti-btn").click(
```

jQuery的.click\(\)方法接受一个参数：一个函数。没问题，你可以在这里输入一个函数：

```js
$("#confetti-btn").click(function (event) {
  playTrumpet();
  fireConfettiCannon();
});
```

对 于现在的我们来说，写出这样的代码相当自然，而回忆起在这种编程方式流行之前，这种写法相对陌生一些，许多语言中都没有这种特性。1958年，Lisp首 先支持函数表达式，也支持调用lambda函数，而C++，Python、C\#以及Java在随后的多年中一直不支持这样的特性。

现在截然不同，所有的四种语言都已支持lambda函数，更新出现的语言普遍都支持内建的lambda函数。我们必须要感谢JavaScript和早期的JavaScript程序员，他们勇敢地构建了重度依赖lambda函数的库，让这种特性被广泛接受。

令人伤感的是，随后在所有我提及的语言中，只有JavaScript的lambda的语法最终变得冗长乏味。

```
// 六种语言中的简单函数示例
function (a) { return a > 0; } // JS
[](int a) { return a > 0; }  // C++
(lambda (a) (> a 0))  ;; Lisp
lambda a: a > 0  # Python
a => a > 0  // C#
a -> a > 0  // Java
```

## 二、箭袋中的新羽

ES6中引入了一种编写函数的新语法

```js
// ES5
var selected = allJobs.filter(function (job) {
  return job.isSelected();
});
// ES6
var selected = allJobs.filter(job => job.isSelected());
```

当你只需要一个只有一个参数的简单函数时，可以使用新标准中的箭头函数，它的语法非常简单：`标识符=>表达式`。你无需输入`function`和`return`，一些小括号、大括号以及分号也可以省略。

> （我个人对于这个特性非常感激，不再需要输入function这几个字符对我而言至关重要，因为我总是不可避免地错误写成functoin，然后我就不得不回过头改正它。）

如果要写一个接受多重参数（也可能没有参数，或者是不定参数、默认参数、参数解构）的函数，你需要用小括号包裹参数list。

```js
// ES5
var total = values.reduce(function (a, b) {
  return a + b;
}, 0);
// ES6
var total = values.reduce((a, b) => a + b, 0);
```

正如你使用类似Underscore.js和Immutable.js这样的库提供的函数工具，箭头函数运行起来同样美不可言。事实上，Immutable的文档中的示例全都由ES6写成，其中的许多特性已经用上了箭头函数。

那么不是非常函数化的情况又如何呢？除表达式外，箭头函数还可以包含一个块语句。回想一下我们之前的示例：

```js
// ES5
$("#confetti-btn").click(function (event) {
  playTrumpet();
  fireConfettiCannon();
});

// ES6
$("#confetti-btn").click( event => {
  playTrumpet();
  fireConfettiCannon();
});
```

这是一个微小的改进，对于使用了Promises的代码来说箭头函数的效果可以变得更加戏剧性，`}).then(function (result) {` 这样的一行代码可以堆积起来。

**注意，使用了块语句的箭头函数不会自动返回值，你需要使用return语句将所需值返回。**

> 小提示：当使用箭头函数创建普通对象时，你总是需要将对象包裹在小括号里。

```js
// 为与你玩耍的每一个小狗创建一个新的空对象
var chewToys = puppies.map(puppy => {});   // 这样写会报Bug！
var chewToys = puppies.map(puppy => ({})); //
```

不幸的是，一个空对象`{}`和一个空的块`{}`看起来完全一样。ES6中的规则是，紧随箭头的`{`被解析为块的开始，而不是对象的开始。因此，`puppy => {}`这段代码就被解析为没有任何行为并返回`undefined`的箭头函数。

更令人困惑的是，你的JavaScript引擎会将类似`{key: value}`的对象字面量解析为一个包含标记语句的块。幸运的是，`{`是唯一一个有歧义的字符，所以用小括号包裹对象字面量是唯一一个你需要牢记的小窍门。

### 三、这个函数的this值是什么呢？

普通`function`函数和箭头函数的行为有一个微妙的区别，**箭头函数没有它自己的**`this`**值，箭头函数内的**`this`**值继承自外围作用域。**

在我们尝试说明这个问题前，先一起回顾一下。

JavaScript中的`this`是如何工作的？它的值从哪里获取？这些问题的答案可都不简单，如果你对此倍感清晰，一定因为你长时间以来一直在处理类似的问题。

这个问题经常出现的其中一个原因是，无论是否需要，`function`函数总会自动接收一个`this`值。你是否写过这样的hack代码：

```js
{
  ...
  addAll: function addAll(pieces) {
    var self = this;
    _.each(pieces, function (piece) {
      self.add(piece);
    });
  },
  ...
}
```

在ES6中，不需要再hack this了，但你需要遵循以下规则：

* 通过`object.method()`语法调用的方法使用非箭头函数定义，这些函数需要从调用者的作用域中获取一个有意义的this值。
* 其它情况全都使用箭头函数。

```js
// ES6
{
  ...
  addAll: function addAll(pieces) {
    _.each(pieces, piece => this.add(piece));
  },
  ...
}
```

在ES6的版本中，注意addAll方法从它的调用者处获取了this值，内部函数是一个箭头函数，所以它继承了外围作用域的this值。

超赞的是，在ES6中你可以用更简洁的方式编写对象字面量中的方法，所以上面这段代码可以简化成：

```js
// ES6的方法语法
{
  ...
  addAll(pieces) {
    _.each(pieces, piece => this.add(piece));
  },
  ...
}
```

箭头函数与非箭头函数间还有一个细微的区别，**箭头函数不会获取它们自己的**`arguments`**对象**。诚然，在ES6中，你可能更多地会使用不定参数和默认参数值这些新特性。

## 四、什么时候“不要”用箭头函数

知晓哪些情况下应该绕过箭头函数，使用更赞成的旧的函数表达式 或者是新的短方法语法。并且注意一下短语法，因为它可能会影响代码可读性。

### 4.1 在对象上定义方法

在 JavaScript 中，方法作为一个函数存储为对象的一个属性。当调用方法时，this 指向该方法的从属对象。

#### 4.1.1 对象字面量

自从箭头函数有了短语法，非常吸引人用它来定义方法。让我们试试看：

```js
var calculate = {  
  array: [1, 2, 3],
  sum: () => {
    console.log(this === window); // => true
    return this.array.reduce((result, item) => result + item);
  }
};
console.log(this === window); // => true  
// Throws "TypeError: Cannot read property 'reduce' of undefined"
calculate.sum();
```

`calculate.sum` 方法是通过箭头函数定义的。但是在调用 `calculate.sum()` 的时候抛出了一个 TypeError，因为 `this.array` 的值是 `undefined`。

当调用 `calculate` 对象上的方法`sum()` 时，上下文仍然是 `window`。这个情况发生是因为箭头函数绑定了该`window` 对象的词法上下文。

执行 `this.array` 等同于 `window.array`， 值为 `undefined`。

解决方案是使用函数表达式或者方法定义的短语法 （ECMAScript 6可用）。 在这种情况下 this 决定于调用的对象，而不是紧邻上下文。 让我们看看修正的版本：

```js
var calculate = {  
  array: [1, 2, 3],
  sum() {
    console.log(this === calculate); // => true
    return this.array.reduce((result, item) => result + item);
  }
};
calculate.sum(); // => 6
```

因为 `sum` 是一个普通函数，调用 `calculate.sum()` 的 `this` 是 `calculate` 对象。 `this.array` 是数组的引用，因此元素之和计算正确，结果是: 6.

#### 4.1.2 对象原型

相同的规则也适用于在 `prototype` 对象上定义方法。

用箭头函数来定义 `sayCatName` 方法，会带来一个不正确的上下文 window：

```js
function MyCat(name) {  
  this.catName = name;
}
MyCat.prototype.sayCatName = () => {  
  console.log(this === window); // => true
  return this.catName;
};
var cat = new MyCat('Mew');  
cat.sayCatName(); // => undefined
```

使用 保守派 函数表达式：

```js
function MyCat(name) {  
  this.catName = name;
}
MyCat.prototype.sayCatName = function() {  
  console.log(this === cat); // => true
  return this.catName;
};
var cat = new MyCat('Mew');  
cat.sayCatName(); // => 'Mew'
```

`sayCatName` 普通函数被当做方法调用的时候 `cat.sayCatName()`，会把上下文改变为 `cat` 对象。

### 4.2 结合动态上下文的回调函数

this 在 JavaScript 中是一个很强大的特性。它允许利用函数调用的方式改变上下文。通常来说，上下文是一个调用发生时候的目标对象，让代码更加 自然化。这就好像 “某些事情正发生在该对象上”。

无论如何，箭头函数在声明的时候都会绑定静态的上下文，而不会是动态的。这是词素 this 不是很必要的一种情况。

给 DOM 元素装配事件监听器是客户端编程的一个通常的任务。一个事件用 this 作为目标元素去触发处理函数。这是一个动态上下文的简便用法。

接下来的例子试图使用一个箭头函数触发一个处理函数：

```js
var button = document.getElementById('myButton');  
button.addEventListener('click', () => {  
  console.log(this === window); // => true
  this.innerHTML = 'Clicked button';
});
```

this 在箭头函数中是 window，也就是被定义为全局上下文（译者注：这里应该描述的就是上文的例子）。当一个点击事件发生的时候，浏览器试着用 button 上下文去调用处理函数，但是箭头函数病不会改变它已经预定义的上下文。

`this.innerHTML` 等价于 `window.innerHTML` ，并没有什么意义。

你不得不应用一个函数表达式，去允许目标元素改变其上下文。

```js
var button = document.getElementById('myButton');  
button.addEventListener('click', function() {  
  console.log(this === button); // => true
  this.innerHTML = 'Clicked button';
});
```

当用户点击该按钮，this 在处理函数中是 button。 从而 `this.innerHTML = 'Clicked button'` 正确地修改了按钮的文本去反映点击状态。

### 4.3 调用构造器

`this` 在一个构造调用过程中是一个新创建的对象。 当执行 `new MyFunction()`，该构造器的上下文 `MyFunction` 是一个新的对象： `this instanceof MyFunction === true`.

**注意一个箭头函数不能作为构造器。 JavaScript 会通过抛出异常的方式进行隐式地预防。**

无论怎样，this 还是会从紧邻上下文中获取，而不是那个新创建的对象。 换句话说，一个箭头函数构造器的调用过程没有什么意义，反而会产生歧义。

让我们看看，如果试图去尝试使用箭头函数作为构造器，会发生什么：

```js
var Message = (text) => {  
  this.text = text;
};
// Throws "TypeError: Message is not a constructor"
var helloMessage = new Message('Hello World!');
```

执行 `new Message('Hello World!')`， `Message` 是一个箭头函数， JavaScript 抛出了一个 `TypeError` ，这意味着 `Message` 不能被用作于构造器。

与一些之前特定版本的 JavaScript 静默失败 相比，我认为 ECMAScript 6 在这些情况下提供含有错误信息的失败会更加高效。

上面的例子可以使用一个 函数表达式 来修正，这才是创建构造器正确的方式 （包括 函数声明）：

```js
var Message = function(text) {  
  this.text = text;
};
var helloMessage = new Message('Hello World!');  
console.log(helloMessage.text); // => 'Hello World!'
```

### 总结

**当要求动态上下文的时候，你就不能使用箭头函数，比如：定义方法，用构造器创建对象，处理时间时用 this 获取目标。**

---

**参考文章**

* [深入浅出ES6（七）：箭头函数 Arrow Functions](http://www.infoq.com/cn/articles/es6-in-depth-arrow-functions)
* [什么时候“不要”用箭头函数](http://www.zcfy.cc/article/when-not-to-use-arrow-functions-482.html)



