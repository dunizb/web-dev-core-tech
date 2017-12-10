# 第九章 HTML中meta标签的大作用

meta是用来在HTML文档中模拟HTTP协议的响应头报文。meta 标签用于网页的&lt;head&gt;与&lt;/head&gt;中，meta 标签的用处很多。meta 的属性有两种：name和http-equiv。name属性主要用于描述网页，对应于content（网页内容），以便于搜索引擎机器人查找、分类（目前几乎所有的搜索引擎都使用网上机器人自动查找meta值来给网页分类）。这其中最重要的是description（站点在搜索引擎上的描述）和keywords（分类关键词），所以应该给每页加一个meta值。

## 一、http-equiv属性

**Content-Type**

&lt;meta http-equiv="Content-Type" contect="text/html";charset=gb\_2312-80"&gt;和 &lt;meta http-equiv="Content-Language" contect="zh-CN"&gt;用以说明主页制作所使用的文字以及语言；又如英文是ISO-8859-1字符集，还有BIG5、utf-8、shift-Jis、Euc、Koi8-2等字符集。

**Refresh**

&lt;meta http-equiv="Refresh" contect="n;url=http://yourlink"&gt;定时让网页在指定的时间n内，跳转到页面http://yourlink；

**Expires**

&lt;meta http-equiv="Expires" contect="Mon,12 May 2001 00:20:00 GMT"&gt;可以用于设定网页的到期时间，一旦过期则必须到服务器上重新调用。需要注意的是必须使用GMT时间格式；

**Pragma**

&lt;meta http-equiv="Pragma" contect="no-cache"&gt;是用于设定禁止浏览器从本地机的缓存中调阅页面内容，设定后一旦离开网页就无法从Cache中再调出；

**set-cookie**

&lt;meta http-equiv="set-cookie" contect="Mon,12 May 2001 00:20:00 GMT"&gt;cookie设定，如果网页过期，存盘的cookie将被删除。需要注意的也是必须使用GMT时间格式；

**Pics-label**

&lt;meta http-equiv="Pics-label" contect=""&gt;网页等级评定，在IE的internet选项中有一项内容设置，可以防止浏览一些受限制的网站，而网站的限制级别就是通过meta属性来设置的；

**windows-Target**

&lt;meta http-equiv="windows-Target" contect="\_top"&gt;强制页面在当前窗口中以独立页面显示，可以防止自己的网页被别人当作一个frame页调用；

**Page-Enter、Page-Exit**

&lt;meta http-equiv="Page-Enter" contect="revealTrans\(duration=10,transtion= 50\)"&gt;和&lt;meta http-equiv="Page-Exit" contect="revealTrans\(duration=20，transtion=6\)"&gt;设定进入和离开页面时的特殊效果，这个功能即FrontPage中的“格式/网页过渡”，不过所加的页面不能够是一个frame页面。







---

参考文章：

HTML中小meta的大作用，[http://www.pconline.com.cn/pcedu/sj/wz/html/0401/293106.html](http://www.pconline.com.cn/pcedu/sj/wz/html/0401/293106.html)

