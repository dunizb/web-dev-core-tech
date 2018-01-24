# 第16节 定时器

JavaScript提供定时执行代码的功能，叫做定时器（timer），主要由setTimeout\(\)和setInterval\(\)这两个函数来完成。它们向任务队列添加定时任务。

## 一、setTimeout\(\)

### 1.1 基本用法

setTimeout函数用来指定某个函数或某段代码，在多少毫秒之后执行。它返回一个整数，表示定时器的编号，以后可以用来取消这个定时器。

```js
var timerId = setTimeout(func|code, delay)
```

上面代码中，setTimeout函数接受两个参数，第一个参数func\|code是将要推迟执行的函数名或者一段代码，第二个参数delay是推迟执行的毫秒数。

```js
console.log(1);
setTimeout('console.log(2)',1000);
console.log(3);
```

上面代码的输出结果就是1，3，2，因为setTimeout指定第二行语句推迟1000毫秒再执行。

需要注意的是，推迟执行的代码必须以字符串的形式，放入setTimeout，因为**引擎内部使用eval函数**，将字符串转为代码。如果推迟执行的是函数，则可以直接将函数名，放入setTimeout。一方面eval函数有安全顾虑，另一方面为了便于JavaScript引擎优化代码，setTimeout方法一般总是采用函数名的形式，就像下面这样。

```js
function f(){
  console.log(2);
}

setTimeout(f,1000);

// 或者
setTimeout(function (){console.log(2)},1000);
```

### 1.2 参数问题

如果省略setTimeout的第二个参数，则该参数默认为0。

除了前两个参数，setTimeout还允许添加更多的参数。它们将被传入推迟执行的函数（回调函数）。

```js
setTimeout(function(a,b){
  console.log(a+b);
},1000,1,1);
```

上面代码中，setTimeout共有4个参数。最后那两个参数，将在1000毫秒之后回调函数执行时，作为回调函数的参数。

IE 9.0及以下版本，只允许setTimeout有两个参数，不支持更多的参数。这时有三种解决方法。第一种是在一个匿名函数里面，让回调函数带参数运行，再把匿名函数输入setTimeout。

```js
setTimeout(function() {
  myFunc("one", "two", "three");
}, 1000);
```

上面代码中，myFunc是真正要推迟执行的函数，有三个参数。如果直接放入setTimeout，低版本的IE不能带参数，所以可以放在一个匿名函数。

第二种解决方法是使用bind方法，把多余的参数绑定在回调函数上面，生成一个新的函数输入setTimeout。

```js
setTimeout(function(arg1){}.bind(undefined, 10), 1000);
```

上面代码中，bind方法第一个参数是undefined，表示将原函数的this绑定全局作用域，第二个参数是要传入原函数的参数。它运行后会返回一个新函数，该函数不带参数。

第三种解决方法是自定义setTimeout，使用apply方法将参数输入回调函数。

```js
<!--[if lte IE 9]><script>
(function(f){
window.setTimeout =f(window.setTimeout);
window.setInterval =f(window.setInterval);
})(function(f){return function(c,t){
var a=[].slice.call(arguments,2);return f(function(){c.apply(this,a)},t)}
});
</script><![endif]-->
```

除了参数问题，setTimeout还有一个需要注意的地方：如果被setTimeout推迟执行的回调函数是某个对象的方法，那么该方法中的this关键字将指向全局环境，而不是定义时所在的那个对象。

```js
var x = 1;

var o = {
  x: 2,
  y: function(){
    console.log(this.x);
  }
};

setTimeout(o.y,1000);
// 1
```

上面代码输出的是1，而不是2，这表示o.y的this所指向的已经不是o，而是全局环境了。

再看一个不容易发现错误的例子。

```js
function User(login) {
  this.login = login;
  this.sayHi = function() {
    console.log(this.login);
  }
}

var user = new User('John');

setTimeout(user.sayHi, 1000);
```

上面代码只会显示undefined，因为等到user.sayHi执行时，它是在全局对象中执行，所以this.login取不到值。

为了防止出现这个问题，一种解决方法是将user.sayHi放在函数中执行。

```js
setTimeout(function() {
  user.sayHi();
}, 1000);
```

