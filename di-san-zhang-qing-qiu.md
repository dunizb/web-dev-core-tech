# 第三章 请求

## 一、概述

HTTP协议只允许客户端发起请求，也只允许服务器针对请求返回响应。我们已经看到了请求的样例，现在我们具体来了解HTTP请求消息的构成。请求消息包括一个首行、首部、请求主体。

```
首行
首部字段
空行
请求体（请求主体）
```

**首行**

请求行由请求方法、请求URL、一个HTTP版本构成，中间用空格隔开

```
GET /hello.html HTTP/1.1
```

**请求方法**

HTTP/1.1支持方法包括：

* OPTIONS
* GET
* POST
* PUT
* DELETE
* TRACE
* CONNECT
* PATCH

在第二章里说过，HTTP 0.9 协议版本中，GET 方法是唯一被支持的 HTTP 请求方法，用来向服务器请求一个指定URL关联的资源。随后，为了优化的目的，HEAD 方法被加入进来；为了支持向服务器提交数据，引入了 POST、PUT、DELETE 方法。分别在语义上表达更新、创建和删除指定的资源。对于任何一个通用目的的HTTP服务器，GET、HEAD方 法是必须实现的，其他的方法都是可选实现的。可以采用 OPTIONS 方法去查询一个服务器资源所支持的请求方法清单。

**首部**

首部包括零到多个首部行，每个首部行由“：”分隔，左边的文本被称为首部字段，右边的文本被称为首部值。采用文本的首部让HTTP变得容易扩展。只要客户端和服务器做好约定，就可添加自己需要的新的请求方法。不过，对于如何扩展的内容，不在本书需要讨论的范围。

```
accept-encoding:gzip, deflate, br
accept-language:zh-CN,zh;q=0.9
cache-control:no-cache
pragma:no-cache
```

**消息主体 message-body**

消息主体就是消息的承载内容。单独分章节随同请求方法来做说明

## 二、请求方法详细介绍

### GET

HTTP GET 方法请求指定的资源。这个URL指向可以是一个静态文件，也可以是一个数据生成软件产生的动态内容。使用 GET 的请求应该只用于获取数据。

如果GET请求在首部区包含了条件获取字段，那么GET 请求就具体化为条件获取\(conditional GET\)。条件字段包括： If-Modified-Since、 If-Unmodified-Since、 If-Match、 If-None-Match、 If-Range 。条件获取请求下，只有满足了条件的资源才会传递响应主体到客户端。这样的首部字段搭配使用就可以达成缓存的目的。

如果GET 请求包括了范围条件，那么GET请求就被具体化为局部获取\(partial GET\)。使用局部获取，对于大文件可以分块传递从而提高传输效率。要是你在做一个视频播放应用，那么可以只传递用户跳播的视频片段，提供更好的用户体验。

以访问 hello.txt 获取其局部为例。使用GET方法，并通过Range头字段指定指定获取文件的开始字节索引和结束字节索引发出如下局部请求：

```
GET /hello.txt HTTP/1.1
Range: bytes=0-2
```

服务器响应:

```
HTTP/1.1 206 Partial Content
Content-Type: text/html
Content-Range: bytes 0-2/12
Content-Length: 3

hel
```

本案例中的 Content-Range 的值是一个内容为 bytes 0-2/12 的字符串，这里需要对它稍作解释：分隔符“/”的前面一组数字表明本次返回位置范围，“/”后的数字指明资源的总大小。

### POST

HTTP POST 方法 发送数据给服务器，常常用来提交表单数据。假设有一个表单，其html如下：

```
<form enctype="application/x-www-form-urlencoded" />
    <input type="text" name="user" action="/example"/>
    <input type="password" name="password" />
    <input type="submit"/>
</form>
```

当在两个文本框内分别填写1，2，然后点击提交的时候，我们需要传递形如：

```
username :1
password :2
```

的内容给服务器。

本案例中，我们需要注意到Form的属性enctype指定了一个看起来有些复杂的值： application/x-www-form-urlencoded，这个值指示Form经由HTTP提交到服务器的消息主体采用的编码方式。最后的提交请求数据就是这样的：

```
POST /example HTTP/1.1

username=1&password=2
```

此编码方式把提交字段和值用“=”分隔，多个提交项目之间用“&”分隔，空格会被使用“+“替代，其他不是字母和数字的字符会用url encoding 来编码，替换为%HH，比如%21 表示 “！”。看到案例后，就知道看起来复杂的enctype其实还比较简单的。

只不过使用这个编码方式的话，一个非字母数字的二进制值会需要3个字符来表达。对于较大的二进制数据来说这样的作法实在有些浪费。因此HTTP标准也引入了新的封装格式：multipart/form-data。我们依然可以通过Form 的enctype属性指定此新的打包格式：

```
<form enctype="multipart/form-data" />
```

