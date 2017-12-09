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











