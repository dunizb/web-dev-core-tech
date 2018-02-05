# 第6节 DNS预解析提升页面速度

在网页体验中我们常会遇到这种情况，即在调用百度联盟、谷歌联盟以及当前网页所在域名外的域名文件时会遇到请求延时非常严重的情况。那么有没有方法去解决这种请求严重延时的现象呢？

一般来说这种延时的原因不会是对方网站带宽或者负载的原因，那么到底是什么导致了这种情况呢。假设是DNS的问题，因为DNS解析速度很可能是造成资源延时的最大原因。于是在页面header中添加了以下代码（用以DNS预解析）：
```html
<meta http-equiv="x-dns-prefetch-control" content="on" />
<link rel="dns-prefetch" href="http://bdimg.share.baidu.com" />
<link rel="dns-prefetch" href="http://nsclick.baidu.com" />
<link rel="dns-prefetch" href="http://hm.baidu.com" />
<link rel="dns-prefetch" href="http://eiv.baidu.com" />
```

效果很不错（测试浏览器为IE8），再打开其他页面时百度分享按钮的加载明显提高！

下面我们来简单了解一下dns-prefetch。

DNS 作为互联网的基础协议，其解析的速度似乎容易被网站优化人员忽视。现在大多数新浏览器已经针对DNS解析进行了优化，典型的一次DNS解析耗费20-120 毫秒，减少DNS解析时间和次数是个很好的优化方式。DNS Prefetching是具有此属性的域名不需要用户点击链接就在后台解析，而域名解析和内容载入是串行的网络操作，所以这个方式能减少用户的等待时间，提升用户体验。

浏览器对网站第一次的域名DNS解析查找流程依次为：浏览器缓存-系统缓存-路由器缓存-ISP DNS缓存-递归搜索。
![DNS解析查找流程](https://images2015.cnblogs.com/blog/562987/201606/562987-20160620203652990-1480146495.jpg)

Chrome内置了DNS Prefetching技术, Firefox 3.5 也引入了这一特性，由于Chrome和Firefox 3.5本身对DNS预解析做了相应优化设置，所以设置DNS预解析的不良影响之一就是可能会降低Google Chrome浏览器及火狐Firefox 3.5浏览器的用户体验。

预解析的实现：
- 用meta信息来告知浏览器, 当前页面要做DNS预解析:`<meta http-equiv="x-dns-prefetch-control" content="on" />`
- 在页面header中使用link标签来强制对DNS预解析: `<link rel="dns-prefetch" href="http://bdimg.share.baidu.com" />`

> 注：dns-prefetch需慎用，多页面重复DNS预解析会增加重复DNS查询次数。

PS：DNS预解析主要是用于网站前端页面优化，在SEO中的作用湛蓝还未作验证，但作为增强用户体验的一部分rel="dns-prefetch"或许值得大家慢慢发现。

------

原文：[http://www.cnblogs.com/rongfengliang/p/5601770.html](http://www.cnblogs.com/rongfengliang/p/5601770.html)
