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

定时让网页在指定的时间n内，跳转到页面[http://yourlink；](http://yourlink；)

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

## 五、针对iOS设备特性

**添加到主屏后的标题（iOS 6 新增）**

```
<meta name="apple-mobile-web-app-title" content="标题">
```

**是否启用 WebApp 全屏模式**

```
<meta name="apple-mobile-web-app-capable" content="yes" />
```

**设置状态栏的背景颜色**

```
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
```

只有在 "apple-mobile-web-app-capable" content="yes" 时生效

* content 参数：
* default 默认值。
* black 状态栏背景是黑色。
* black-translucent 状态栏背景是黑色半透明。 如果设置为 default 或 black ,网页内容从状态栏底部开始。 如果设置为 black-translucent ,网页内容充满整个屏幕，顶部会被状态栏遮挡。

**禁止数字识自动别为电话号码**

```
<meta name="format-detection" content="telephone=no" />
```

**iOS 图标**

rel 参数： apple-touch-icon 图片自动处理成圆角和高光等效果。 apple-touch-icon-precomposed 禁止系统自动添加效果，直接显示设计原图。 iPhone 和 iTouch，默认 57x57 像素，必须有

```
<link rel="apple-touch-icon-precomposed" href="/apple-touch-icon-57x57-precomposed.png" />
```

iPad，72x72 像素，可以没有，但推荐有

```
<link rel="apple-touch-icon-precomposed" sizes="72x72" href="/apple-touch-icon-72x72-precomposed.png" />
```

Retina iPhone 和 Retina iTouch，114x114 像素，可以没有，但推荐有

```
<link rel="apple-touch-icon-precomposed" sizes="114x114" href="/apple-touch-icon-114x114-precomposed.png" />
```

Retina iPad，144x144 像素，可以没有，但推荐有

```
<link rel="apple-touch-icon-precomposed" sizes="144x144" href="/apple-touch-icon-144x144-precomposed.png" />
```

IOS 图标大小在iPhone 6 plus上是180×180，iPhone 6 是120x120。 适配iPhone 6 plus，则需要在中加上这段

```
<link rel="apple-touch-icon-precomposed" sizes="180x180" href="retinahd_icon.png">
```

**iOS 启动画面**

iPad 的启动画面是不包括状态栏区域的。

iPad 竖屏 768 x 1004（标准分辨率）

```
<link rel="apple-touch-startup-image" sizes="768x1004" href="/splash-screen-768x1004.png" />
```

iPad 竖屏 1536x2008（Retina）

```
<link rel="apple-touch-startup-image" sizes="1536x2008" href="/splash-screen-1536x2008.png" />
```

iPad 横屏 1024x748（标准分辨率）

```
<link rel="apple-touch-startup-image" sizes="1024x748" href="/Default-Portrait-1024x748.png" />
```

iPad 横屏 2048x1496（Retina）

```
<link rel="apple-touch-startup-image" sizes="2048x1496" href="/splash-screen-2048x1496.png" />
```

iPhone 和 iPod touch 的启动画面是包含状态栏区域的。

iPhone/iPod Touch 竖屏 320x480 \(标准分辨率\)

```
<link rel="apple-touch-startup-image" href="/splash-screen-320x480.png" />
```

iPhone/iPod Touch 竖屏 640x960 \(Retina\)

```
<link rel="apple-touch-startup-image" sizes="640x960" href="/splash-screen-640x960.png" />
```

iPhone 5/iPod Touch 5 竖屏 640x1136 \(Retina\)

```
<link rel="apple-touch-startup-image" sizes="640x1136" href="/splash-screen-640x1136.png" />
```

添加智能 App 广告条 Smart App Banner（iOS 6+ Safari）

```
<meta name="apple-itunes-app" content="app-id=myAppStoreID, affiliate-data=myAffiliateData, app-argument=myURL">
```

Phone 6对应的图片大小是750×1294，iPhone 6 Plus 对应的是1242×2148 。

```
<link rel="apple-touch-startup-image" href="launch6.png" media="(device-width: 375px)">

<link rel="apple-touch-startup-image" href="launch6plus.png" media="(device-width: 414px)">
```

## 六、其它移动端的meta

```
<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no" />
<meta name="apple-mobile-web-app-capable" content="yes" />
<meta name="apple-mobile-web-app-status-bar-style" content="black" />
<meta name="format-detection"content="telephone=no, email=no" />
<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no" />
<meta name="apple-mobile-web-app-capable" content="yes" /><!-- 删除苹果默认的工具栏和菜单栏 -->
<meta name="apple-mobile-web-app-status-bar-style" content="black" /><!-- 设置苹果工具栏颜色 -->
<meta name="format-detection" content="telphone=no, email=no" /><!-- 忽略页面中的数字识别为电话，忽略email识别 -->
<!-- 启用360浏览器的极速模式(webkit) -->
<meta name="renderer" content="webkit">
<!-- 避免IE使用兼容模式 -->
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<!-- 针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓 -->
<meta name="HandheldFriendly" content="true">
<!-- 微软的老式浏览器 -->
<meta name="MobileOptimized" content="320">
<!-- uc强制竖屏 -->
<meta name="screen-orientation" content="portrait">
<!-- QQ强制竖屏 -->
<meta name="x5-orientation" content="portrait">
<!-- UC强制全屏 -->
<meta name="full-screen" content="yes">
<!-- QQ强制全屏 -->
<meta name="x5-fullscreen" content="true">
<!-- UC应用模式 -->
<meta name="browsermode" content="application">
<!-- QQ应用模式 -->
<meta name="x5-page-mode" content="app">
<!-- windows phone 点击无高光 -->
<meta name="msapplication-tap-highlight" content="no">
<!-- 适应移动端end -->
```

## 七、其它

**Windows8**

Windows 8 磁贴颜色

```
<meta name="msapplication-TileColor" content="#000"/> 
```

Windows 8 磁贴图标

```
<meta name="msapplication-TileImage" content="icon.png"/>
```

**RSS订阅**

```
<link rel="alternate" type="application/rss+xml" title="RSS" href="/rss.xml" />
```

**favicon icon**

```
<link rel="shortcut icon" type="image/ico" href="/favicon.ico" />
```

比较详细的 favicon 介绍可参考[https://github.com/audreyr/favicon-cheat-sheet](https://github.com/audreyr/favicon-cheat-sheet)

---

参考文章：

HTML中小meta的大作用，[http://www.pconline.com.cn/pcedu/sj/wz/html/0401/293106.html](http://www.pconline.com.cn/pcedu/sj/wz/html/0401/293106.html)