这样指定的封装类型，请求消息会变成：

```
POST /example HTTP/1.1
Host: example.com
Content-Type: multipart/form-data; boundary=---------------------------9051914041544843365972754266
Content-Length: 554

-----------------------------9051914041544843365972754266
Content-Disposition: form-data; name="username"

1
-----------------------------9051914041544843365972754266
Content-Disposition: form-data; name="password"
Content-Type: text/plain

2
-----------------------------9051914041544843365972754266--
```

我们要留意的是，在请求消息内的第三行，也就是Content-Type字段的值内，指定请求主体内的格式为multipart/form-data，同时在“；”后还有一个 boundary\(边界\) 子字段，它的值是一个字符串。此字符串的目的就是指定每个表单字段的开始和结束。在两个 boundary 之间，就是一个字段的内容。每个字段内可以指定字段的名称和类型，然后加上一个空行后内容开始。直到遇到一个新的 boundary 结束。

boundary字符串常常由若干个连字符加上一个随机字符串构成，由客户端软件生成，算法可以自己决定，只要不会在内容中出现就可以。如果对冲突感到担心，还可以在生成后由软件在请求消息体内搜索此字符串，如果发现有相同的话，就在此基础上继续添加一个随机字符，再执行此过程直到不再出现即可。

### OPTIONS

请求方法OPTIONS用来查询URL指定的资源所支持的方法列表。

请求案例：

```
OPTIONS /example HTTP/1.1
```

响应：

```
HTTP/1.1 OK

Allow:GET,POST,PUT,OPTIONS
```

本请求案例中，请求的就是/example 指定的资源所支持的方法。响应案例给出的是此资源支持的请求方法，列表为GET、POST、PUT、OPTIONS。

服务器应该返回405 \(Method Not Allowed\)响应，如果使用的请求方法是为服务器所知的、但是并不被允许的话。 服务器应该返回 501 \(Not Implemented\) ，如果请求方法没有被实现的话。

### PUT和DELETE

PUT方法的意图，是对URL指定的资源进行创建,如果资源存在就修改它；相应的，DELETE方法的意图是对URL指定的资源进行删除。

可是要做到这两件事，只是POST就够了。那么为何在HTTP标准内还有PUT和DELETE呢？我将会举个业务案例，给出只是使用POST、和全面使用HTTP方法的效果，以此对比来说明问题。

假设我们手里有一个电子商务网站，那么我们必然需要提供订单的维护，包括创建、修改、删除、查询。

对于查询，我们可以采用GET 方法，并提供订单编号为参数：

```
GET /order/1
```

使用GET 方法在这里是恰如其分的，因为GET隐含着只是查询，而并不会影响服务器的状态。

接下来，我们更新一个订单，假设编号为2：

```
POST /order/2
Content-Type:text/json

{
    "date":"2015-12-07"，
    “guest":"frodo",
    [{
        "item":"The King of Ring",
        "count":"2",
        "price":"100"
    }]
}
```

这里使用POST也是合适的。因为POST的语义中包括对资源进行更新。

但是对于创建和删除，我们就有不同的做法了。

首先看创建。我们常见的方法依然是使用POST，但是在URL内（或者请求消息主体内的参数）要和更新操作不同，以便区别两者：

```
POST /order/2/create
Content-Type:text/json

{
    "date":"2015-12-07"，
    “guest":"frodo",
    [{
        "item":"The King of Ring",
        "count":"1",
        "price":"100"
    }]
}
```

这样做是可以达成业务需求的。然而，我们可以有更好的选择。这个选择不但能够完成功能的需求，还能够满足Restful App的规范化需求。我们可以在创建资源是选择PUT：

```
PUT /order/2
Content-Type:text/json

…
```

删除的时候也是一样。典型的请求消息：

```
POST /order/2/delete
或者有人这样

POST /order/2/remove

或者还有人这样
POST /order/remove_order/2
```

而如果我想要满足Restful app规范，选择就是一个样：

```
DELETE /order/2
```

稍微做过总结，Restful App 的方案的好处是看得到的：

1. 把操作意图表达在请求方法内
2. 把操作意图从URL中分离出来

相对于使用POST做全部的提交数据的做法而言，这样的做法经过一个著名框架（Ruby on Rails\)的首倡，目前得到了很多框架的附和，堪称一时风气之先。这样做可以有语义上的一致性，避免不同程序员选择的不同方案导致的不必要的混乱。

尽管Web Form的Action字段只能指定为GET和POST，本身没有提供PUT、DELETE方法，但是可以通过Form隐含字段来细分POST为 PUT、POST、DELETE，比如约定一个叫做\_method的字段，其值可以在PUT、POST、DELETE、POST之间选择一个：

```
<form method="post" …>
  <input type="hidden" name="_method" value="PUT | POST | DELETE " />
  …
```

