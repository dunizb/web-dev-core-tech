# 第1节 前端跨域技术之JSONP、反向代理、CORS

网上说了很多很多，但是看完之后还是很混乱，所以我自己重新总结一下。

## 一、跨域技术有哪些

解决 js 跨域问题一共有8种方法

1. jsonp（只支持 get）
2. 反向代理
3. CORS
4. document.domain + iframe 跨域
5. window.name + iframe 跨域
6. window.postMessage
7. location.hash + iframe
8. web sockets

各个方法都有各自的优缺点，但是目前前端开发方面比较常用的是 jsonp，反向代理，CORS

**CORS**是跨源资源分享（Cross-Origin Resource Sharing）的缩写。它是W3C标准，是跨源AJAX请求的根本解决方法。优点是正统，符合标准，缺点是：但是需要服务器端配合，比较麻烦。

**JSONP** 优点是对旧式浏览器支持较好，缺点是：

* 只支持 get 请求
* 有安全问题\(请求代码中可能存在安全隐患\)。
* 要确定jsonp请求是否失败并不容易

**反向代理**都能够兼容以上的缺点，但是仅仅作为前端开发模式的时候使用，在正式上线环境较少用到。

* 因为开发环境的域名跟线上环境不一样才需要这样处理。
* 如果线上环境太复杂，本身也是多域（后面说到的同源策略问题，多子域，或者多端口问题），那么需要采用 jsonp 或者 CORS 来处理。

这里主要说明这三种方式。其他方式暂不说明。

## 二、什么是跨域问题

### 2.1 跨域错误提示

跨域问题一般只出现在前端开发中使用 javascript 进行网络请求的时候，浏览器为了安全访问网络请求的数据而进行的限制。

提示的错误大致如下：

```
No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://XXXXXX' is therefore not allowed access.
```

![](/assets/cors1.png)

### 2.2 同源策略

因为浏览器收到**同源策略**的限制，当前域名的js只能读取同域下的窗口属性。

同源指的是**三个源头同时相同**：**协议相同、域名相同、端口相同**

