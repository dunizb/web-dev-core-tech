# 第9节 HTTP缓存机制

缓存机制无处不在，有客户端缓存，服务端缓存，代理服务器缓存等。在HTTP中具有缓存功能的是浏览器缓存。

HTTP缓存作为web性能优化的重要手段，对于从事web开发的朋友有重要的意义。本文将围绕以下几个方面来整理HTTP缓存：

* 缓存的优点
* 缓存的规则
* 缓存的方案
* 不同刷新的请求执行过程

## 一、缓存优点

通常所说的Web缓存指的是可以自动保存常见http请求副本的http设备。对于前端开发者来说，浏览器充当了重要角色。除此外常见的还有各种各样的代理服务器也可以做缓存。当Web请求到达缓存时，缓存从本地副本中提取这个副本内容而不需要经过服务器。这带来了以下优点：
- 缓存减少了冗余的数据传输，节省流量
- 缓存缓解了带宽瓶颈问题。不需要更多的带宽就能更快加载页面
- 缓存缓解了瞬间拥塞，降低了对原始服务器的要求。
- 缓存降低了距离延时， 因为从较远的地方加载页面会更慢一些。

## 二、缓存的种类

缓存可以是单个用户专用的，也可以是多个用户共享的。专用缓存被称为**私有缓存**，共享的缓存被称为**公有缓存**。

### 2.1 私有缓存

私有缓存只针对专有用户，所以不需要很大空间，廉价。Web浏览器中有内建的私有缓存——大多数浏览器都会将常用资源缓存在你的个人电脑的磁盘和内存中。如Chrome浏览器的缓存存放位置就在：`C:\Users\Your_Account\AppData\Local\Google\Chrome\User Data\Default`中的Cache文件夹和Media Cache文件夹。

### 2.2 公有缓存

公有缓存是特殊的共享代理服务器，被称为**缓存代理服务器**或**代理缓存**（反向代理的一种用途）。公有缓存会接受来自多个用户的访问，所以通过它能够更好的减少冗余流量。

