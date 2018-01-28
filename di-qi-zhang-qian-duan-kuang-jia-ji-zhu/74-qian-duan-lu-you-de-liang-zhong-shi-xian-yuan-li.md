# 第4节 前端路由的两种实现原理

早期的路由都是后端实现的，直接根据 url 来 reload 页面，页面变得越来越复杂服务器端压力变大，随着 ajax 的出现，页面实现非 reload 就能刷新数据，也给前端路由的出现奠定了基础。我们可以通过记录 url 来记录 ajax 的变化，从而实现前端路由。

本文主要讲两种主流方式实现前端路由。

## 一、History API

这里不细说每一个 API 的用法，大家可以看 MDN 的文档：[https://developer.mozilla.org…](https://developer.mozilla.org/en-US/docs/Web/API/History)

重点说其中的两个新增的API history.pushState 和 history.replaceState

```js
history.pushState(state, title, url);
```

这两个 API 都接收三个参数，分别是：

* **状态对象（state object）** — 一个JavaScript对象，与用pushState\(\)方法创建的新历史记录条目关联。无论何时用户导航到新创建的状态，popstate事件都会被触发，并且事件对象的state属性都包含历史记录条目的状态对象的拷贝。
* **标题（title）** — FireFox浏览器目前会忽略该参数，虽然以后可能会用上。考虑到未来可能会对该方法进行修改，传一个空字符串会比较安全。或者，你也可以传入一个简短的标题，标明将要进入的状态。
* **地址（URL**） — 新的历史记录条目的地址。浏览器不会在调用pushState\(\)方法后加载该地址，但之后，可能会试图加载，例如用户重启浏览器。新的URL不一定是绝对路径；如果是相对路径，它将以当前URL为基准；传入的URL与当前URL应该是同源的，否则，pushState\(\)会抛出异常。该参数是可选的；不指定的话则为文档当前URL。

相同之处是两个 API 都会操作浏览器的历史记录，而不会引起页面的刷新。

不同之处在于，pushState会增加一条新的历史记录，而replaceState则会替换当前的历史记录。

我们拿大百度的控制台举例子（具体说是我的浏览器在百度首页打开控制台。。。）我们在控制台输入

```js
window.history.pushState(null, null, "https://www.baidu.com/?name=orange");
```

好，我们观察此时的 url 变成了这样 

![](/assets/History.png)我们这里不一一测试，直接给出其它用法，大家自行尝试

```js
window.history.pushState(null, null, "https://www.baidu.com/name/orange");
//url: https://www.baidu.com/name/orange
window.history.pushState(null, null, "?name=orange");
//url: https://www.baidu.com?name=orange
window.history.pushState(null, null, "name=orange");
//url: https://www.baidu.com/name=orange
window.history.pushState(null, null, "/name/orange");
//url: https://www.baidu.com/name/orange
window.history.pushState(null, null, "name/orange");
//url: https://www.baidu.com/name/orange
```

> 注意:这里的 url 不支持跨域，当我们把 www.baidu.com 换成 baidu.com 时就会报错。

回到上面例子中，每次改变 url 页面并没有刷新，同样根据上文所述，浏览器会产生历史记录 ![](/assets/History1.png)

## 二、状态对象



这就是实现页面无刷新情况下改变 url 的前提，下面我们说下第一个参数 **状态对象**

如果运行 history.pushState\(\) 方法，历史栈对应的纪录就会存入 状态对象，我们可以随时主动调用历史条目，此处引用 mozilla 的例子：

```js
<!DOCTYPE HTML>
<!-- this starts off as http://example.com/line?x=5 -->
<title>Line Game - 5</title>
<p>You are at coordinate <span id="coord">5</span> on the line.</p>
<p>
 <a href="?x=6" onclick="go(1); return false;">Advance to 6</a> or
 <a href="?x=4" onclick="go(-1); return false;">retreat to 4</a>?
</p>
<script>
 var currentPage = 5; // prefilled by server！！！！
 function go(d) {
     setupPage(currentPage + d);
     history.pushState(currentPage, document.title, '?x=' + currentPage);
 }
 onpopstate = function(event) {
     setupPage(event.state);
 }
 function setupPage(page) {
     currentPage = page;
     document.title = 'Line Game - ' + currentPage;
     document.getElementById('coord').textContent = currentPage;
     document.links[0].href = '?x=' + (currentPage+1);
     document.links[0].textContent = 'Advance to ' + (currentPage+1);
     document.links[1].href = '?x=' + (currentPage-1);
     document.links[1].textContent = 'retreat to ' + (currentPage-1);
 }
</script>
```

我们点击 Advance to ？ 对应的 url 与模版都会 +1，反之点击 retreat to ？ 就会都 -1，这就满足了 url 与模版视图同时变化的需求

实际当中我们不需要去模拟 onpopstate 事件，官方文档提供了 popstate 事件，当我们在历史记录中切换时就会产生 popstate 事件。对于触发 popstate 事件的方式，各浏览器实现也有差异，我们可以根据不同浏览器做兼容处理。

## 三、hash

我们经常在 url 中看到 \#，这个 \#有两种情况，一个是我们所谓的锚点，比如典型的回到顶部按钮原理、Github 上各个标题之间的跳转等，路由里的 \# 不叫锚点，我们称之为 hash，大型框架的路由系统大多都是哈希实现的。

同样我们需要一个根据监听哈希变化触发的事件 —— hashchange 事件

我们用 window.location 处理哈希的改变时不会重新渲染页面，而是当作新页面加到历史记录中，这样我们跳转页面就可以在 hashchange 事件中注册 ajax 从而改变页面内容。

这里我在 codepen 上模拟了一下原理:[ http://codepen.io/orangexc/pe…](http://codepen.io/orangexc/pen/LRrxvP)

hashchange 在低版本 IE 需要通过轮询监听 url 变化来实现，我们可以模拟如下

```js
(function(window) {
  // 如果浏览器不支持原生实现的事件，则开始模拟，否则退出。
  if ( "onhashchange" in window.document.body ) { return; }
  var location = window.location,
  oldURL = location.href,
  oldHash = location.hash;
  // 每隔100ms检查hash是否发生变化
  setInterval(function() {
    var newURL = location.href,
    newHash = location.hash;
    // hash发生变化且全局注册有onhashchange方法（这个名字是为了和模拟的事件名保持统一）；
    if ( newHash != oldHash && typeof window.onhashchange === "function" ) {
      // 执行方法
      window.onhashchange({
        type: "hashchange",
        oldURL: oldURL,
        newURL: newURL
      });
      oldURL = newURL;
      oldHash = newHash;
    }
  }, 100);
})(window);
```

大型框架的路由当然不会这么简单，angular 1.x 的路由对哈希、模版、处理器进行关联，大致如下

```js
app.config(['$routeProvider', '$locationProvider', function ($routeProvider, $locationProvider) {
 $routeProvider
 .when('/article', {
   templateUrl: '/article.html',
   controller: 'ArticleController'
 }).otherwise({
   redirectTo: '/index'
 });
 $locationProvider.html5Mode(true);
}])
```

这套路由方案默认是以 \# 开头的哈希方式，如果不考虑低版本浏览器，就可以直接调用 $locationProvider.html5Mode\(true\) 利用 H5 的方案而不用哈希方案。

## 总结

两种方案我推荐 hash 方案，因为照顾到低级浏览器，就是不美观（多了一个 \#），两者兼顾也不是不可，只能判断浏览器给出对应方案啦，不过也只支持 IE8+，更低版本兼容见上文！

这个链接的 demo 含有判断方法：[http://sandbox.runjs.cn/show/… ](http://sandbox.runjs.cn/show/dxi5lgcx)。同时给出 Github 仓库地址: [minrouter](https://github.com/cheft/minrouter)，推荐大家读下源码，仅仅 117 行，精辟！

如果在上面链接测试时你的 url 里多了一个 \#，说明你的浏览器该更新啦。

