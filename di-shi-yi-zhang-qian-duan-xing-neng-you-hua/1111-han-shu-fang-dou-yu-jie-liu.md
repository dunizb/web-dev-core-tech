# 第11节 函数防抖与节流

## 一、函数节流的原理

函数节流的原理挺简单的，估计大家都想到了，那就是定时器。当我触发一个时间时，先setTimout让这个事件延迟一会再执行，如果在这个时间间隔内又触发了事件，那我们就clear掉原来的定时器，再setTimeout一个新的定时器延迟一会执行，就这样。

在浏览器中，频繁的DOM操作非常消耗内存和CPU时间，比如监听了resize,touchmove,scroll...等事件，在dom改变时都会不断触发回调。现在的react 和 vue 等前端框架都提出了虚拟DOM的概念，会把多次DOM操作合并到一次真实操作中，就是使用了Diff算法，这样就大大减低了DOM操作的频次。但是，这里并不是要讨论diff算法，如果感兴趣可以戳上面的链接，而是解释如何利用setTimeout来减低DOM频繁操作的风险。

## 二、简单实现

明白了原理，那就可以在代码里用上了，但每次都要手动去新建清除定时器毕竟麻烦，于是需要封装。在《JavaScript高级程序设计》一书有介绍函数节流，里面封装了这样一个函数节流函数：
```js
function throttle(method, context) {
     clearTimeout(methor.tId);
     method.tId = setTimeout(function(){
         method.call(context);
     }， 100);
 }
```

它把定时器ID存为函数的一个属性（= =个人的世界观不喜欢这种写法）。而调用的时候就直接写
```js
window.onresize = function(){
    throttle(myFunc);
}
```

这样两次函数调用之间至少间隔100ms。

而impress用的是另一个封装函数：
```js
var throttle = function(fn, delay){
 	var timer = null;
 	return function(){
 		var context = this, args = arguments;
 		clearTimeout(timer);
 		timer = setTimeout(function(){
 			fn.apply(context, args);
 		}, delay);
 	};
 };
```

它使用闭包的方法形成一个私有的作用域来存放定时器变量timer。而调用方法为
```js
window.onresize = throttle(myFunc, 100);
```

两种方法各有优劣，前一个封装函数的优势在把上下文变量当做函数参数，直接可以定制执行函数的this变量；后一个函数优势在于把延迟时间当做变量（当然，前一个函数很容易做这个拓展），而且个人觉得使用闭包代码结构会更优，且易于拓展定制其他私有变量，缺点就是虽然使用apply把调用throttle时的this上下文传给执行函数，但毕竟不够灵活。

## 三、优化

函数节流让一个函数只有在你不断触发后停下来歇会才开始执行，中间你操作得太快它直接无视你。这样做就有点太绝了。resize一般还好，但假如你写一个拖拽元素位置的程序，然后直接使用函数节流，那恭喜你，你会发现你拖动时元素是不动的，你拖完了，它直接闪到终点去。

**其实函数节流的出发点，就是让一个函数不要执行得太频繁，减少一些过快的调用来节流。**当你改变浏览器大小，浏览器触发resize事件的时间间隔是多少？我不清楚，个人猜测是16ms（每秒64次），反正跟mousemove一样非常太频繁，一个很小的时间段内必定执行，这是浏览器设好的，你无法直接改。而真正的节流应该是在可接受的范围内尽量延长这个调用时间，也就是我们自己控制这个执行频率，让函数减少调用以达到减少计算、提升性能的目的。假如原来是16ms执行一次，我们如果发现resize时每50ms一次也可以接受，那肯定用50ms做时间间隔好一点。

而上面介绍的函数节流，它这个频率就不是50ms之类的，它就是无穷大，只要你能不间断resize，刷个几年它也一次都不执行处理函数。我们可以对上面的节流函数做拓展：
```js
var throttleV2 = function(fn, delay, mustRunDelay){
 	var timer = null;
 	var t_start;
 	return function(){
 		var context = this, args = arguments, t_curr = +new Date();
 		clearTimeout(timer);
 		if(!t_start){
 			t_start = t_curr;
 		}
 		if(t_curr - t_start >= mustRunDelay){
 			fn.apply(context, args);
 			t_start = t_curr;
 		}
 		else {
 			timer = setTimeout(function(){
 				fn.apply(context, args);
 			}, delay);
 		}
 	};
 };
 ```
 
在这个拓展后的节流函数升级版，我们可以设置第三个参数，即必然触发执行的时间间隔。如果用下面的方法调用
```js
window.onresize = throttleV2(myFunc, 50, 100);
```

则意味着，50ms的间隔内连续触发的调用，后一个调用会把前一个调用的等待处理掉，但每隔100ms至少执行一次。原理也很简单，打时间tag，一开始记录第一次调用的时间戳，然后每次调用函数都去拿最新的时间跟记录时间比，超出给定的时间就执行一次，更新记录时间。

[狠击这里查看测试页面](http://www.alloyteam.com/wp-content/uploads/2012/11/throttle-test.html)

到现在为止呢，当我们在开发中遇到类似的问题，一个函数可能非常频繁地调用，我们有了几个选择：

* 一呢，还是用原来的写法，频繁执行就频繁执行吧，哥的电脑好；
* 二是用原始的函数节流；
* 三则是用函数节流升级版。

不是说第一种就不好，这要看实际项目的要求，有些就是对实时性要求高。而如果要求没那么苛刻，我们可以视具体情况使用第二种或第三种方法，理论上第二种方法执行的函数调用最少，性能应该节省最多，而第三种方法则更加地灵活，你可以在性能与体验上探索一个平衡点。
 
## 四、第三方库的实现

具体实现代码，看下 underscore.js中的 _.debounce 

语法：
```js
_.debounce(function, wait, [immediate])
```

源码：
```js
// Returns a function, that, as long as it continues to be invoked, will not
// be triggered. The function will be called after it stops being called for
// N milliseconds. If `immediate` is passed, trigger the function on the
// leading edge, instead of the trailing.
_.debounce = function(func, wait, immediate) {
  var timeout, args, context, timestamp, result;

  var later = function() {
 var last = _.now() - timestamp;

 if (last < wait && last >= 0) {
   timeout = setTimeout(later, wait - last);
 } else {
   timeout = null;
   if (!immediate) {
     result = func.apply(context, args);
     if (!timeout) context = args = null;
   }
 }
  };

  return function() {
   context = this;
   args = arguments;
   timestamp = _.now();
   var callNow = immediate && !timeout;
   if (!timeout) timeout = setTimeout(later, wait);
   if (callNow) {
     result = func.apply(context, args);
     context = args = null;
   }

   return result;
  };
};
```

`wait`参数代表`debounce`时间， `_.now()`返回当前时间的时间戳，同样以`ms` 为单位。 如果传入了 `immediate`,会立即触发回调函数。

******

**参考文章**

* [浅谈JavaScript函数节流](http://www.alloyteam.com/2012/11/javascript-throttle/)
* [高级函数技巧-函数防抖与节流](https://segmentfault.com/a/1190000012493043)