下图中每个客户端都会重复的向服务器访问一个资源（此时还不在私有缓存中），这样它会多次访问服务器，增加服务器压力。而使用共享的公有缓存时，缓存只需要从服务器取一次，以后不用再经过服务器，能够显著减轻服务器压力。
![](https://oss-cn-hangzhou.aliyuncs.com/yqfiles/3976c20dcda9d2556acd30437920994c7bdf4133.png)

事实上在实际应用中通常采用层次化的公有缓存，基本思想是在靠近客户端的地方使用小型廉价缓存，而更高层次中，则逐步采用更大、功能更强的缓存在装载多用户共享的资源。

## 三、缓存处理流程

![](https://oss-cn-hangzhou.aliyuncs.com/yqfiles/8048690cc1307a7a853434d0b37759ba69a8da4d.png)

而对于前端开发者来说，我们主要跟浏览器中的缓存打交道，所以上图流程简化为：
![](https://oss-cn-hangzhou.aliyuncs.com/yqfiles/96b79e257cd57cf32f0a326dbf19764956e4cdaa.png)

> 这个图也告诉了你：什么时候HTTP求情会返回304状态码

面这张图展示了某一网站，对不同资源的请求结果，其中可以看到有的资源直接从缓存中读取，有的资源跟服务器进行了再验证，有的资源重新从服务器端获取。
![](https://oss-cn-hangzhou.aliyuncs.com/yqfiles/51dd47f3446a34f181209212232294e2d7e710c5.png)

**注意，我们讨论的所有关于缓存资源的问题，都仅仅针对GET请求。而对于POST, DELETE, PUT这类行为性操作通常不做任何缓存**

## 四、新鲜度限值

HTTP通过缓存将服务器资源的副本保留一段时间，这段时间称为`新鲜度限值`。这在一段时间内请求相同资源不会再通过服务器。HTTP协议中`Cache-Control` 和 `Expires`可以用来设置新鲜度的限值，前者是HTTP1.1中新增的响应头，后者是HTTP1.0中的响应头。二者所做的事时都是相同的，但由于`Cache-Control`使用的是相对时间，而`Expires`可能存在客户端与服务器端时间不一样的问题，所以我们更倾向于选择`Cache-Control`。

### 4.1 Cache-Control

下面我们来看看`Cache-Control`都可以设置哪些属性值

**max-age**

（单位为s）指定设置缓存最大的有效时间，定义的是时间长短。当浏览器向服务器发送请求后，在max-age这段时间里浏览器就不会再向服务器发送请求了。
```html
<html>
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
    <meta http-equiv="X-UA-Compatible" content="IE=EDGE" />
    <title>Web Cache</title>
    <link rel="shortcut icon" href="./shortcut.png">
    <script>
    </script>
  </head>
  <body class="claro">
  <img src="./cache.png">
  </body>
</html>
```
```js
var http = require('http');
var fs = require('fs');
http.createServer(function(req, res) {
    if (req.url === '/' || req.url === '' || req.url === '/index.html') {
        fs.readFile('./index.html', function(err, file) {
            console.log(req.url)
            //对主文档设置缓存，无效果
            res.setHeader('Cache-Control', "no-cache, max-age=" + 5);
            res.setHeader('Content-Type', 'text/html');
            res.writeHead('200', "OK");
            res.end(file);
        });
    }
    if (req.url === '/cache.png') {
        fs.readFile('./cache.png', function(err, file) {
            res.setHeader('Cache-Control', "max-age=" + 5);//缓存五秒
            res.setHeader('Content-Type', 'images/png');
            res.writeHead('200', "Not Modified");
            res.end(file);
        });
    }

}).listen(8888)
```

当在5秒内第二次访问页面时，浏览器会直接从缓存中取得资源
![](https://oss-cn-hangzhou.aliyuncs.com/yqfiles/6f764db84597fb505098507b6723578e18922e1d.png)

**public **

指定响应可以在代理缓存中被缓存，于是可以被多用户共享。如果没有明确指定private，则默认为public。

**private**

响应只能在私有缓存中被缓存，不能放在代理缓存上。对一些用户信息敏感的资源，通常需要设置为private。

**no-cache**

表示必须先与服务器确认资源是否被更改过（依靠`If-None-Match`和`Etag`），然后再决定是否使用本地缓存。

如果上文中关于cache.png的处理改成下面这样，则每次访问页面，浏览器都需要先去服务器端验证资源有没有被更改。
```js
fs.readFile('./cache.png', function(err, file) {
            console.log(req.headers);
            console.log(req.url)
            if (!req.headers['if-none-match']) {
                res.setHeader('Cache-Control', "no-cache, max-age=" + 5);
                res.setHeader('Content-Type', 'images/png');
                res.setHeader('Etag', "ffff");
                res.writeHead('200', "Not Modified");
                res.end(file);
            } else {
                if (req.headers['if-none-match'] === 'ffff') {
                    res.writeHead('304', "Not Modified");
                    res.end();
                } else {
                    res.setHeader('Cache-Control', "max-age=" + 5);
                    res.setHeader('Content-Type', 'images/png');
                    res.setHeader('Etag', "ffff");
                    res.writeHead('200', "Not Modified");
                    res.end(file);
                }
            }

        });
```
![](https://oss-cn-hangzhou.aliyuncs.com/yqfiles/ba506586e5d63570a26238e79205cdaa2f660897.png)

**no-store**

绝对禁止缓存任何资源，也就是说每次用户请求资源时，都会向服务器发送一个请求，每次都会下载完整的资源。通常用于机密性资源。

关于`Cache-Control`的使用，见下面这张图（来自[大额](http://www.cnblogs.com/skylar/p/browser-http-caching.html?spm=a2c4e.11153940.blogcont50624.4.669f3256wtXoM)）
![](https://oss-cn-hangzhou.aliyuncs.com/yqfiles/368232419be36f32c3af7359365290f287a853ca.png)

### 4.2 客户端的新鲜度限值

`Cache-Control`不仅仅可以在响应头中设置，还可以在请求头中设置。浏览器通过请求头中设置`Cache-Control`可以决定是否从缓存中读取资源。**这也是为什么有时候点击浏览器刷新按钮和在地址栏回车，在NetWork模块中看到完全不同的结果**
![](https://oss-cn-hangzhou.aliyuncs.com/yqfiles/ede419b5cf814603ffba9ce10d70a2b56bf0669c.png)

### 4.3 Expires

不推荐使用Expires，它指定的是具体的过期日期而不是秒数。因为很多服务器跟客户端存在时钟不一致的情况，所以最好还是使用`Cache-Control`.

## 五、服务器再验证

浏览器或代理缓存中缓存的资源过期了，并不意味着它和原始服务器上的资源有实际的差异，仅仅意味着到了要进行核对的时间了。这种情况被称为服务器再验证。
- 如果资源发生变化，则需要取得新的资源，并在缓存中替换旧资源。
- 如果资源没有发生变化，缓存只需要获取新的响应头，和一个新的过期时间，对缓存中的资源过期时间进行更新即可。

HTTP1.1推荐使用的验证方式是`If-None-Match/Etag`，在HTTP1.0中则使用`If-Modified-Since/Last-Modified`。

### 5.1 Etag与If-None-Match

根据实体内容生成一段hash字符串，标识资源的状态，由服务端产生。浏览器会将这串字符串传回服务器，验证资源是否已经修改，如果没有修改，过程如下(图片来自浅谈[Web缓存](http://web.jobbole.com/85243/?spm=a2c4e.11153940.blogcont50624.5.669f3256wtXoM))：
![](https://oss-cn-hangzhou.aliyuncs.com/yqfiles/bbe0d07be1e68d01d904f278be75fa1b5e3f801e.png)
![](https://oss-cn-hangzhou.aliyuncs.com/yqfiles/2d959c41de8c2cda0a562ef54a7e757a886e7e9c.png)

上文的demo中我们见到过服务器端如何验证Etag：
![](https://oss-cn-hangzhou.aliyuncs.com/yqfiles/a8907c9493c700f7424be12a88b289034c9811d4.png)

**由于Etag有服务器构造，所以在集群环境中一定要保证Etag的唯一性**

### 5.2 If-Modified-Since与Last-Modified

这两个是HTTP1.0中用来验证资源是否过期的请求/响应头，这两个头部都是日期，验证过程与`Etag`类似，这里不详细介绍。使用这两个头部来验证资源是否更新时，存在以下问题：
- 有些文档资源周期性的被重写，但实际内容没有改变。此时文件元数据中会显示文件最近的修改日期与`If-Modified-Since`不相同，导致不必要的响应。
- 有些文档资源被修改了，但修改内容并不重要，不需要所有的缓存都更新（比如代码注释）

关于缓存的更新问题，请大家看看这里[张云龙的回答](https://www.zhihu.com/question/20790576?spm=a2c4e.11153940.blogcont50624.6.669f3256wtXoM)，本文就不详细展开了。

本文demo代码如下：
HTML
```html
<!DOCTYPE HTML>
<html>
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
    <meta http-equiv="X-UA-Compatible" content="IE=EDGE" />
    <title>Web Cache</title>
    <link rel="shortcut icon" href="./shortcut.png">
    <script>
    </script>
  </head>
  <body class="claro">
  <img src="./cache.png">
  </body>
</html>
```
Node.js
```js
var http = require('http');
var fs = require('fs');
http.createServer(function(req, res) {
    if (req.url === '/' || req.url === '' || req.url === '/index.html') {
        fs.readFile('./index.html', function(err, file) {
            console.log(req.url)
            //对主文档设置缓存，无效果
            res.setHeader('Cache-Control', "no-cache, max-age=" + 5);
            res.setHeader('Content-Type', 'text/html');
            res.writeHead('200', "OK");
            res.end(file);
        });
    }
    if (req.url === '/shortcut.png') {
        fs.readFile('./shortcut.png', function(err, file) {
            console.log(req.url)
            res.setHeader('Content-Type', 'images/png');
            res.writeHead('200', "OK");
            res.end(file);
        })
    }
    if (req.url === '/cache.png') {
        fs.readFile('./cache.png', function(err, file) {
            console.log(req.headers);
            console.log(req.url)
            if (!req.headers['if-none-match']) {
                res.setHeader('Cache-Control', "max-age=" + 5);
                res.setHeader('Content-Type', 'images/png');
                res.setHeader('Etag', "ffff");
                res.writeHead('200', "Not Modified");
                res.end(file);
            } else {
                if (req.headers['if-none-match'] === 'ffff') {
                    res.writeHead('304', "Not Modified");
                    res.end();
                } else {
                    res.setHeader('Cache-Control', "max-age=" + 5);
                    res.setHeader('Content-Type', 'images/png');
                    res.setHeader('Etag', "ffff");
                    res.writeHead('200', "Not Modified");
                    res.end(file);
                }
            }

        });
    }

}).listen(8888)
```

## 面试题

### 什么时候会出现304状态码？

如果客户端发送了一个带条件的GET 请求且该请求已被允许，而文档的内容（自上次访问以来或者根据请求的条件）并没有改变，则服务器应当返回这个304状态码。简单的表达就是：客户端已经执行了GET，但文件未变化。[百度百科](https://baike.baidu.com/item/304状态码?fr=aladdin)

### 为什么会出现204状态码？

表明请求处理成功，但是作为服务器并不想要提供消息在消息主体内，或者并没有什么消息主体需要提供。 比如使用DELETE请求情况下，如果服务成功完成，可以返回204 No Content。此场景下就是告诉客户端：“你的DELETE请求已经完成，但是因为这个资源已经被删除，所以，也就没有什么需要返回的消息”。

204 对用户代理（浏览器）也是有意义的。用户代理收到204，就不应该引发请求的文档的当前视图（就是不要去刷新当前文档，也不要导航到别的URL）。这就意味着，如果有一个 HTML Form然后提交，如果服务器返回的状态是204状态，那么浏览器不可以刷新窗口或者到其他的页面。所有的 Form 输入的内容都不要改变。当然这样的做法在用户代理中很少有人如此实践。因为用户点击发生了，和服务器的交互也发生了，而用户界面却对此毫无响应，那么这样的做法显然会让用户感到迷惑。

可要是在ajax的应用上下文，这样做就比较有价值了。ajax应用获得204状态返回就可以提示用户操作已经成功。并且如同204状态码的意图，不需修改当前 Form 的任何值。因为服务本来不需要返回任何具体数据，它只需要告诉客户端请求已经成功处理。[刘传君《HTTP小书》](http://www.ituring.com.cn/book/1791)



------

**参考文章**
- [作为前端应当了解的Web缓存知识](https://yq.aliyun.com/articles/50624)
- [HTTP----HTTP缓存机制](https://juejin.im/post/5a1d4e546fb9a0450f21af23)