上面代码中，sayHi是在user作用域内执行，而不是在全局作用域内执行，所以能够显示正确的值。

另一种解决方法是，使用bind方法，将绑定sayHi绑定在user上面。

```js
setTimeout(user.sayHi.bind(user), 1000);
```

> HTML 5标准规定，`setTimeout`的最短时间间隔是4毫秒。为了节电，对于那些不处于当前窗口的页面，浏览器会将时间间隔扩大到1000毫秒。另外，如果笔记本电脑处于电池供电状态，Chrome和IE 9以上的版本，会将时间间隔切换到系统定时器，大约是15.6毫秒。二、

## 二、setInterval\(\)

setInterval函数的用法与setTimeout完全一致，区别仅仅在于setInterval指定某个任务每隔一段时间就执行一次，也就是无限次的定时执行。

与setTimeout一样，除了前两个参数，setInterval方法还可以接受更多的参数，它们会传入回调函数，下面是一个例子。

```js
function f(){
  for (var i=0;i<arguments.length;i++){
    console.log(arguments[i]);
  }
}

setInterval(f, 1000, "Hello World");
// Hello World
// Hello World
// Hello World
// ...
```

如果网页不在浏览器的当前窗口（或tab），许多浏览器限制setInteral指定的反复运行的任务最多每秒执行一次。

setInterval的一个常见用途是实现轮询。下面是一个轮询URL的Hash值是否发生变化的例子。

```js
var hash = window.location.hash;
var hashWatcher = setInterval(function() {
  if (window.location.hash != hash) {
    updatePage();
  }
}, 1000);
```

setInterval指定的是“开始执行”之间的间隔，并不考虑每次任务执行本身所消耗的时间。因此实际上，两次执行之间的间隔会小于指定的时间。比如，setInterval指定每100ms执行一次，每次执行需要5ms，那么第一次执行结束后95毫秒，第二次执行就会开始。如果某次执行耗时特别长，比如需要105毫秒，那么它结束后，下一次执行就会立即开始。

为了确保两次执行之间有固定的间隔，可以不用setInterval，而是每次执行结束后，使用setTimeout指定下一次执行的具体时间。

```js
var i = 1;
var timer = setTimeout(function () {
  alert(i++);
  timer = setTimeout(arguments.callee, 2000);
}, 2000);
```

上面代码可以确保，下一个对话框总是在关闭上一个对话框之后2000毫秒弹出。

根据这种思路，可以自己部署一个函数，实现间隔时间确定的setInterval的效果。

```js
function interval(func, wait){
  var interv = function(){
    func.call(null);
    setTimeout(interv, wait);
  };

  setTimeout(interv, wait);
}

interval(function(){
  console.log(2);
},1000);
```

上面代码部署了一个interval函数，用循环调用setTimeout模拟了setInterval。

**HTML 5标准规定，setInterval的最短间隔时间是10毫秒，也就是说，小于10毫秒的时间间隔会被调整到10毫秒。**

## 三、clearTimeout\(\)，clearInterval\(\)

setTimeout和setInterval函数，都返回一个表示计数器编号的整数值，将该整数传入clearTimeout和clearInterval函数，就可以取消对应的定时器。

```js
var id1 = setTimeout(f,1000);
var id2 = setInterval(f,1000);

clearTimeout(id1);
clearInterval(id2);
```

setTimeout和setInterval返回的整数值是连续的，也就是说，第二个setTimeout方法返回的整数值，将比第一个的整数值大1。利用这一点，可以写一个函数，取消当前所有的setTimeout。

```js
(function() {
  var gid = setInterval(clearAllTimeouts, 0);

  function clearAllTimeouts() {
    var id = setTimeout(function() {}, 0);
    while (id > 0) {
      if (id !== gid) {
        clearTimeout(id);
      }
      id--;
    }
  }
})();
```

运行上面代码后，实际上再设置任何setTimeout都无效了。

下面是一个clearTimeout实际应用的例子。有些网站会实时将用户在文本框的输入，通过Ajax方法传回服务器，jQuery的写法如下。

```js
$('textarea').on('keydown', ajaxAction);
```

