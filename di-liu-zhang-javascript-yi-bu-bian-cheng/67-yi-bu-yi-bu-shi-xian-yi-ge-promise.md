# 第7节 一步一步实现一个Promise

## 一、Promise标准解读 {#一、Promise标准解读}

**1. 只有一个then方法，没有catch，race，all等方法，甚至没有构造函数**  
Promise标准中仅指定了Promise对象的then方法的行为，其它一切我们常见的方法/函数都并没有指定，包括catch，race，all等常用方法，甚至也没有指定该如何构造出一个Promise对象，另外then也没有一般实现中（Q, $q等）所支持的第三个参数，一般称onProgress

**2. then方法返回一个新的Promise**  
Promise的then方法返回一个新的Promise，而不是返回this，此处在下文会有更多解释

```js
promise2 = promise1.then(alert)
promise2 != promise1 // true
```

**3. 不同Promise的实现需要可以相互调用\(interoperable\)**

**4. Promise的初始状态为pending，它可以由此状态转换为fulfilled（本文为了一致把此状态叫做resolved）或者rejected，一旦状态确定，就不可以再次转换为其它状态，状态确定的过程称为settle**

> [更具体的标准见这里](http://link.zhihu.com/?target=https%3A//promisesaplus.com/)

下面我们就来一步一步实现一个Promise

## 二、一步一步实现一个Promise {#二、一步一步实现一个Promise}

下面我们就来一步一步实现一个Promise

### 2.1 构造函数 {#2.1构造函数}

因为标准并没有指定如何构造一个Promise对象，所以我们同样以目前一般Promise实现中通用的方法来构造一个Promise对象，也是ES6原生Promise里所使用的方式，即：

```js
// Promise构造函数接收一个executor函数，executor函数执行完同步或异步操作后，调用它的两个参数resolve和reject
var promise = new Promise(function(resolve, reject) {
  /*
    如果操作成功，调用resolve并传入value
    如果操作失败，调用reject并传入reason
  */
})
```

我们先实现构造函数的框架如下：

```js
function Promise(executor) {
  var self = this
  self.status = 'pending' // Promise当前的状态
  self.data = undefined // Promise的值
  // Promise resolve时的回调函数集，因为在Promise结束之前有可能有多个回调添加到它上面
  self.onResolvedCallback = [] 
  // Promise reject时的回调函数集，因为在Promise结束之前有可能有多个回调添加到它上面
  self.onRejectedCallback = [] 
  executor(resolve, reject) // 执行executor并传入相应的参数
}
```

上面的代码基本实现了Promise构造函数的主体，但目前还有两个问题：

1. 我们给executor函数传了两个参数：resolve和reject，这两个参数目前还没有定义
2. executor有可能会出错（throw），类似下面这样，而如果executor出错，Promise应该被其throw出的值reject：

```js
new Promise(function(resolve, reject) {
  throw 2
})
```

所以我们需要在构造函数里定义resolve和reject这两个函数：

```js
function Promise(executor) {
  var self = this
  self.status = 'pending' // Promise当前的状态
  self.data = undefined // Promise的值
  self.onResolvedCallback = [] // Promise resolve时的回调函数集，因为在Promise结束之前有可能有多个回调添加到它上面
  self.onRejectedCallback = [] // Promise reject时的回调函数集，因为在Promise结束之前有可能有多个回调添加到它上面
  function resolve(value) {
    // TODO
  }
  function reject(reason) {
    // TODO
  }
  try { // 考虑到执行executor的过程中有可能出错，所以我们用try/catch块给包起来，并且在出错后以catch到的值reject掉这个Promise
    executor(resolve, reject) // 执行executor
  } catch(e) {
    reject(e)
  }
}
```

有人可能会问，resolve和reject这两个函数能**不能不定义在构造函数里**呢？考虑到我们在executor函数里是以resolve\(value\)，reject\(reason\)的形式调用的这两个函数，而不是以resolve.call\(promise, value\)，reject.call\(promise, reason\)这种形式调用的，所以这两个函数在调用时的内部也必然有一个隐含的this，也就是说，要么这两个函数是经过bind后传给了executor，要么它们定义在构造函数的内部，使用self来访问所属的Promise对象。所以如果我们想把这两个函数定义在构造函数的外部，确实是可以这么写的：

```js
function resolve() {
  // TODO
}
function reject() {
  // TODO
}
function Promise(executor) {
  try {
    executor(resolve.bind(this), reject.bind(this))
  } catch(e) {
    reject.bind(this)(e)
  }
}
```

但是众所周知，bind也会返回一个新的函数，这么一来还是相当于每个Promise对象都有一对属于自己的resolve和reject函数，就跟写在构造函数内部没什么区别了，所以我们就直接把这两个函数定义在构造函数里面了。不过话说回来，如果浏览器对bind的所优化，使用后一种形式应该可以提升一下内存使用效率。

**另外我们这里的实现并没有考虑隐藏this上的变量，这使得这个Promise的状态可以在executor函数外部被改变，在一个靠谱的实现里，构造出的Promise对象的状态和最终结果应当是无法从外部更改的。**

接下来，我们实现resolve和reject这两个函数

```js
function Promise(executor) {
  // …
  function resolve(value) {
    if (self.status === 'pending') {
      self.status = 'resolved'
      self.data = value
      for(var i = 0; i < self.onResolvedCallback.length; i++) {
        self.onResolvedCallback[i](value)
      }
    }
  }
  function reject(reason) {
    if (self.status === 'pending') {
      self.status = 'rejected'
      self.data = reason
      for(var i = 0; i < self.onRejectedCallback.length; i++) {
        self.onRejectedCallback[i](reason)
      }
    }
  }
  // …
}
```

基本上就是在判断状态为pending之后把状态改为相应的值，并把对应的value和reason存在self的data属性上面，之后执行相应的回调函数，逻辑很简单，这里就不多解释了。

### 2.2 then方法 {#2.2then方法}

Promise对象有一个then方法，用来注册在这个Promise状态确定后的回调，很明显，then方法需要写在原型链上。then方法会返回一个Promise，关于这一点，Promise/A+标准并没有要求返回的这个Promise是一个新的对象，但在Promise/A标准中，明确规定了then要返回一个新的对象，目前的Promise实现中then几乎都是返回一个新的Promise\(详情\)对象，所以在我们的实现中，也让then返回一个新的Promise对象。

关于这一点，我认为标准中是有一点矛盾的：

[标准中说](http://link.zhihu.com/?target=https%3A//promisesaplus.com/%23point-49)，如果promise2 = promise1.then\(onResolved, onRejected\)里的onResolved/onRejected返回一个Promise，则promise2直接取这个Promise的状态和值为己用，但考虑如下代码：

```js
promise2 = promise1.then(function foo(value) {
  return Promise.reject(3)
})
```

此处如果foo运行了，则promise1的状态必然已经确定且为resolved，如果then返回了this（即promise2 === promise1），说明promise2和promise1是同一个对象，而此时promise1/2的状态已经确定，没有办法再取Promise.reject\(3\)的状态和结果为己用，因为Promise的状态确定后就不可再转换为其它状态。

另外每个Promise对象都可以在其上多次调用then方法，而每次调用then返回的Promise的状态取决于那一次调用then时传入参数的返回值，所以then不能返回this，因为then每次返回的Promise的结果都有可能不同。

下面我们来实现then方法：

```js
// then方法接收两个参数，onResolved，onRejected，分别为Promise成功或失败后的回调
Promise.prototype.then = function(onResolved, onRejected) {
  var self = this
  var promise2
  // 根据标准，如果then的参数不是function，则我们需要忽略它，此处以如下方式处理
  onResolved = typeof onResolved === 'function' ? onResolved : function(v) {}
  onRejected = typeof onRejected === 'function' ? onRejected : function(r) {}
  if (self.status === 'resolved') {
    return promise2 = new Promise(function(resolve, reject) {
    })
  }
  if (self.status === 'rejected') {
    return promise2 = new Promise(function(resolve, reject) {
    })
  }
  if (self.status === 'pending') {
    return promise2 = new Promise(function(resolve, reject) {
    })
  }
}
```

Promise总共有三种可能的状态，我们分三个if块来处理，在里面分别都返回一个new Promise。

根据标准，我们知道，对于如下代码，promise2的值取决于then里面函数的返回值：

```js
promise2 = promise1.then(function(value) {
  return 4
}, function(reason) {
  throw new Error('sth went wrong')
})
```

如果promise1被resolve了，promise2的将被4 resolve，如果promise1被reject了，promise2将被new Error\('sth went wrong'\) reject，更多复杂的情况不再详述。

所以，我们需要在then里面执行onResolved或者onRejected，并根据返回值\(标准中记为x\)来确定promise2的结果，并且，如果onResolved/onRejected返回的是一个Promise，promise2将直接取这个Promise的结果：

```js
Promise.prototype.then = function(onResolved, onRejected) {
  var self = this
  var promise2
  // 根据标准，如果then的参数不是function，则我们需要忽略它，此处以如下方式处理
  onResolved = typeof onResolved === 'function' ? onResolved : function(value) {}
  onRejected = typeof onRejected === 'function' ? onRejected : function(reason) {}
  if (self.status === 'resolved') {
    // 如果promise1(此处即为this/self)的状态已经确定并且是resolved，我们调用onResolved
    // 因为考虑到有可能throw，所以我们将其包在try/catch块里
    return promise2 = new Promise(function(resolve, reject) {
      try {
        var x = onResolved(self.data)
        if (x instanceof Promise) { // 如果onResolved的返回值是一个Promise对象，直接取它的结果做为promise2的结果
          x.then(resolve, reject)
        }
        resolve(x) // 否则，以它的返回值做为promise2的结果
      } catch (e) {
        reject(e) // 如果出错，以捕获到的错误做为promise2的结果
      }
    })
  }
  // 此处与前一个if块的逻辑几乎相同，区别在于所调用的是onRejected函数，就不再做过多解释
  if (self.status === 'rejected') {
    return promise2 = new Promise(function(resolve, reject) {
      try {
        var x = onRejected(self.data)
        if (x instanceof Promise) {
          x.then(resolve, reject)
        }
      } catch (e) {
        reject(e)
      }
    })
  }
  if (self.status === 'pending') {
  // 如果当前的Promise还处于pending状态，我们并不能确定调用onResolved还是onRejected，
  // 只能等到Promise的状态确定后，才能确实如何处理。
  // 所以我们需要把我们的**两种情况**的处理逻辑做为callback放入promise1(此处即this/self)的回调数组里
  // 逻辑本身跟第一个if块内的几乎一致，此处不做过多解释
    return promise2 = new Promise(function(resolve, reject) {
      self.onResolvedCallback.push(function(value) {
        try {
          var x = onResolved(self.data)
          if (x instanceof Promise) {
            x.then(resolve, reject)
          }
        } catch (e) {
          reject(e)
        }
      })
      self.onRejectedCallback.push(function(reason) {
        try {
          var x = onRejected(self.data)
          if (x instanceof Promise) {
            x.then(resolve, reject)
          }
        } catch (e) {
          reject(e)
        }
      })
    })
  }
}
// 为了下文方便，我们顺便实现一个catch方法
Promise.prototype.catch = function(onRejected) {
  return this.then(null, onRejected)
}
```

至此，我们基本实现了Promise标准中所涉及到的内容，但还有几个问题：

不同的Promise实现之间需要无缝的可交互，即Q的Promise，ES6的Promise，和我们实现的Promise之间以及其它的Promise实现，应该并且是有必要无缝相互调用的，比如：

```js
// 此处用MyPromise来代表我们实现的Promise
new MyPromise(function(resolve, reject) { // 我们实现的Promise
  setTimeout(function() {
    resolve(42)
  }, 2000)
}).then(function() {
  return new Promise.reject(2) // ES6的Promise
}).then(function() {
  return Q.all([ // Q的Promise
    new MyPromise(resolve=>resolve(8)), // 我们实现的Promise
    new Promise.resolve(9), // ES6的Promise
    Q.resolve(9) // Q的Promise
  ])
})
```

我们前面实现的代码并没有处理这样的逻辑，我们只判断了onResolved/onRejected的返回值是否为我们实现的Promise的实例，并没有做任何其它的判断，所以上面这样的代码目前是没有办法在我们的Promise里正确运行的。

下面这样的代码目前也是没办法处理的：

```js
new Promise(resolve=>resolve(8))
  .then()
  .then()
  .then(function foo(value) {
    alert(value)
  })
```

正确的行为应该是alert出8，而如果拿我们的Promise，运行上述代码，将会alert出undefined。这种行为称为穿透，即8这个值会穿透两个then\(说Promise更为准确\)到达最后一个then里的foo函数里，成为它的实参，最终将会alert出8。

下面我们首先处理简单的情况，值的穿透

### 2.3 Promise值的穿透 {#2.3Promise值的穿透}

通过观察，会发现我们希望下面这段代码

```js
new Promise(resolve=>resolve(8))
  .then()
  .catch()
  .then(function(value) {
    alert(value)
  })
```

跟下面这段代码的行为是一样的

```js
new Promise(resolve=>resolve(8))
  .then(function(value){
    return value
  })
  .catch(function(reason){
    throw reason
  })
  .then(function(value) {
    alert(value)
  })
```

所以如果想要把then的实参留空且让值可以穿透到后面，意味着then的两个参数的默认值分别为function\(value\) {return value}，function\(reason\) {throw reason}。

所以我们只需要把then里判断onResolved和onRejected的部分改成如下即可：

```js
onResolved = typeof onResolved === 'function' ? onResolved : function(value) {return value}
onRejected = typeof onRejected === 'function' ? onRejected : function(reason) {throw reason}
```

于是Promise神奇的值的穿透也没有那么黑魔法，只不过是then默认参数就是把值往后传或者抛

### 2.4 不同Promise的交互 {#2.4不同Promise的交互}

关于不同Promise间的交互，其实标准里是有说明的，其中详细指定了如何通过then的实参返回的值来决定promise2的状态，我们只需要按照标准把标准的内容转成代码即可。

这里简单解释一下标准：

> 即我们要把onResolved/onRejected的返回值，x，当成一个可能是Promise的对象，也即标准里所说的thenable，并以最保险的方式调用x上的then方法，如果大家都按照标准实现，那么不同的Promise之间就可以交互了。而标准为了保险起见，即使x返回了一个带有then属性但并不遵循Promise标准的对象（比如说这个x把它then里的两个参数都调用了，同步或者异步调用（PS，原则上then的两个参数需要异步调用，下文会讲到），或者是出错后又调用了它们，或者then根本不是一个函数），也能尽可能正确处理。

关于为何需要不同的Promise实现能够相互交互，我想原因应该是显然的，Promise并不是JS一早就有的标准，不同第三方的实现之间是并不相互知晓的，如果你使用的某一个库中封装了一个Promise实现，想象一下如果它不能跟你自己使用的Promise实现交互的场景。。。

建议各位对照着标准阅读以下代码，因为标准对此说明的非常详细，所以你应该能够在任意一个Promise实现中找到类似的代码：

```js
/*
resolvePromise函数即为根据x的值来决定promise2的状态的函数
也即标准中的[Promise Resolution Procedure](https://promisesaplus.com/#point-47)
x为`promise2 = promise1.then(onResolved, onRejected)`里`onResolved/onRejected`的返回值
`resolve`和`reject`实际上是`promise2`的`executor`的两个实参，因为很难挂在其它的地方，所以一并传进来。
相信各位一定可以对照标准把标准转换成代码，这里就只标出代码在标准中对应的位置，只在必要的地方做一些解释
*/
function resolvePromise(promise2, x, resolve, reject) {
  var then
  var thenCalledOrThrow = false
  if (promise2 === x) { // 对应标准2.3.1节
    return reject(new TypeError('Chaining cycle detected for promise!'))
  }
  if (x instanceof Promise) { // 对应标准2.3.2节
    // 如果x的状态还没有确定，那么它是有可能被一个thenable决定最终状态和值的
    // 所以这里需要做一下处理，而不能一概的以为它会被一个“正常”的值resolve
    if (x.status === 'pending') {
      x.then(function(value) {
        resolvePromise(promise2, value, resolve, reject)
      }, reject)
    } else { // 但如果这个Promise的状态已经确定了，那么它肯定有一个“正常”的值，而不是一个thenable，所以这里直接取它的状态
      x.then(resolve, reject)
    }
    return
  }
  if ((x !== null) && ((typeof x === 'object') || (typeof x === 'function'))) { // 2.3.3
    try {
      // 2.3.3.1 因为x.then有可能是一个getter，这种情况下多次读取就有可能产生副作用
      // 即要判断它的类型，又要调用它，这就是两次读取
      then = x.then 
      if (typeof then === 'function') { // 2.3.3.3
        then.call(x, function rs(y) { // 2.3.3.3.1
          if (thenCalledOrThrow) return // 2.3.3.3.3 即这三处谁选执行就以谁的结果为准
          thenCalledOrThrow = true
          return resolvePromise(promise2, y, resolve, reject) // 2.3.3.3.1
        }, function rj(r) { // 2.3.3.3.2
          if (thenCalledOrThrow) return // 2.3.3.3.3 即这三处谁选执行就以谁的结果为准
          thenCalledOrThrow = true
          return reject(r)
        })
      } else { // 2.3.3.4
        resolve(x)
      }
    } catch (e) { // 2.3.3.2
      if (thenCalledOrThrow) return // 2.3.3.3.3 即这三处谁选执行就以谁的结果为准
      thenCalledOrThrow = true
      return reject(e)
    }
  } else { // 2.3.4
    resolve(x)
  }
}
```

然后我们使用这个函数的调用替换then里几处判断x是否为Promise对象的位置即可，见下方完整代码。

最后，我们刚刚说到，原则上，promise.then\(onResolved, onRejected\)里的这两相函数需要异步调用，关于这一点，标准里也有[说明](http://link.zhihu.com/?target=https%3A//promisesaplus.com/%23point-67)：

> In practice, this requirement ensures that onFulfilled and onRejected execute asynchronously, after the event loop turn in which then is called, and with a fresh stack.

所以我们需要对我们的代码做一点变动，即在四个地方加上setTimeout\(fn, 0\)，这点会在完整的代码中注释，请各位自行发现。

事实上，即使你不参照标准，最终你在自测试时也会发现如果then的参数不以异步的方式调用，有些情况下Promise会不按预期的方式行为，通过不断的自测，最终你必然会让then的参数异步执行，让executor函数立即执行。本人在一开始实现Promise时就没有参照标准，而是自己凭经验测试，最终发现的这个问题。

至此，我们就实现了一个的Promise，完整代码如下：

```js
try {
  module.exports = Promise
} catch (e) {}
function Promise(executor) {
  var self = this
  self.status = 'pending'
  self.onResolvedCallback = []
  self.onRejectedCallback = []
  function resolve(value) {
    if (value instanceof Promise) {
      return value.then(resolve, reject)
    }
    setTimeout(function() { // 异步执行所有的回调函数
      if (self.status === 'pending') {
        self.status = 'resolved'
        self.data = value
        for (var i = 0; i < self.onResolvedCallback.length; i++) {
          self.onResolvedCallback[i](value)
        }
      }
    })
  }
  function reject(reason) {
    setTimeout(function() { // 异步执行所有的回调函数
      if (self.status === 'pending') {
        self.status = 'rejected'
        self.data = reason
        for (var i = 0; i < self.onRejectedCallback.length; i++) {
          self.onRejectedCallback[i](reason)
        }
      }
    })
  }
  try {
    executor(resolve, reject)
  } catch (reason) {
    reject(reason)
  }
}
function resolvePromise(promise2, x, resolve, reject) {
  var then
  var thenCalledOrThrow = false
  if (promise2 === x) {
    return reject(new TypeError('Chaining cycle detected for promise!'))
  }
  if (x instanceof Promise) {
    if (x.status === 'pending') { //because x could resolved by a Promise Object
      x.then(function(v) {
        resolvePromise(promise2, v, resolve, reject)
      }, reject)
    } else { //but if it is resolved, it will never resolved by a Promise Object but a static value;
      x.then(resolve, reject)
    }
    return
  }
  if ((x !== null) && ((typeof x === 'object') || (typeof x === 'function'))) {
    try {
      then = x.then //because x.then could be a getter
      if (typeof then === 'function') {
        then.call(x, function rs(y) {
          if (thenCalledOrThrow) return
          thenCalledOrThrow = true
          return resolvePromise(promise2, y, resolve, reject)
        }, function rj(r) {
          if (thenCalledOrThrow) return
          thenCalledOrThrow = true
          return reject(r)
        })
      } else {
        resolve(x)
      }
    } catch (e) {
      if (thenCalledOrThrow) return
      thenCalledOrThrow = true
      return reject(e)
    }
  } else {
    resolve(x)
  }
}
Promise.prototype.then = function(onResolved, onRejected) {
  var self = this
  var promise2
  onResolved = typeof onResolved === 'function' ? onResolved : function(v) {
    return v
  }
  onRejected = typeof onRejected === 'function' ? onRejected : function(r) {
    throw r
  }
  if (self.status === 'resolved') {
    return promise2 = new Promise(function(resolve, reject) {
      setTimeout(function() { // 异步执行onResolved
        try {
          var x = onResolved(self.data)
          resolvePromise(promise2, x, resolve, reject)
        } catch (reason) {
          reject(reason)
        }
      })
    })
  }
  if (self.status === 'rejected') {
    return promise2 = new Promise(function(resolve, reject) {
      setTimeout(function() { // 异步执行onRejected
        try {
          var x = onRejected(self.data)
          resolvePromise(promise2, x, resolve, reject)
        } catch (reason) {
          reject(reason)
        }
      })
    })
  }
  if (self.status === 'pending') {
    // 这里之所以没有异步执行，是因为这些函数必然会被resolve或reject调用，而resolve或reject函数里的内容已是异步执行，构造函数里的定义
    return promise2 = new Promise(function(resolve, reject) {
      self.onResolvedCallback.push(function(value) {
        try {
          var x = onResolved(value)
          resolvePromise(promise2, x, resolve, reject)
        } catch (r) {
          reject(r)
        }
      })
      self.onRejectedCallback.push(function(reason) {
          try {
            var x = onRejected(reason)
            resolvePromise(promise2, x, resolve, reject)
          } catch (r) {
            reject(r)
          }
        })
    })
  }
}
Promise.prototype.catch = function(onRejected) {
  return this.then(null, onRejected)
}
Promise.deferred = Promise.defer = function() {
  var dfd = {}
  dfd.promise = new Promise(function(resolve, reject) {
    dfd.resolve = resolve
    dfd.reject = reject
  })
  return dfd
}
```

## 三、测试 {#三、测试}

如何确定我们实现的Promise符合标准呢？Promise有一个配套的测试脚本，只需要我们在一个CommonJS的模块中暴露一个deferred方法（即exports.deferred方法），就可以了，代码见上述代码的最后。然后执行如下代码即可执行测试：

```js
npm i -g promises-aplus-tests
promises-aplus-tests Promise.js
```

## 四、关于Promise的其它问题 {#四、关于Promise的其它问题}

### 4.1 Promise的性能问题 {#4.1Promise的性能问题}

可能各位看官会觉得奇怪，Promise能有什么性能问题呢？并没有大量的计算啊，几乎都是处理逻辑的代码。

理论上说，不能叫做“性能问题”，而只是有可能出现的延迟问题。什么意思呢，记得刚刚我们说需要把4块代码包在setTimeout里吧，先考虑如下代码：

```js
var start = +new Date()
function foo() {
  setTimeout(function() {
    console.log('setTimeout')
    if((+new Date) - start < 1000) {
      foo()
    }
  })
}
foo()
```

运行上面的代码，会打印出多少次'setTimeout'呢，各位可以自己试一下，不出意外的话，应该是250次左右，我刚刚运行了一次，是241次。这说明，上述代码中两次setTimeout运行的时间间隔约是4ms（另外，setInterval也是一样的），实事上，这正是浏览器两次Event Loop之间的时间间隔，相关标准各位可以自行查阅。另外，在Node中，这个时间间隔跟浏览器不一样，经过我的测试，是1ms。

单单一个4ms的延迟可能在一般的web应用中并不会有什么问题，但是考虑极端情况，我们有20个Promise链式调用，加上代码运行的时间，那么这个链式调用的第一行代码跟最后一行代码的运行很可能会超过100ms，如果这之间没有对UI有任何更新的话，虽然本质上没有什么性能问题，但可能会造成一定的卡顿或者闪烁，虽然在web应用中这种情形并不常见，但是在Node应用中，确实是有可能出现这样的case的，所以一个能够应用于生产环境的实现有必要把这个延迟消除掉。在Node中，我们可以调用process.nextTick或者setImmediate（[Q就是这么做的](http://link.zhihu.com/?target=https%3A//github.com/kriskowal/q/blob/v1/q.js%23L101)），在浏览器中具体如何做，已经超出了本文的讨论范围，总的来说，就是我们需要实现一个函数，行为跟setTimeout一样，但它需要异步且尽早的调用所有已经加入队列的函数，[这里](http://link.zhihu.com/?target=http%3A//www.bluejava.com/4NS/Speed-up-your-Websites-with-a-Faster-setTimeout-using-soon)有一个实现。

### 4.2 如何停止一个Promise链？ {#4.2如何停止一个Promise链？}

在一些场景下，我们可能会遇到一个较长的Promise链式调用，在某一步中出现的错误让我们完全没有必要去运行链式调用后面所有的代码，类似下面这样（此处略去了then/catch里的函数）：

```js
new Promise(function(resolve, reject) {
  resolve(42)
})
  .then(function(value) {
    // "Big ERROR!!!"
  })
  .catch()
  .then()
  .then()
  .catch()
  .then()
```

假设这个Big ERROR!!!的出现让我们完全没有必要运行后面所有的代码了，但链式调用的后面即有catch，也有then，无论我们是return还是throw，都不可避免的会进入某一个catch或then里面，那有没有办法让这个链式调用在Big ERROR!!!的后面就停掉，完全不去执行链式调用后面所有回调函数呢？

一开始遇到这个问题的时候我也百思不得其解，在网上搜遍了也没有结果，有人说可以在每个catch里面判断Error的类型，如果自己处理不了就接着throw，也有些其它办法，但总是要对现有代码进行一些改动并且所有的地方都要遵循这些约定，甚是麻烦。

然而当我从一个实现者的角度看问题时，确实找到了答案，就是在发生Big ERROR后return一个Promise，但这个Promise的executor函数什么也不做，这就意味着这个Promise将永远处于pending状态，由于then返回的Promise会直接取这个永远处于pending状态的Promise的状态，于是返回的这个Promise也将一直处于pending状态，后面的代码也就一直不会执行了，具体代码如下：

```js
new Promise(function(resolve, reject) {
  resolve(42)
})
  .then(function(value) {
    // "Big ERROR!!!"
    return new Promise(function(){})
  })
  .catch()
  .then()
  .then()
  .catch()
  .then()
```

这种方式看起来有些山寨，它也确实解决了问题。但它引入的一个新问题就是链式调用后面的所有回调函数都无法被垃圾回收器回收（在一个靠谱的实现里，Promise应该在执行完所有回调后删除对所有回调函数的引用以让它们能被回收，在前文的实现里，为了减少复杂度，并没有做这种处理），但如果我们不使用匿名函数，而是使用函数定义或者函数变量的话，在需要多次执行的Promise链中，这些函数也都只有一份在内存中，不被回收也是可以接受的。

我们可以将返回一个什么也不做的Promise封装成一个有语义的函数，以增加代码的可读性：

```js
Promise.cancel = Promise.stop = function() {
  return new Promise(function(){})
}
```

然后我们就可以这么使用了：

```js
new Promise(function(resolve, reject) {
  resolve(42)
})
  .then(function(value) {
    // "Big ERROR!!!"
    return Promise.stop()
  })
  .catch()
  .then()
  .then()
  .catch()
  .then()
```

看起来是不是有语义的多？

关于停掉Promise链这个问题，我后面找到了更好的解决办法，也整理出了另一篇文章，详见这里：[从如何停掉Promise链说起](http://link.zhihu.com/?target=https%3A//github.com/xieranmaya/blog/issues/5)

## 五、最佳实践 {#五、最佳实践}

这里不免再啰嗦两句最佳实践

一是不要把Promise写成嵌套结构，至于怎么改进，这里就不多说了

```js
// 错误的写法
promise1.then(function(value) {
  promise1.then(function(value) {
    promise1.then(function(value) {
    })
  })
})
```

二是链式Promise要返回一个Promise，而不只是构造一个Promise

```js
// 错误的写法
Promise.resolve(1).then(function(){
  Promise.resolve(2)
}).then(function(){
  Promise.resolve(3)
})
```

---

原文：[史上最易读懂的 Promise/A+ 完全实现](https://zhuanlan.zhihu.com/p/21834559)







