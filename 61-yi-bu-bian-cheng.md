# 第1节 异步编程

你可能知道，Javascript语言的执行环境是"单线程"（single thread）。

所谓"单线程"，就是指一次只能完成一件任务。如果有多个任务，就必须排队，前面一个任务完成，再执行后面一个任务，以此类推。

![](http://www.ruanyifeng.com/blogimg/asset/201212/bg2012122101.jpg)

这种模式的**好处是实现起来比较简单，执行环境相对单纯；坏处是只要有一个任务耗时很长，后面的任务都必须排队等着，会拖延整个程序的执行**。常见的浏览器无响应（假死），往往就是因为某一段Javascript代码长时间运行（比如死循环），导致整个页面卡在这个地方，其他任务无法执行。

为了解决这个问题，Javascript语言将任务的执行模式分成两种：同步（Synchronous）和异步（Asynchronous）。

**"同步模式"**就是上一段的模式，后一个任务等待前一个任务结束，然后再执行，程序的执行顺序与任务的排列顺序是一致的、同步的；**"异步模式"**则完全不同，每一个任务有一个或多个回调函数（callback），前一个任务结束后，不是执行后一个任务，而是执行回调函数，后一个任务则是不等前一个任务结束就执行，所以程序的执行顺序与任务的排列顺序是不一致的、异步的。

"异步模式"非常重要。在浏览器端，耗时很长的操作都应该异步执行，避免浏览器失去响应，最好的例子就是Ajax操作。在服务器端，"异步模式"甚至是唯一的模式，因为执行环境是单线程的，如果允许同步执行所有http请求，服务器性能会急剧下降，很快就会失去响应。

## 一、Javascript异步编程的4种方法

### 1.1 回调函数

这是异步编程最基本的方法。假定有两个函数f1和f2，后者等待前者的执行结果。

```js
f1();
f2();
```

如果f1是一个很耗时的任务，可以考虑改写f1，把f2写成f1的回调函数。

```js
function f1(callback){
   setTimeout(function () {
      // f1的任务代码
　　　 callback();
　  }, 1000);
}
```

执行代码就变成下面这样：

```js
f1(f2);
```

采用这种方式，我们把同步操作变成了异步操作，f1不会堵塞程序运行，相当于先执行程序的主要逻辑，将耗时的操作推迟执行。

**回调函数的优点是简单、容易理解和部署，缺点是不利于代码的阅读和维护，各个部分之间高度耦合（Coupling），流程会很混乱，而且每个任务只能指定一个回调函数。**

### 1.2 事件监听

另一种思路是采用事件驱动模式。任务的执行不取决于代码的顺序，而取决于某个事件是否发生。

还是以f1和f2为例。首先，为f1绑定一个事件（这里采用的jQuery的写法）。

```js
f1.on('done', f2);
```

上面这行代码的意思是，当f1发生done事件，就执行f2。然后，对f1进行改写：

```js
function f1(){
　　setTimeout(function () {
　　　  // f1的任务代码
　　　　f1.trigger('done');
　　}, 1000);
}
```

f1.trigger\('done'\)表示，执行完成后，立即触发done事件，从而开始执行f2。

**这种方法的优点是比较容易理解，可以绑定多个事件，每个事件可以指定多个回调函数，而且可以"去耦合"（Decoupling），有利于实现模块化。缺点是整个程序都要变成事件驱动型，运行流程会变得很不清晰。**

### 1.3 发布/订阅

上一节的"事件"，完全可以理解成"信号"。

我们假定，存在一个"信号中心"，某个任务执行完成，就向信号中心"发布"（publish）一个信号，其他任务可以向信号中心"订阅"（subscribe）这个信号，从而知道什么时候自己可以开始执行。这就叫做"发布/订阅模式"（publish-subscribe pattern），又称"观察者模式"（observer pattern）。

这个模式有多种实现，下面采用的是Ben Alman的Tiny Pub/Sub，这是jQuery的一个插件。

首先，f2向"信号中心"jQuery订阅"done"信号。

```js
jQuery.subscribe("done", f2);
```

然后，f1进行如下改写：

```js
function f1(){
　　setTimeout(function () {
　　　　// f1的任务代码
　　　　jQuery.publish("done");
　　}, 1000);
}
```

jQuery.publish\("done"\)的意思是，f1执行完成后，向"信号中心"jQuery发布"done"信号，从而引发f2的执行。

此外，f2完成执行后，也可以取消订阅（unsubscribe）。

```js
jQuery.unsubscribe("done", f2);
```

这种方法的性质与"事件监听"类似，但是明显优于后者。因为我们可以通过查看"消息中心"，了解存在多少信号、每个信号有多少订阅者，从而监控程序的运行。

下面是原生JavaScript模拟的一个“发布/订阅”模式

```js
var Subject = function(){
    var _observers = [];

    this.attach = function(observer){
        _observers.push(observer);
    }

    this.detach = function(){
        _observers.pop();
    }

    this.notify = function(msg){
        for(var i = 0; i < _observers.length; i++){
            _observers[i].update(msg);
        }
    }

    this.print = function(){
        console.log(_observers.length);
        console.log(_observers);
    }
}

var Observer = function(name){
    this.update = function(msg){
        console.log('I am ', name, 'and I get the message ', msg);
    }
}

var sub = new Subject();
sub.attach(new Observer('a'));
sub.attach(new Observer('b'));
sub.notify('hello');

console.log('');

setTimeout(function(){
    sub.detach();
    sub.attach(new Observer('c'));
    sub.notify('world');
}, 1000);
```

### 1.4 Promises对象

Promises对象是CommonJS工作组提出的一种规范，目的是为异步编程提供统一接口。

简单说，它的思想是，每一个异步任务返回一个Promise对象，该对象有一个then方法，允许指定回调函数。比如，f1的回调函数f2,可以写成：

```js
f1().then(f2);
```

f1要进行如下改写（这里使用的是jQuery的实现）：

```js
function f1(){
　  var dfd = $.Deferred();
　　setTimeout(function () {
　　　　// f1的任务代码
　　　　dfd.resolve();
　　}, 500);
　　return dfd.promise;
}
```

**这样写的优点在于，回调函数变成了链式写法，程序的流程可以看得很清楚，而且有一整套的配套方法，可以实现许多强大的功能。**

比如，指定多个回调函数：

```js
f1().then(f2).then(f3);
```

再比如，指定发生错误时的回调函数：

```js
f1().then(f2).fail(f3);
```

而且，它还有一个前面三种方法都没有的好处：如果一个任务已经完成，再添加回调函数，该回调函数会立即执行。所以，你不用担心是否错过了某个事件或信号。这种方法的缺点就是编写和理解，都相对比较难。

## 二、JavaScript异步编程开源库

### 2.1 jQuery的deferred对象

简单说，deferred对象就是jQuery的回调函数解决方案。在英语中，defer的意思是"延迟"，所以deferred对象的含义就是"延迟"到未来某个点再执行。

它解决了如何处理耗时操作的问题，对那些操作提供了更好的控制，以及统一的编程接口。它的主要功能，可以归结为四点。下面我们通过示例代码，一步步来学习。

**ajax操作的链式写法**

首先，回顾一下jQuery的ajax操作的传统写法：

```js
$.ajax({
　　url: "test.html",
　　success: function(){
　　　　alert("哈哈，成功了！");
　　},
　　error:function(){
　　　　alert("出错啦！");
　　}
});
```

在上面的代码中，$.ajax\(\)接受一个对象参数，这个对象包含两个方法：success方法指定操作成功后的回调函数，error方法指定操作失败后的回调函数。

$.ajax\(\)操作完成后，如果使用的是低于**1.5.0**版本的jQuery，返回的是XHR对象，你没法进行链式操作；如果高于1.5.0版本，返回的是deferred对象，可以进行链式操作。

现在，新的写法是这样的：

```js
$.ajax("test.html")
　　.done(function(){ alert("哈哈，成功了！"); })
　　.fail(function(){ alert("出错啦！"); });
```

可以看到，done\(\)相当于success方法，fail\(\)相当于error方法。采用链式写法以后，代码的可读性大大提高。

**指定同一操作的多个回调函数**

deferred对象的一大好处，就是它允许你自由添加多个回调函数。还是以上面的代码为例，如果ajax操作成功后，除了原来的回调函数，我还想再运行一个回调函数，怎么办？

很简单，直接把它加在后面就行了。

```js
$.ajax("test.html")
　　.done(function(){ alert("哈哈，成功了！");} )
　　.fail(function(){ alert("出错啦！"); } )
　　.done(function(){ alert("第二个回调函数！");} );
```

回调函数可以添加任意多个，它们按照添加顺序执行。

**为多个操作指定回调函数**

deferred对象的另一大好处，就是它允许你为多个事件指定一个回调函数，这是传统写法做不到的。

请看下面的代码，它用到了一个新的方法$.when\(\)：

```js
$.when($.ajax("test1.html"), $.ajax("test2.html"))
　　.done(function(){ alert("哈哈，成功了！"); })
　　.fail(function(){ alert("出错啦！"); });
```

这段代码的意思是，先执行两个操作$.ajax\("test1.html"\)和$.ajax\("test2.html"\)，如果都成功了，就运行done\(\)指定的回调函数；如果有一个失败或都失败了，就执行fail\(\)指定的回调函数。

**普通操作的回调函数接口（上）**

deferred对象的最大优点，就是它把这一套回调函数接口，从ajax操作扩展到了所有操作。也就是说，任何一个操作----不管是ajax操作还是本地操作，也不管是异步操作还是同步操作----都可以使用deferred对象的各种方法，指定回调函数。

我们来看一个具体的例子。假定有一个很耗时的操作wait：

```js
var wait = function(){
　　var tasks = function(){
　　　　alert("执行完毕！");
　　};
　　setTimeout(tasks,5000);
};
```

我们为它指定回调函数，应该怎么做呢？

很自然的，你会想到，可以使用$.when\(\)：

```js
$.when(wait())
　　.done(function(){ alert("哈哈，成功了！"); })
　　.fail(function(){ alert("出错啦！"); });
```

但是，这样写的话，done\(\)方法会立即执行，起不到回调函数的作用。原因在于$.when\(\)的参数只能是deferred对象，所以必须对wait\(\)进行改写：

```js
var dtd = $.Deferred(); // 新建一个deferred对象
var wait = function(dtd){
　　var tasks = function(){
　　　　alert("执行完毕！");
　　　　dtd.resolve(); // 改变deferred对象的执行状态
　　};
　　setTimeout(tasks,5000);
　　return dtd;
};
```

现在，wait\(\)函数返回的是deferred对象，这就可以加上链式操作了。

```js
$.when(wait(dtd))
　　.done(function(){ alert("哈哈，成功了！"); })
　　.fail(function(){ alert("出错啦！"); });
```

wait\(\)函数运行完，就会自动运行done\(\)方法指定的回调函数。

**普通操作的回调函数接口（中）**

另一种防止执行状态被外部改变的方法，是使用deferred对象的建构函数$.Deferred\(\)。

这时，wait函数还是保持不变，我们直接把它传入$.Deferred\(\)：

```js
$.Deferred(wait)
　　.done(function(){ alert("哈哈，成功了！"); })
　　.fail(function(){ alert("出错啦！"); });
```

jQuery规定，$.Deferred\(\)可以接受一个函数名（注意，是函数名）作为参数，$.Deferred\(\)所生成的deferred对象将作为这个函数的默认参数。

**普通操作的回调函数接口（下）**

除了上面两种方法以外，我们还可以直接在wait对象上部署deferred接口。

```js
var dtd = $.Deferred(); // 生成Deferred对象
var wait = function(dtd){
　　var tasks = function(){
　　　　alert("执行完毕！");
　　　　dtd.resolve(); // 改变Deferred对象的执行状态
　　};
　　setTimeout(tasks,5000);
};
dtd.promise(wait);
wait.done(function(){ alert("哈哈，成功了！"); })
.fail(function(){ alert("出错啦！"); });
wait(dtd);
```

这里的关键是dtd.promise\(wait\)这一行，它的作用就是在wait对象上部署Deferred接口。正是因为有了这一行，后面才能直接在wait上面调用done\(\)和fail\(\)。

**deferred.resolve\(\)方法和deferred.reject\(\)方法**

如果仔细看，你会发现在上面的wait\(\)函数中，还有一个地方我没讲解。那就是dtd.resolve\(\)的作用是什么？

要说清楚这个问题，就要引入一个新概念"执行状态"。jQuery规定，deferred对象有三种执行状态----未完成，已完成和已失败。如果执行状态是"已完成"（resolved）,deferred对象立刻调用done\(\)方法指定的回调函数；如果执行状态是"已失败"，调用fail\(\)方法指定的回调函数；如果执行状态是"未完成"，则继续等待，或者调用progress\(\)方法指定的回调函数（jQuery1.7版本添加）。

前面部分的ajax操作时，deferred对象会根据返回结果，自动改变自身的执行状态；但是，在wait\(\)函数中，这个执行状态必须由程序员手动指定。dtd.resolve\(\)的意思是，将dtd对象的执行状态从"未完成"改为"已完成"，从而触发done\(\)方法。

类似的，还存在一个deferred.reject\(\)方法，作用是将dtd对象的执行状态从"未完成"改为"已失败"，从而触发fail\(\)方法。

### 2.2 Q.js

Q.js是一个Promise函数库，已经实现了Promise/A+标准。

特点：

* 浏览器端和服务端公用
* 发展早，较其他库相对成熟

q.js的所有方法操作的对象都得是一个promise对象,下面来说说怎么用q来创建promise对象

下面所有实例统一在nodejs环境里执行,请执行下面命令安装q.js，然后引用它

```
npm install q --save
```

```js
var q = require('q'),
    fs = require('fs');
```

下面会用到文件模块，所以也要引用

**创建promise对象**

q.fcall 传递给此方法一个返回值的函数或者一个defer实例,将会创建一个promise对象

```js
var promise = q.fcall(function(){
    return [10, 20];
});

var promise1 = q.fcall(function(){
    var deferred = q.defer();
    fs.readFile('../data/file2.txt', 'utf8', deferred.makeNodeResolver());
    return deferred.promise;
});
```

q.defer 通过延迟对象来创建promise对象

```js
var getFile = function(){
    var deferred = q.defer();
    fs.readFile('../data/file.txt', 'utf8', function(err, content){
        if(err){
            deferred.reject(new Error(err));
        }else{
            deferred.resolve(content);
        }
    });
    return deferred.promise;
}
```

q.promise 传递此方法一个函数来创建promise对象

```js
var getFilePromise = function(){
    return q.promise(function(resolve, reject, notify){
        fs.readFile('../data/file.txt', 'utf8', function(err, content){
            if(err){
                reject(new Error(err));
            }else{
                resolve(content);
            }
        });
    })
}
```

**使用promise对象**

最简单的使用方法就是直接使用then方法,传入一个成功回调与一个异常回调

```js
// fcall创建的promise

promise.then(function(msg){
    console.log(msg);
}, function(err){
    // do stuff
});

// q.defer创建的promise
getFile().then(function(msg){
    console.log(msg);
}, function(err){
    // do stuff
})

// q.promise创建的promise中q.defer使用一样
```

q.all 使用此方法可以保证执行多个异步方法之后执行回调

```js
q.all([promise, promise1]).then(function(msg){
    console.log('normal', msg);
}, function(err){
    console.log(err);
})
```

上面的q.all方法成功返回的结果会返回一个包含所有异步方法结果的数组,出现一个异步方法异常则执行错误回调方法.

> 注意q.all 里的promise数组支持不带延迟对象以及延迟对象

q.when 使用此方法执行不带延迟对象以及延迟对象promise,不支持传递数组内带延迟对象

```js
q.when(promise1, function(msg){
    log('when' + msg);
},function(err){
    console.log(err);
})

q.when(promise, function(msg){
    log('when' + msg);
},function(err){
    console.log(err);
})

q.when([promise, promise], function(msg){
    log('when' + msg);
},function(err){
    console.log(err);
})
```

返回的结果处理跟q.all一样

> 注意q.when 不支持数组内传递延迟对象

q.nfcall \| q.nfapply 这两方法是对nodejs里异步方法的适配，不用写上面promise对象定义里的resolve及reject等

```js
q.nfcall(fs.readFile, '../data/file.txt', 'utf8').then(function(msg){
    console.log('nfcall', msg);
})

q.nfapply(fs.readFile, ['../data/file.txt', 'utf8']).then(function(msg){
    console.log('nfapply', msg);
})
```

其实nfcall = node function call, nfapply = node function apply,传递的传递参考call与apply方法,回调处理跟上面的一样

当回调函数内出现异常时,回调异常函数是catch不到的,这里q提供了一个fail方法

```js
ar getFileHandy = function(){
    var deferred = q.defer();
    fs.readFile('../data/file1.txt', 'utf8', deferred.makeNodeResolver());
    return deferred.promise;
}
getFileHandy().then(function(msg){
    console.log(msg);
    throw new Error('demo exception');
}).fail(function(err){
    // 此处可以catch 所有的异常
    console.log(err);
});
```

以上都是单一的异步方法调用，下面说说q里的链式调用

```js
promise1.then(function(msg){
    return getFile();
}).
then(function(msg){
    return getFileHandy()
    .then(function(data){
        return getFilePromise();
    })
    .then(function(content){
        console.log('>>' + content);
        throw new Error('haha error!');
    });
}).
fail(function(err){
    console.log('err>>' + err);
});
```

所谓的链式调用就是在成功回调内返回一个新的promise对象,假如之后的回调不需要获取闭包内容的话,可以直接返回,否则可以接着写then方法调用。

---

**参考文章：**

* [Javascript异步编程的4种方法](http://www.ruanyifeng.com/blog/2012/12/asynchronous＿javascript.html)
* [jQuery的deferred对象详解](http://www.ruanyifeng.com/blog/2011/08/a_detailed_explanation_of_jquery_deferred_object.html)
* [Promise实现之q.js](https://yq.aliyun.com/articles/53393)



