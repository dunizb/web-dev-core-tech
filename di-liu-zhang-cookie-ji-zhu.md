# 第六章 Cookie技术

网络早期最大的问题之一是如何管理状态。简而言之，服务器无法知道两个请求是否来自同一个浏览器。当时最简单的方法是在请求时，在页面中插入一些参数，并在下一个请求中传回参数。这需要使用包含参数的隐藏的表单，或者作为URL参数的一部分传递。这两个解决方案都手动操作，容易出错。

网景公司当时一名员工Lou Montulli，在1994年将“cookies”的概念应用于网络通信，用来解决用户网上购物的购物车历史记录，目前所有浏览器都支持cookies。

## 一、cookie是什么

cookie翻译过来是“饼干，甜品”的意思，cookie在网络应用中到处存在，当我们浏览之前访问过的网站，网页中可能会显示：你好，王三少，这就会让我们感觉很亲切，像吃了一块很甜的饼干一样。

由于http是无状态的协议，一旦客户端和服务器的数据交换完毕，就会断开连接，再次请求，会重新连接，这就说明服务器单从网络连接上是没有办法知道用户身份的。怎么办呢？那就给每次新的用户请求时，给它颁发一个身份证（独一无二）吧，下次访问，必须带上身份证，这样服务器就会知道是谁来访问了，针对不同用户，做出不同的响应。，这就是Cookie的原理。

其实cookie是一个很小的文本文件，是浏览器储存在用户的机器上的。Cookie是纯文本，没有可执行代码。储存一些服务器需要的信息，每次请求站点，会发送相应的cookie，这些cookie可以用来辨别用户身份信息等作用。

我们还可以再具体点，假设两个客户端来访，它们分别是 A 和 B 。

A首次访问，服务器响应：

```
HTTP/1.1 200 OK
Set-Cookie:id=25

这是你的首访
```

B首次访问，服务器响应：

```
HTTP/1.1 200 OK
Set-Cookie:id=26

这是你的首访
```

A再次访问，发送之前的Cookie给服务器：

```
GET /A HTTP/1.1
Cookie: id=25
```

服务器解析Cookie头字段，知道这个客户是A，可以做出个性响应：

```
HTTP/1.1 200 OK

欢迎你第二次访问，你的号牌为25
```

B再次访问，带上Cookie：

```
GET /A HTTP/1.1
Cookie: id=26
```

服务器响应：

```
HTTP/1.1 200 OK
Set-Cookie:id=26

欢迎你第二次访问，你的号牌为26
```

## 二、cookie的类型

可以按照过期时间分为两类：会话cookie和持久cookie。会话cookie是一种临时cookie，用户退出浏览器，会话Cookie就会被删除了，持久cookie则会储存在硬盘里，保留时间更长，关闭浏览器，重启电脑，它依然存在，通常是持久性的cookie会维护某一个用户周期性访问服务器的配置文件或者登录信息。

> 持久cookie 设置一个特定的过期时间（Expires）或者有效期（Max-Age）

```
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2019 07:28:00 GMT;
```

## 三、cookie的属性

### **cookie的域**

产生Cookie的服务器可以向set-Cookie响应首部添加一个Domain属性来控制哪些站点可以看到那个cookie，例如下面：

```
Set-Cookie: name="dunizb"; domain="dunizb.com"
```

如果用户访问的是dunizb.com那就会发送cookie: name="dunizb", 如果用户访问www.aaa.com（非dunizb.com）就不会发送这个Cookie。

### cookie的路径 Path

Path属性可以为服务器特定文档指定Cookie，这个属性设置的url且带有这个前缀的url路径都是有效的。

例如：dunizb.com 和 dunizb.com/user/这两个url。 dunizb.com 设置cookie

```
Set-cookie: id="123432";domain="m.zhuanzhuan.58.com";
```

dunizb.com/user/ 设置cookie：

```
Set-cookie：user="wang", domain="m.zhuanzhuan.58.com"; path=/user/
```

但是访问其他路径dunizb.com/other/就会获得

```
cookie: id="123432"
```

如果访问dunizb.com/user/就会获得

```
cookie: id="123432"
cookie: user="dunizb"
```

### secure

设置了属性secure，cookie只有在https协议加密情况下才会发送给服务端。但是这并不是最安全的，由于其固有的不安全性，敏感信息也是不应该通过cookie传输的。

```
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure;
```

> chrome 52和firefox 52 开始不安全的（HTTP）是无法使用secure的

## 四、操作Cookie

通过docuemnt.cookie可以设置和获取Cookie的值

```
document.cookie = "user = dunizb"
console.log(document.cookie)
```

禁止javascript操作cookie（为避免跨域脚本\(xss\)攻击，通过javascript的document.cookie无法访问带有HttpOnly标记的cookie。）

```
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2017 07:28:00 GMT; Secure; HttpOnly
```

## 五、第三方cookie

通常cookie的域和浏览器地址的域匹配，这被称为第一方cookie。那么第三方cookie就是cookie的域和地址栏中的域不匹配，这种cookie通常被用在第三方广告网站。为了跟踪用户的浏览记录，并且根据收集的用户的浏览习惯，给用户推送相关的广告。 ![](/assets/640.webp)













