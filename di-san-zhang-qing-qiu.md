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