这样就可以由框架实现完整的对资源操作的不同类型。在使用框架的基础上，应用可以直接享受到完整的GET、PUT 、POST、 DELETE语义。

### CONNECT

在当前已经建立HTTP连接的情况下，CONNECT 方法用来告知代理服务器，客户端想要和服务器之间建立SSL连接。

要是没有HTTP代理服务器，客户端可以使用 Connection 头字段来表达客户端要升级到SSL的请求：

```
GET http://example.bank.com/acct_stat.html?749394889300 HTTP/1.1
Host: example.bank.com
Upgrade: TLS/1.0
Connection: Upgrade
```

这样服务器接收到此消息即可发送：

```
HTTP/1.1 101 Switching Protocols
Upgrade: TLS/1.0, HTTP/1.1
Connection: Upgrade
```

表示确认。一次握手后，双方认可，这个http连接之后就可以发送SSL流量了。

如果中间有HTTP代理服务器的话，情况就不同了。因为我们使用的Connection头字段是hop-by-hop（逐跳）的，这个头字段会被代理服务器认为是在客户端到代理服务器之间的协议升级。于是，代理升级连接协议，解析并删除此字段后继续转到服务器。这样服务器是收不到这个首部的，本来希望客户端和服务器直接达成SSL 升级，实际上却变成了客户端和代理服务器之间的SSL升级，这是违背Connetion字段的本意的。

为了解决此问题，HTTP 引入了 Connect 方法。客户端使用如下消息，通知代理服务器，去做一个连接到指定的服务器地址和端口:

```
CONNECT example.com 443 HTTP/1.1
```

代理服务器随后提取CONNECT 方法指定的地址和端口（这里是 example.com 443 ），建立和此服务器的SSL连接，成功后随后通知客户端，需要的连接建立完毕：

```
HTTP/1.1 OK
```

之后，代理服务器简单的转发客户端的消息到服务器，以及转发服务器来的消息给客户端。因为它只是转发，它就变成了一个透明代理。透明代理和一般代理是不同的，一般的http 代理不是仅仅转发，还需要解析头字段、考虑是否缓存、添加Via头字段等工作，而透明代理只管转发，不管内容和格式。

升级到 SSL 只能有客户端发起。如果服务器希望升级，可以通过状态码426 upgrade required 告知客户端。

因此，客户端和服务器之间要升级到SSL，就必须区分两种情况，一种是两者之间存在代理服务器，就需要用Connect 方法；否则使用第一种方法（使用Connection 头字段的方法）即可。

有很多资料提到 Connect 方法建立的是一种隧道，叫做SSL隧道。我觉得这个说法不妥，因为和隧道的定义是不符的。

隧道被用来在一个协议上承载一个系统本来并不支持的外部协议。一个协议内嵌套另一个协议，就像一个管道嵌入另一个管道，因此取名为隧道。 这就意味着，完全可能在TCP上承载IP、IPV6协议，或者在TCP上承载NetBIOS协议。

我们再进一步查看CONNECTION连接和HTTP隧道在防火墙面前的差异。在使用CONNECT 方法时，一旦连接建立成功后，传递的内容如加密流量是在RAW SOCKET上，对于可以识别HTTP包格式的防火墙，可以知道这个流量尽管可能是80端口，但是并非HTTP流量，因为它的格式根本不遵循HTTP标准内的请求和响应包格式。而HTTP隧道的流量到来时，即使可以识别包格式的防火墙也会认为它传递的就是HTTP流量，因为非HTTP流量本来就是包装HTTP消息内的。因此，CONNECTION 方法建立的SSL通道并不是隧道。把Connect建立的通道叫做隧道会导致认知的混乱。

所以理解 Connect 方法，需要对比和区分以下内容：

1. Http 可以通过Connection：upgrade的方法升级到TLS。仅仅使用于无代理服务器的情况。
2. Connect 方法是 http upgrade tls 的一个替代。针对有代理的情况。
3. Connect 方法成功返回后，中间的http代理变成了透明的代理：不再使用HTTP协议解析数据包和修改数据包，而是简单的转发流量。

这样就清晰了。

### 小结

* GET 表示我要请求一个由URI指定的在服务器上的资源。
* PUT方法 表示如果指定URI资源不存在就创建它，否则就修改它。
* POST方法 表示要创建一个新的子资源，或者更新一个存在的资源。
* DELETE表示我要删除一个由URI指定的资源。
* HEAD 和GET一样，但是仅仅返回指定资源响应的头部分，而不必返回响应主体
* OPTIONS 查询目标资源支持method的清单。
* TRACE 查询到目标资源经过的中间节点。用于测试。
* CONNECT 建立一个到URI指定的服务器的隧道。
* PATCH方法用于对资源应用部分修改。

---

本文摘自《HTTP小书》[http://www.ituring.com.cn/book/1791](http://www.ituring.com.cn/book/1791)

