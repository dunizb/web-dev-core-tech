# 第4节 Web图像优化之图片懒加载技术

图片懒加载是一种网页优化技术。图片作为一种网络资源，在被请求时也与普通静态资源一样，将占用网络资源，而一次性将整个页面的所有图片加载完，将大大增加页面的首屏加载时间。为了解决这种问题，通过前后端配合，使图片仅在浏览器当前视窗内出现时才加载该图片，达到减少首屏图片请求数的技术就被称为“图片懒加载”。

## 一、传统的解决方案

我们先来探讨一下传统的图片懒加载技术是如何实现的。

以京东sale活动页为例，我们随便选取一个活动地址，查看网页源代码，我们可以找到形如这种结构的img标签：

```html
<img class="J_imgLazyload" src="//img14.360buyimg.com/cms/g10/M00/13/04/rBEQWFFj4PUIAAAAAAAESxyqJLUAADvdAIHC9oAAARj186.gif" original="//img11.360buyimg.com/cms/jfs/t12118/41/1394617476/43413/2253395a/5a1f7569N63f38100.jpg" />
```

我们试着来分析一下这个img标签的结构，首先我们在浏览器中直接打开该img标签的src，即：“//img14.360buyimg.com/cms/g10/M00/13/04/rBEQWFFj4PUIAAAAAAAESxyqJLUAADvdAIHC9oAAARj186.gif”。结果发现这张图是一张10\*10的空白图片。我们发现img标签中还有一个伪属性original，里面也有一个很像是图片的链接，果然如我们所料，original中存放的才是真实图片的路径。

**那么浏览器是怎么加载存放在伪属性中的真实图片的呢？**

