# 第九章 HTML中meta标签的大作用

meta是用来在HTML文档中模拟HTTP协议的响应头报文。meta 标签用于网页的&lt;head&gt;与&lt;/head&gt;中，meta 标签的用处很多。meta 的属性有两种：name和http-equiv。name属性主要用于描述网页，对应于content（网页内容），以便于搜索引擎机器人查找、分类（目前几乎所有的搜索引擎都使用网上机器人自动查找meta值来给网页分类）。这其中最重要的是description（站点在搜索引擎上的描述）和keywords（分类关键词），所以应该给每页加一个meta值。

## 一、http-equiv属性

**Content-Type**

```
<meta http-equiv="Content-Type" contect="text/html";charset=gb_2312-80">
<meta http-equiv="Content-Language" contect="zh-CN">
```

用以说明主页制作所使用的文字以及语言；又如英文是ISO-8859-1字符集，还有BIG5、utf-8、shift-Jis、Euc、Koi8-2等字符集。

**Refresh**

```
<meta http-equiv="Refresh" contect="n;url=http://yourlink">
```

定时让网页在指定的时间n内，跳转到页面http://yourlink；

**Expires**

```
<meta http-equiv="Expires" contect="Mon,12 May 2001 00:20:00 GMT">
```

可以用于设定网页的到期时间，一旦过期则必须到服务器上重新调用。需要注意的是必须使用GMT时间格式；

**Pragma**

```
<meta http-equiv="Pragma" contect="no-cache">
```

是用于设定禁止浏览器从本地机的缓存中调阅页面内容，设定后一旦离开网页就无法从Cache中再调出；

**set-cookie**

```
<meta http-equiv="set-cookie" contect="Mon,12 May 2001 00:20:00 GMT">
```

cookie设定，如果网页过期，存盘的cookie将被删除。需要注意的也是必须使用GMT时间格式；

**Pics-label**

```
<meta http-equiv="Pics-label" contect="">
```

网页等级评定，在IE的internet选项中有一项内容设置，可以防止浏览一些受限制的网站，而网站的限制级别就是通过meta属性来设置的；

**windows-Target**

```
<meta http-equiv="windows-Target" contect="_top">
```

强制页面在当前窗口中以独立页面显示，可以防止自己的网页被别人当作一个frame页调用；

**Page-Enter、Page-Exit**

```
<meta http-equiv="Page-Enter" contect="revealTrans(duration=10,transtion= 50)">
<meta http-equiv="Page-Exit" contect="revealTrans(duration=20，transtion=6)">
```

设定进入和离开页面时的特殊效果，这个功能即FrontPage中的“格式/网页过渡”，不过所加的页面不能够是一个frame页面。

## 二、name 属性

&lt;meta name="Generator" contect=""&gt;用以说明生成工具（如Microsoft FrontPage 4.0）等；

&lt;meta name="keywords" contect=""&gt;向搜索引擎说明你的网页的关键词；

&lt;meta name="description" contect=""&gt;告诉搜索引擎你的站点的主要内容；

&lt;meta name="Author" contect="你的姓名"&gt;告诉搜索引擎你的站点的制作的作者；

&lt;meta name="Robots" contect= "all\|none\|index\|noindex\|follow\|nofollow"&gt;告诉搜索引擎爬虫是否爬取页面

* all：文件将被检索，且页面上的链接可以被查询；
* none：文件将不被检索，且页面上的链接不可以被查询；
* index：文件将被检索；
* follow：页面上的链接可以被查询；
* noindex：文件将不被检索，但页面上的链接可以被查询；
* nofollow：文件将不被检索，页面上的链接可以被查询。

## **四、浏览器内核控制**

**优先使用 IE 最新版本和 Chrome**

```
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
```

**360 使用Google Chrome Frame**

```
<meta name="renderer" content="webkit">
```

360 浏览器就会在读取到这个标签后，立即切换对应的极速核。 另外为了保险起见再加入

```
<meta http-equiv="X-UA-Compatible" content="IE=Edge,chrome=1">
```

这样写可以达到的效果是如果安装了 Google Chrome Frame，则使用 GCF 来渲染页面，如果没有安装 GCF，则使用最高版本的 IE 内核进行渲染。

**百度禁止转码**

通过百度手机打开网页时，百度可能会对你的网页进行转码，脱下你的衣服，往你的身上贴狗皮膏药的广告，为此可在 head 内添加

```
<meta http-equiv="Cache-Control" content="no-siteapp" />
```

## 五、

---

参考文章：

HTML中小meta的大作用，[http://www.pconline.com.cn/pcedu/sj/wz/html/0401/293106.html](http://www.pconline.com.cn/pcedu/sj/wz/html/0401/293106.html)