这样写有一个很大的缺点，就是如果用户连续击键，就会连续触发keydown事件，造成大量的Ajax通信。这是不必要的，而且很可能会发生性能问题。正确的做法应该是，设置一个门槛值，表示两次Ajax通信的最小间隔时间。如果在设定的时间内，发生新的keydown事件，则不触发Ajax通信，并且重新开始计时。如果过了指定时间，没有发生新的keydown事件，将进行Ajax通信将数据发送出去。

```js
$('textarea').on('keydown', debounce(ajaxAction, 2500))
```

利用setTimeout和clearTimeout，可以实现debounce方法，该方法用于防止某个函数在短时间内被密集调用。具体来说，debounce方法返回一个新版的该函数，这个新版函数调用后，只有在指定时间内没有新的调用，才会执行，否则就重新计时。

```js
function debounce(fn, delay){
  var timer = null; // 声明计时器
  return function(){
    var context = this;
    var args = arguments;
    clearTimeout(timer);
    timer = setTimeout(function(){
      fn.apply(context, args);
    }, delay);
  };
}

// 用法示例
var todoChanges = _.debounce(batchLog, 1000);
Object.observe(models.todo, todoChanges);
```

> 现实中，最好不要设置太多个setTimeout和setInterval，它们耗费CPU。比较理想的做法是，将要推迟执行的代码都放在一个函数里，然后只对这个函数使用setTimeout或setInterval。

## 四、运行机制

setTimeout和setInterval的运行机制是，将指定的代码移出本次执行，等到下一轮 Event Loop 时，再检查是否到了指定时间。如果到了，就执行对应的代码；如果不到，就等到再下一轮 Event Loop 时重新判断。

这意味着，setTimeout和setInterval指定的代码，必须等到本轮 Event Loop 的所有任务都执行完，才会开始执行。由于前面的任务到底需要多少时间执行完，是不确定的，所以没有办法保证，setTimeout和setInterval指定的任务，一定会按照预定时间执行。

```js
setTimeout(someTask, 100);
veryLongTask();
```

上面代码的setTimeout，指定100毫秒以后运行一个任务。但是，如果后面的veryLongTask函数（同步任务）运行时间非常长，过了100毫秒还无法结束，那么被推迟运行的someTask就只有等着，等到veryLongTask运行结束，才轮到它执行。

下面是setInterval的例子。

```js
setInterval(function () {
  console.log(2);
}, 1000);

sleep(3000);
```

上面的第一行语句要求每隔1000毫秒，就输出一个2。但是，紧接着的语句需要3000毫秒才能完成，那么setInterval就必须推迟到3000毫秒之后才开始生效。这3000毫秒之内，setInterval不会产生累积效应。

## 五、正常任务与微任务

正常情况下，JavaScript的任务是同步执行的，即执行完前一个任务，然后执行后一个任务。只有遇到异步任务的情况下，执行顺序才会改变。

这时，需要区分两种任务：正常任务（task）与微任务（microtask）。它们的区别在于，“正常任务”在下一轮Event Loop执行，“微任务”在本轮Event Loop的所有任务结束后执行。

```js
console.log(1);

setTimeout(function() {
  console.log(2);
}, 0);

Promise.resolve().then(function() {
  console.log(3);
}).then(function() {
  console.log(4);
});

console.log(5);

// 1
// 5
// 3
// 4
// 2
```

上面代码的执行结果说明，setTimeout\(fn, 0\)在Promise.resolve之后执行。

这是因为setTimeout语句指定的是“正常任务”，即不会在当前的Event Loop执行。而Promise会将它的回调函数，在状态改变后的那一轮Event Loop指定为微任务。所以，3和4输出在5之后、2之前。

正常任务包括以下情况。

* setTimeout
* setInterval
* setImmediate
* I/O
* 各种事件（比如鼠标单击事件）的回调函数

微任务目前主要是process.nextTick\(Nodejs的API\)和 Promise 这两种情况。

事实上，process.nextTick是不会进入异步队列的，而是直接在主线程队列尾强插一个任务，虽然不会阻塞主线程，但是会阻塞异步任务的执行，如果有嵌套的process.nextTick，那异步任务就永远没机会被执行到了。

---

原文：[http://javascript.ruanyifeng.com/advanced/timer.html](http://javascript.ruanyifeng.com/advanced/timer.html)





