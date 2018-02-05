# 附录4：HTTP 304状态码的详细讲解

304状态码或许不应该认为是一种错误，而是对客户端有缓存情况下服务端的一种响应。

## 整个请求响应过程

客户端在请求一个文件的时候，发现自己缓存的文件有 `Last Modified` ，那么在请求中会包含 `If Modified Since` ，这个时间就是缓存文件的 `Last Modified` 。因此，如果请求中包含 `If Modified Since`，就说明已经有缓存在客户端。服务端只要判断这个时间和当前请求的文件的修改时间就可以确定是返回 304 还是 200 。

对于静态文件，例如：CSS、图片，服务器会自动完成 `Last Modified` 和 `If Modified Since` 的比较，完成缓存或者更新。但是对于动态页面，就是动态产生的页面，往往没有包含 `Last Modified` 信息，这样浏览器、网关等都不会做缓存，也就是在每次请求的时候都完成一个 200 的请求。

因此，对于动态页面做缓存加速，首先要在 Response 的 HTTP Header 中增加 `Last Modified` 定义，其次根据 Request 中的 `If Modified Since` 和被请求内容的更新时间来返回 200 或者 304 。虽然在返回 304 的时候已经做了一次数据库查询，但是可以避免接下来更多的数据库查询，并且没有返回页面内容而只是一个 HTTP Header，从而大大的降低带宽的消耗，对于用户的感觉也是提高。当这些缓存有效的时候，通过 Fiddler 或HttpWatch 查看一个请求会得到这样的结果：

* 第一次访问 200
* 按F5刷新（第二次访问） 304
* 按Ctrl+F5强制刷新 200

**第一次\(首次\)访问 200**

![](http://img.blog.csdn.net/20170412095401308?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHV3ZWkyMDAz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**第二次F5刷新访问 304**

![](http://img.blog.csdn.net/20170412095455308?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHV3ZWkyMDAz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

请求的头信息里多了 “If-Modified-Since","If-None-Match"

**第三次 按Ctrl+F5强制刷新 200**

同第一次，不贴图了

## 为什么要使用条件请求

当用户访问一个网页时，条件请求可以加速网页的打开时间\(因为可以省去传输整个响应体的时间\)，但仍然会有网络延迟，因为浏览器还是得为每个资源生成一条条件请求，并且等到服务器返回HTTP/304响应，才能读取缓存来显示网页。更理想的情况是，服务器在响应上指定`Cache-Control`或`Expires`指令,这样客户端就能知道该资源的可用时间为多长，也就能跳过条件请求的步骤，直接使用缓存中的资源了。可是，即使服务器提供了这些信息，在下列情况下仍然需要使用条件请求:

* 在超过服务器指定的过期时间之后
* 如果用户执行了刷新操作的话

在上节给出的图片中,请求头中包含了一个`Pragma: no-cache`.这是由于用户使用F5刷新了网页。如果用户按下了CTRL-F5 \(有时称之为“强刷-hard refresh”\)，你会发现浏览器省略了`If-Modified-Since`和`If-None-Match`请求头，也就是无条件的请求页面中的每个资源。

## 避免条件请求

通常来说，缓存是个好东西。如果你想提高自己网站的访问速度，缓存是必须要考虑的。可是在调试的时候，有时候需要阻止缓存，这样才能确保你所访问到的资源是最新的。

你也许会有个疑问:“如果不改变网站内容,我怎么才能让Fiddler不返回304而返回一个包含响应体的HTTP/200响应呢?”

你可以在Fiddler中的网络会话\(Web Sessions\)列表中选择一条响应为HTTP/304的会话，然后按下U键。Fiddler将会无条件重发\(Unconditionally reissue\)这个请求。然后使用命compare命令对比一下两个请求有什么不同，对比结果如下，从中可以得知,Fiddler是通过省略条件请求头来实现无缓存请求的:

```
Screenshot of Windiff of conditional and unconditional requests
```

如果你想全局阻止HTTP/304响应，可以这么做：首先清除浏览器的缓存,可以使用Fiddler工具栏上的Clear Cache按钮\(仅能清除Internet Explorer缓存\),或者在浏览器上按CTRL+SHIFT+DELETE\(所有浏览器都支持\)。在清除浏览器的缓存之后，回到Fiddler中，在菜单中选择`Rules > Performance > Disable Caching`选项，然后Fiddler就会:删除所有请求中的条件请求相同的请求头以及所有响应中的缓存时间相关的响应头。此外,还会在每个请求中添加`Pragma: no-cache`请求头，在每个响应中添加`Cache-Control: no-cache`响应头，阻止浏览器缓存这些资源。

## ETag是什么意思？

HTTP 协议规格说明定义ETag为“被请求变量的实体值” 。 另一种说法是，ETag是一个可以与Web资源关联的记号（token）。典型的Web资源可以一个Web页，但也可能是JSON或XML文档。服务器单独负责判断记号是什么及其含义，并在HTTP响应头中将其传送到客户端

---

首先看一个关于304请求的响应头的信息，这里面有两个比较重要的请求头字段：`If-Modified-Since` 和 `If-None-Match`，这两个字段表示发送的是一个条件请求。  
![](http://7xkn2v.dl1.z0.glb.clouddn.com/QQ20160215-0.png)

当客户端缓存了目标资源但不确定该缓存资源是否是最新版本的时候, 就会发送一个条件请求，这样就可以辨别出一个请求是否是条件请求，在进行条件请求时,客户端会提供给服务器一个`If-Modified-Since`请求头,其值为服务器上次返回的`Last-Modified`响应头中的Date日期值,还会提供一个`If-None-Match`请求头,值为服务器上次返回的`ETag`响应头的值。  
![](http://7xkn2v.dl1.z0.glb.clouddn.com/QQ20160215-1.png)

服务器会读取到这两个请求头中的值,判断出客户端缓存的资源是否是最新的,如果是的话,**服务器就会返回HTTP/304 Not Modified响应头, 但没有响应体**.客户端收到304响应后,就会从本地缓存中读取对应的资源.

**所以：当访问七牛资源出现304访问的情况下其实就是先在本地缓存了访问的资源，然后请求的时候流量其实就是cdn返回的响应头的字节数的流量。**

这里以Chrome为例说一下缓存资源在本地的保存的位置，通过在Chrome浏览器的地址栏输入Chrome:Version查看Chrome浏览器保存文件的位置：  
![](http://7xkn2v.dl1.z0.glb.clouddn.com/QQ20160215-2.png)

另一种情况是,如果服务器认为客户端缓存的资源已经过期了,那么服务器就会返回HTTP/200 OK响应,响应体就是该资源当前最新的内容.客户端收到200响应后,就会用新的响应体覆盖掉旧的缓存资源.

只有在客户端缓存了对应资源且该资源的响应头中包含了`Last-Modified`或`ETag`的情况下,才可能发送条件请求.如果这两个头都不存在,则必须无条件\(unconditionally\)请求该资源,服务器也就必须返回完整的资源数据.

另外，有时候我们浏览器调试的时候不希望本地缓存，可以设置取消缓存即可：  
![](http://7xkn2v.dl1.z0.glb.clouddn.com/QQ20160215-3.png)

---

**参考文章**

* [http://blog.csdn.net/netdxy/article/details/50670734](http://blog.csdn.net/netdxy/article/details/50670734)
* [http://blog.csdn.net/huwei2003/article/details/70139062](http://blog.csdn.net/huwei2003/article/details/70139062)



