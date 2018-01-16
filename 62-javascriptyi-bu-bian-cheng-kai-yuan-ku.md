# 第2节 JavaScript异步编程开源库

## 一、jQuery的deferred对象

简单说，deferred对象就是jQuery的回调函数解决方案。在英语中，defer的意思是"延迟"，所以deferred对象的含义就是"延迟"到未来某个点再执行。

它解决了如何处理耗时操作的问题，对那些操作提供了更好的控制，以及统一的编程接口。它的主要功能，可以归结为四点。下面我们通过示例代码，一步步来学习。

### 1.1 **ajax操作的链式写法**

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

### 1.2 **指定同一操作的多个回调函数**

deferred对象的一大好处，就是它允许你自由添加多个回调函数。还是以上面的代码为例，如果ajax操作成功后，除了原来的回调函数，我还想再运行一个回调函数，怎么办？

很简单，直接把它加在后面就行了。

```js
$.ajax("test.html")
　　.done(function(){ alert("哈哈，成功了！");} )
　　.fail(function(){ alert("出错啦！"); } )
　　.done(function(){ alert("第二个回调函数！");} );
```

回调函数可以添加任意多个，它们按照添加顺序执行。

### 1.3 **为多个操作指定回调函数**

deferred对象的另一大好处，就是它允许你为多个事件指定一个回调函数，这是传统写法做不到的。

请看下面的代码，它用到了一个新的方法$.when\(\)：

```js
$.when($.ajax("test1.html"), $.ajax("test2.html"))
　　.done(function(){ alert("哈哈，成功了！"); })
　　.fail(function(){ alert("出错啦！"); });
```

这段代码的意思是，先执行两个操作$.ajax\("test1.html"\)和$.ajax\("test2.html"\)，如果都成功了，就运行done\(\)指定的回调函数；如果有一个失败或都失败了，就执行fail\(\)指定的回调函数。

### 1.4 **普通操作的回调函数接口（上）**

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

### 1.5 **普通操作的回调函数接口（中）**

另一种防止执行状态被外部改变的方法，是使用deferred对象的建构函数$.Deferred\(\)。

这时，wait函数还是保持不变，我们直接把它传入$.Deferred\(\)：

```js
$.Deferred(wait)
　　.done(function(){ alert("哈哈，成功了！"); })
　　.fail(function(){ alert("出错啦！"); });
```

jQuery规定，$.Deferred\(\)可以接受一个函数名（注意，是函数名）作为参数，$.Deferred\(\)所生成的deferred对象将作为这个函数的默认参数。

### 1.6 **普通操作的回调函数接口（下）**

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

### 1.7 **deferred.resolve\(\)方法和deferred.reject\(\)方法**

如果仔细看，你会发现在上面的wait\(\)函数中，还有一个地方我没讲解。那就是dtd.resolve\(\)的作用是什么？

要说清楚这个问题，就要引入一个新概念"执行状态"。jQuery规定，deferred对象有三种执行状态----未完成，已完成和已失败。如果执行状态是"已完成"（resolved）,deferred对象立刻调用done\(\)方法指定的回调函数；如果执行状态是"已失败"，调用fail\(\)方法指定的回调函数；如果执行状态是"未完成"，则继续等待，或者调用progress\(\)方法指定的回调函数（jQuery1.7版本添加）。

前面部分的ajax操作时，deferred对象会根据返回结果，自动改变自身的执行状态；但是，在wait\(\)函数中，这个执行状态必须由程序员手动指定。dtd.resolve\(\)的意思是，将dtd对象的执行状态从"未完成"改为"已完成"，从而触发done\(\)方法。

类似的，还存在一个deferred.reject\(\)方法，作用是将dtd对象的执行状态从"未完成"改为"已失败"，从而触发fail\(\)方法。

## 二、Q.js

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

### 2.1 **创建promise对象**

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

### 2.2 **使用promise对象**

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

**参考文章**

* [Promise实现之q.js](https://yq.aliyun.com/articles/53393)