首先后端直出的页面结构中，img标签中的src为一张占位图，真实图片地址存放在一个伪属性中，如:data-src中。当页面滚动时，遍历当前页面需要进行懒加载的图片，判断图片是否在可视区域内，如果在的话，则取存放在伪属性中的真实src替换当前的src。  
![](http://mmbiz.qpic.cn/mmbiz_png/NTzDrVG8ibqk9TRD4Rag0Am5hVyhYicsejgibKCOLOx3qe1TxmuA5gj9vrTjjhqHTMVMiaoDkaUJDbMhH8cbWzj9Zg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

以上是一张简略的示意图，外部黑色框代表页面范围（即html的范围），虚线内代表浏览器当前视窗区域。在这个场景下，只有第4、5、6张图片处在当前视窗中，因此它们会使用上述加载策略，使用真实src替换占位图。而其余的几张图片由于并不在可视区域内，因此并不会被立即加载，只有当滚动事件产生，某张图片（或图片的一部分）开始出现在视窗内时，才会真正加载。

一个简单实现：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>LazyLoad</title>
    <style type="text/css">
        img {
            display: block;
            width: 500px;
            height: 400px;
        }
    </style>
</head>
<body>
    <img src="" guoyu-src="https://o2team.github.io/misc/beidan/gpu/fengmian.png">
    <img src="" guoyu-src="https://o2team.github.io/misc/beidan/gpu/fengmian.png">
    <img src="" guoyu-src="https://o2team.github.io/misc/beidan/gpu/fengmian.png">

    <script type="text/javascript">
        var aImg = document.querySelectorAll('img');
        var len = aImg.length;
        var n = 0;//存储图片加载到的位置，避免每次都从第一张图片开始遍历
        window.onscroll = function() {
            var seeHeight = document.documentElement.clientHeight;
            var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
            for (var i = n; i < len; i++) {
                if (aImg[i].offsetTop < seeHeight + scrollTop) {
                    if (aImg[i].getAttribute('src') == '') {
                        aImg[i].src = aImg[i].getAttribute('guoyu-src');
                    }
                    n = i + 1;
                    console.log('n = ' + n);
                }
            }
        };
    </script>
</body>
</html>
```

## 二、可优化的一些点

### 2.1 滚动函数的节流

浏览器中的滚动事件是一个触发非常频繁的事件，有时候我们主观体验只是轻轻拨动了一下滚轮，但实际上触发了数次甚至数十次的onscroll事件：  
![](http://mmbiz.qpic.cn/mmbiz_jpg/NTzDrVG8ibqk9TRD4Rag0Am5hVyhYicsejCT9x4Mmcliczcrs5pSJkZRBkmgMgnQ8EYicCFjyQxXFBGc3LzIAicUZ0w/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1)

还记得我们在前文所述的懒加载策略中提到，每次滚动事件都将执行：选择页面所有图片→遍历→筛选在当前视窗内的图片→使用真实src替换占位图。以上过程是一个非常耗时的过程，尤其是在筛选符合条件的图片的步骤。因此，我们需要对滚动事件进行节流：只有当后一次触发滚动与前一次触发滚动的时间间隔大于一定阈值时，才认为这次滚动生效。

通俗地说，就是两次滚动事件之间必须大于一定的时间。这意味着用户在快速下滑页面时，筛选加载策略并不会执行，只有当用户停下或是逐渐放缓下滑速度时，才会进行筛选加载策略。若用jQuery实现上述节流步骤则如下所示（scroll事件节流阈值150ms,resize事件节流阈值100ms）：

```js
// 常规绑定
var scrollTimer = 0;
_window.scroll(function(){
    clearTimeout(scrollTimer);
    scrollTimer = setTimeout(_load, 150);
});
var resizeTimer = 0;
_window.resize(function(){
    clearTimeout(resizeTimer);
    resizeTimer = setTimeout(_load, 100);
});
```

### 2.2 预加载

顾名思义，预加载就是指预先加载一些未在当前视窗内，但是即将出现的一些图片。还是拿前文举的例子进行扩展：  
![](http://mmbiz.qpic.cn/mmbiz_png/NTzDrVG8ibqk9TRD4Rag0Am5hVyhYicsejwic28zAbTgicsZbqSJ7iba9nasuQVr8iayAzKTY3ScH1veqP6VU4ENaNdQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

可以看到，虽然当前视窗中只有3张图片，但若使用预加载，将提前加载前后各1屏幕内的图像（红色虚线内），如此一来，当用户在当前视窗内继续上滑或下滑时，该屏内的图片已经提前加载完毕，用户就不会感知到图片从白屏到显像的这种抖动过程，大大提升了用户体验。

## 三、实现懒加载的技术点

关键点如下：  
1. 页面中的img元素，如果没有src属性，浏览器就不会发出请求去下载图片（也就没有请求咯，也就提高性能咯），一旦通过javascript设置了图片路径，浏览器才会送请求。有点按需分配的意思，你不想看，就不给你看，你想看了就给你看~  
2. 如何获取正真的路径，这个简单，现在正真的路径存在元素的“data-url”（这个名字起个自己认识好记的就行）属性里，要用的时候就取出来，再设置；

开始比较之前，先了解一些基本的知识，比如说如何获取某个元素的尺寸大小、滚动条滚动距离及偏移位置距离；   
![](/assets/LazyLoad.png)

屏幕可视窗口大小：对应于图中1、2位置处

* 原生方法：window.innerHeight 标准浏览器及IE9+ \|\| document.documentElement.clientHeight 标准浏览器及低版本IE标准模式 \|\| document.body.clientHeight 低版本混杂模式
* jQuery方法： $\(window\).height\(\) 

浏览器窗口顶部与文档顶部之间的距离，也就是滚动条滚动的距离：也就是图中3、4处对应的位置；

* 原生方法：window.pagYoffset——IE9+及标准浏览器 \|\| document.documentElement.scrollTop 兼容ie低版本的标准模式 \|\| document.body.scrollTop 兼容混杂模式；
* jQuery方法：$\(document\).scrollTop\(\); 

获取元素的尺寸：对应于图中5、6位置处；左边jquery方法，右边原生方法

* $\(o\).width\(\) = o.style.width; 
* $\(o\).innerWidth\(\) = o.style.width+o.style.padding;
* $\(o\).outerWidth\(\) = o.offsetWidth = o.style.width+o.style.padding+o.style.border;
* $\(o\).outerWidth\(true\) = o.style.width+o.style.padding+o.style.border+o.style.margin;

> 注意：要使用原生的style.xxx方法获取属性，这个元素必须已经有内嵌的样式，如`<div style="...."></div>`；

如果原先是通过外部或内部样式表定义css样式，必须使用o.currentStyle\[xxx\] \|\| document.defaultView.getComputedStyle\(0\)\[xxx\]来获取样式值

获取元素的位置信息：对应与图中7、8位置处

返回元素相对于文档document顶部、左边的距离；

* jQuery：$\(o\).offset\(\).top元素距离文档顶的距离，$\(o\).offset\(\).left元素距离文档左边缘的距离
* 原生：getoffsetTop\(\)，高程上有具体说明，这边就忽略了；
* jQuery：position\(\)返回一个对象，$\(o\).position\(\).left = style.left，$\(o\).position\(\).top = style.top；

知道如何获取元素尺寸、偏移距离后，接下来一个问题就是：如何判断某个元素进入或者即将进入可视窗口区域？下面也通过一张图来说明问题。  
![](/assets/LazyLoad1.png)

外面最大的框为实际页面的大小，中间浅蓝色的框代表父元素的大小，对象1~8代表元素位于页面上的实际位置；以水平方向来做如下说明！

* 对象8左边界相对于页面左边界的偏移距离（offsetLeft）大于父元素右边界相对于页面左边界的距离，此时可判读元素位于父元素之外；
* 对象7左边界跨过了父元素右边界，此时：对象7左边界相对于页面左边界的偏移距离（offsetLeft）小于 父元素右边界相对于页面左边界的距离，因此对象7就进入了父元素可视区；
* 在对象6的位置处，对象5的右边界与页面左边界的距离 大于 父元素左边界与页面左边界的距离；
* 在对象5位置处时，对象5的右边界与页面左边界的距离 小于 父元素左边界与页面左边界的距离；此时，可判断元素处于父元素可视区外；
* 因此水平方向必须买足两个条件，才能说明元素位于父元素的可视区内；同理垂直方向也必须满足两个条件；具体见下文的源码；

## 四、扩展为jquery插件

使用方法：$\("selector"\).scrollLoad\({ 参数在代码中有说明 }\)

```js
(function($) {
    $.fn.scrollLoading = function(options) {
        var defaults = {
            // 在html标签中存放的属性名称；
            attr: "data-url",
            // 父元素默认为window
            container: window,
            callback: $.noop
        };
        // 不管有没有传入参数，先合并再说；
        var params = $.extend({}, defaults, options || {});
        // 把父元素转为jquery对象；
        var container = $(params.container);
        // 新建一个数组，然后调用each方法，用于存储每个dom对象相关的数据；
        params.cache = [];
        $(this).each(function() {
            // 取出jquery对象中每个dom对象的节点类型，取出每个dom对象上设置的图片路径
            var node = this.nodeName.toLowerCase(), url = $(this).attr(params["attr"]);
            //重组，把每个dom对象上的属性存为一个对象；
            var data = {
                obj: $(this),
                tag: node,
                url: url
            };
            // 把这个对象加到一个数组中；
            params.cache.push(data);
        });
        var callback = function(call) {
            if ($.isFunction(params.callback)) {
                params.callback.call(call);
            }
        };

        //每次触发滚动事件时，对每个dom元素与container元素进行位置判断，如果满足条件，就把路径赋予这个dom元素！
        var loading = function() {
            // 获取父元素的高度
            var contHeight = container.outerHeight();
            var contWidth = container.outerWidth();
            // 获取父元素相对于文档页顶部的距离，这边要注意了，分为以下两种情况；
            if (container.get(0) === window) {
                // 第一种情况父元素为window，获取浏览器滚动条已滚动的距离；$(window)没有offset()方法；
                var contop = $(window).scrollTop();
                var conleft = $(window).scrollLeft();
            } else {
                // 第二种情况父元素为非window元素，获取它的滚动条滚动的距离；
                var contop = container.offset().top;
                var conleft = container.offset().left;
            }
            $.each(params.cache, function(i, data) {
                var o = data.obj, tag = data.tag, url = data.url, post, posb, posl, posr;
                if (o) {
                    //对象顶部与文档顶部之间的距离，如果它小于父元素底部与文档顶部的距离，则说明垂直方向上已经进入可视区域了；
                    post = o.offset().top - (contop + contHeight);
                    //对象底部与文档顶部之间的距离，如果它大于父元素顶部与文档顶部的距离，则说明垂直方向上已经进入可视区域了；
                    posb = o.offset().top + o.height() - contop;
                    // 水平方向上同理；
                    posl = o.offset().left - (conleft + contWidth);
                    posr = o.offset().left + o.width() - conleft;
                    // 只有当这个对象是可视的，并且这四个条件都满足时，才能给这个对象赋予图片路径；
                    if ( o.is(':visible') && (post < 0 && posb > 0) && (posl < 0 && posr > 0) ) {
                        if (url) {
                            //在浏览器窗口内
                            if (tag === "img") {
                                //设置图片src
                                callback(o.attr("src", url));
                            } else {
                                // 设置除img之外元素的背景url
                                callback(o.css("background-image", "url("+ url +")"));
                            }
                        } else {
                            // 无地址，直接触发回调
                            callback(o);
                        }
                        // 给对象设置完图片路径之后，把params.cache中的对象给清除掉；对象再进入可视区，就不再进行重复设置了；
                        data.obj = null;
                    }
                }
            });
        };
        //加载完毕即执行
        loading();
        //滚动执行
        container.bind("scroll", loading);
    };
})(jQuery);
```

---

**参考文章**

* [“懒”的妙用——浅析图片懒加载技术](http://mp.weixin.qq.com/s/JYglEGYN9tnGpDg7ARPx7w?scene=25#wechat_redirect)
* [滚动加载图片（懒加载）实现原理](https://www.cnblogs.com/flyromance/p/5042187.html)
* [JavaScript：原生JS实现图片懒加载](http://blog.csdn.net/gy_u_yg/article/details/73132171)

**延伸阅读**

* [如何实现图片懒加载](https://www.cnblogs.com/luyangdong/p/7899308.html)