举例来说，[http://www.example.com/dir/page.html](http://www.example.com/dir/page.html) 这个网址

```
协议是 http://
域名是 www.example.com
端口是80 

//它的同源情况如下：
http://www.example.com/dir2/other.html：同源
http://example.com/dir/other.html：不同源（域名不同）
http://v2.www.example.com/dir/other.html：不同源（域名不同）
http://www.example.com:81/dir/other.html：不同源（端口不同）
```

同源策略限制了以下行为：

* Cookie、LocalStorage 和 IndexDB 无法读取
* DOM 和 JS 对象无法获取
* Ajax请求发送不出去

详细的同源策略相关，可以参考：[http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)

## 三、反向代理方式解决跨域问题

反向代理和正向代理的区别：

* 正向代理（Forward Proxy），通常都被简称为代理，就是在用户无法正常访问外部资源，比方说受到GFW的影响无法访问twitter的时候，我们可以通过代理的方式，让用户绕过防火墙，从而连接到目标网络或者服务。
* 反向代理（Reverse Proxy）是指以代理服务器来接受 Internet 上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给 Internet 请求连接的客户端，此时，代理服务器对外就表现为一个服务器。

那么我们可以理解为反向代理

如何使用反向代理服务器来达到跨域问题解决：

* 前端ajax请求的是本地反向代理服务器
* 本地反向代理服务器接收到后：
  * 修改请求的 http-header 信息，例如 referer，host，端口等
  * 修改后将请求发送到实际的服务器
* 修改后将请求发送到实际的服务器

现在前端开发一般使用 nodejs来做本地反向代理服务器

```js
// 在 express 之后引入路由
var app = express();

var apiRoutes = express.Router();

app.use(bodyParser.urlencoded({extended:false}))

// 自定义 api 路由
apiRoutes.get("/lyric", function (req, res) {
  var url = "https://c.y.qq.com/lyric/fcgi-bin/fcg_query_lyric_new.fcg";

  axios.get(url, {
    headers: { // 修改 header
      referer: "https://c.y.qq.com/",
      host: "c.y.qq.com"
    },
    params: req.query
  }).then((response) => {
    var ret = response.data
    if (typeof ret === "string") {
      var reg = /^\w+\(({[^()]+})\)$/;
      var matches = ret.match(reg);
      if (matches) {
        ret = JSON.parse(matches[1])
      }
    }
    res.json(ret)
  }).catch((e) => {
    console.log(e)
  })
});

// 使用这个路由
app.use("/api", apiRoutes);
```

## 四、JSONP方式解决跨域问题

JSONP 是 JSON 的一种使用模式，可以解决主流浏览器的跨域数据访问问题。其原理是根据 `XmlHttpRequest` 对象受到同源策略的影响，而 &lt;script&gt; 标签元素却不受同源策略影响，可以加载跨域服务器上的脚本，网页可以从其他来源动态产生 JSON 资料。用 JSONP 获取的不是 JSON 数据，而是可以直接运行的 JavaScript 语句。

> JSONP有些文章会叫动态创建script，因为他确实是动态写入 script 标签的内容从而达到跨域的效果。

JSONP（JSON with Padding）是数据格式JSON的一种“使用模式”。

JSONP 只能用 get 方式。

### 4.1 使用 jQuery 集成的 $.ajax 实现 JSONP 跨域调用

我们先从简单的实现开始，利用 jQuery 中的 `$.ajax` 来实现上诉的跨域调用。

我们将 服务器 3000 上的请求页面的 JavaScript 代码改为：

```js
// 回调函数
function jsonpCallback(data) {
    console.log("jsonpCallback: " + data.name)
}
$("#submit").click(function() {
    var data = {
        name: $("#name").val(),
        id: $("#id").val()
    };
    $.ajax({
        url: 'http://localhost:3001/ajax/deal',
        data: data,
        dataType: 'jsonp',
        cache: false,
        timeout: 5000,
        // jsonp 字段含义为服务器通过什么字段获取回调函数的名称
        jsonp: 'callback',
        // 声明本地回调函数的名称，jquery 默认随机生成一个函数名称
        jsonpCallback: 'jsonpCallback',
        success: function(data) {
            console.log("ajax success callback: " + data.name)
        },
        error: function(jqXHR, textStatus, errorThrown) {
            console.log(textStatus + ' ' + errorThrown);
        }
    });
});
```

服务器 3001 上对应的处理函数为：

```js
app.get('/ajax/deal', function(req, res) {
    console.log("server accept: ", req.query.name, req.query.id)
    var data = "{" + "name:'" + req.query.name + " - server 3001 process'," + "id:'" + req.query.id + " - server 3001 process'" + "}"
    var callback = req.query.callback
    var jsonp = callback + '(' + data + ')'
    console.log(jsonp)
    res.send(jsonp)
    res.end()
})
```

这里一定要注意 data 中字符串拼接，不能直接将 JSON 格式的 data 直接传给回调函数，否则会发生编译错误： parsererror Error: jsonpCallback was not called。

其实脑海里应该有一个概念：利用 JSONP 格式返回的值一段要立即执行的 JavaScript 代码，所以不会像 ajax 的 `XmlHttpRequest` 那样可以监听不同事件对数据进行不同处理。

### 4.2 使用 &lt;script&gt; 标签原生实现 JSONP

经过上面的事件，你是不是觉得 JSONP 的实现和 Ajax 大同小异？

其实，由于实现的原理不同，由 JSONP 实现的跨域调用不是通过 `XmlHttpRequset` 对象，而是通过 `script` 标签，所以在实现原理上，JSONP 和 Ajax 已经一点关系都没有了。看上去形式相似只是由于 jQuery 对 JSONP 做了封装和转换。

比如在上面的例子中，我们假设要传输的数据 data 格式如下：

```js
{
    name: "chiaki",
    id": "3001"
}
```

那么数据是如何传输的呢？HTTP 请求头的第一行如下：

```
GET /ajax/deal?callback=jsonpCallback&name=chiaki&id=3001&_=1473164876032 HTTP/1.1
```

可见，即使形式上是用 `POST` 传输一个 JSON 格式的数据，其实发送请求时还是转换成 `GET` 请求。

其实如果理解 JSONP 的原理的话就不难理解为什么只能使用 `GET` 请求方法了。由于是通过 `script` 标签进行请求，所以上述传输过程根本上是以下的形式：

```js
<script src = 'http://localhost:3001/ajax/deal?callback=jsonpCallback&name=chiaki&id=3001&_=1473164876032'></script>
```

这样从服务器返回的代码就可以直接在这个 `script` 标签中运行了。下面我们自己实现一个 JSONP：

服务器 3000请求页面的 JavaScript 代码中，只有回调函数 jsonpCallback：

```js
function jsonpCallback(data) {
    console.log("jsonpCallback: "+data.name)
}
```

服务器 3000请求页面还包含一个 `script` 标签：

```js
<script src = 'http://localhost:3001/jsonServerResponse?jsonp=jsonpCallback'></script>
```

服务器 3001上对应的处理函数：

```js
app.get('/jsonServerResponse', function(req, res) {
    var cb = req.query.jsonp
    console.log(cb)
    var data = 'var data = {' + 'name: $("#name").val() + " - server 3001 jsonp process",' + 'id: $("#id").val() + " - server 3001 jsonp process"' + '};'
    var debug = 'console.log(data);'
    var callback = '$("#submit").click(function() {' + data + cb + '(data);' + debug + '});'
    res.send(callback)
    res.end()
})
```

与上面一样，我们在所获取的参数后面加上 “ - server 3001 jsonp process” 代表服务器对数据的操作。从代码中我么可以看到，处理函数除了根据参数做相应的处理，更多的也是进行字符串的拼接。

### 4.3 JSONP 总结

至此，我们了解了 JSONP 的原理以及实现方式，它帮我们实现前端跨域请求，但是在实践的过程中，我们还是可以发现它的不足：  
  1. 只能使用 GET 方法发起请求，这是由于 script 标签自身的限制决定的。  
  2. 不能很好的发现错误，并进行处理。与 Ajax 对比，由于不是通过 XmlHttpRequest 进行传输，所以不能注册 success、 error 等事件监听函数。

## 五、使用 CORS 实现跨域调用

### 5.1 什么是 CORS ？

Cross-Origin Resource Sharing（CORS）跨域资源共享是一份浏览器技术的规范，提供了 Web 服务从不同域传来沙盒脚本的方法，以避开浏览器的同源策略，是 JSONP 模式的现代版。与 JSONP 不同，CORS 除了 GET 要求方法以外也支持其他的 HTTP 要求。用 CORS 可以让网页设计师用一般的 XMLHttpRequest，这种方式的错误处理比 JSONP 要来的好。另一方面，JSONP 可以在不支持 CORS 的老旧浏览器上运作。现代的浏览器都支持 CORS。

### 5.2 CORS 的实现

还是以 服务器 3000 上的请求页面向 服务器 3001 发送请求为例。

服务器 3000 上的请求页面 JavaScript 不变，如下：

```js
$(function() {
    $("#submit").click(function() {
        var data = {
            name: $("#name").val(),
            id: $("#id").val()
        };
        $.ajax({
            type: 'POST',
            data: data,
            url: 'http://localhost:3001/cors',
            dataType: 'json',
            cache: false,
            timeout: 5000,
            success: function(data) {
                console.log(data)
            },
            error: function(jqXHR, textStatus, errorThrown) {
                console.log('error ' + textStatus + ' ' + errorThrown);
            }
        });
    });
});
```

服务器 3001上对应的处理函数：

```js
app.post('/cors', function(req, res) {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    res.header("Access-Control-Allow-Methods", "PUT,POST,GET,DELETE,OPTIONS");
    res.header("X-Powered-By", ' 3.2.1')
    res.header("Content-Type", "application/json;charset=utf-8");
    var data = {
        name: req.body.name + ' - server 3001 cors process',
        id: req.body.id + ' - server 3001 cors process'
    }
    console.log(data)
    res.send(data)
    res.end()
})
```

在服务器中对返回信息的请求头进行了设置。

## 六、一些其它的跨域调用方式

### 6.1 window.name

window对象有个name属性，该属性有个特征：即在一个窗口 \(window\) 的生命周期内，窗口载入的所有的页面都是共享一个 window.name 的，每个页面对 window.name 都有读写的权限，window.name 是持久存在一个窗口载入过的所有页面中的，并不会因新页面的载入而进行重置。

### 6.2 window.postMessage\(\)

这个方法是 HTML5 的一个新特性，可以用来向其他所有的 window 对象发送消息。需要注意的是我们必须要保证所有的脚本执行完才发送 MessageEvent，如果在函数执行的过程中调用了他，就会让后面的函数超时无法执行。

参考：[https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage)



---

**参考资料**

* [jsonp-反向代理-CORS解决JS跨域问题的个人总结](https://segmentfault.com/a/1190000012967320)
* [前端跨域请求原理及实践](http://tingandpeng.com/2016/09/05/%E5%89%8D%E7%AB%AF%E8%B7%A8%E5%9F%9F%E8%AF%B7%E6%B1%82%E5%8E%9F%E7%90%86%E5%8F%8A%E5%AE%9E%E8%B7%B5/)



