# 第3节 Web图像优化之响应式图片

严格的说响应式图片不算图像优化技巧，但是某种程度上也能达到性能优化效果

说到响应式网站，我们都知道弹性布局、弹性图片、媒体查询，更多指的是布局的方式，比如说使用`max-width: 100%`，这样让图片的宽度随着容器的大小而改变，响应式设计让网站能兼容各种屏幕设备，但是在我们流量这么昂贵的时代，让一个小屏幕的手机去加载一张为大屏幕PC设计的几百K的图片，虽然这张图片会看起来非常的清晰，但是疯涨的流量消耗肯定会让用户非常的苦恼，而且我们也需要加载更长的时间才能把页面加载出来。

我们开发的目的不是挑战用户的耐心和金钱，而是让用户在使用的过程中有更好的感受，在这种情况下加载与 用户设备相匹配的小图片，即快速，又不会影响用户的体验，帮用户节省了成本，同样的，你在PC端加载一张小图片也不会影响用户的体验，帮用户节省了成本，同样的你在PC端加载一张小图片，虽然速度很快但是放大后模糊的效果会让用户觉得网站很Low很不专业,而且PC大部分使用的wifi，我们不需要去接受太多的流量，那么如何解决刚才说的这些问题呢？响应式图片的概念也就随之产生了，响应式图片，不仅仅是指图片的排版和布局，更多的还指可以根据设备大小而加载不同的图片，虽然这个过程增加了一点点UI设计师的工作量，不过那会大大改善用户的体验，那么想要响应式图片如何来实现呢？

## 一、JS或者服务端硬编码

这种方式的原理其实就是跟踪`window`的`resize`事件，然后修改一下图片的路径，我们准备三张图片，1-480.png、1-800.png、1-1600.png，分别对应三种大小设备屏幕，然后：

```js
$(function(){
    function makeImageResponsive() {
        var width = $(window).width();
        var img = $('.container img');
        var imgUrlPrefix = 'http://sandbox.runjs.cn/uploads/rs/496/pkutja85/';
        if (width <= 480) {
            img.attr('src',imgUrlPrefix + '1-480.png');
        } else if (width <= 800) {
            img.attr('src',imgUrlPrefix + '1-800.png');
        } else {
            img.attr('src',imgUrlPrefix + '1-1600.png');
        }
    }

    $(window).on('resize load', makeImageResponsive);
});
```

[在线查看](http://sandbox.runjs.cn/show/nzuscj9e)，[源码](http://runjs.cn/code/nzuscj9e)

这种形式还有一种变种的方式， 就是把屏幕信息写入Cookie中，取图片的时候在服务器端决定返回哪一张图片，这样的话就不需要我们来写脚本了，我们通过Cookie和在服务器端进行控制就可以达到目的，兼容性也比较好。

## 二、srcset 方法

通过js或者服务器端返回确实可以达到响应式图片这样一个目的，兼容性也非常的好，但是需要我们编码实现响应式的逻辑，在修改的时候也不是很方便，这属于命令式的实现，那么有没有声明式的实现呢？

也就是说我们把这几个图片地址声明在HTML中，由浏览器来决定如何加载，这样的话更加的灵活，把展示的逻辑从js脚本或者从服务器端硬编码分离出来，这样也降低的耦合，因为展现和我们的业务逻辑是分开的。

那么具体有没有呢？答案是有的。

经过了多年的努力和争辩，我们现在有了一些符合标准的标记，这些标记其实都经过了很多的修改、讨论，最后决定了这样几种，这些标记是来解决响应式图片的问题的。这些标记如下：

* srcset
* srcset 配合 sizes
* picture
* svg

这些新元素和新属性让我们可以编写多个可替换的图片，让每个客户端使用最适合的那一个，这些已经进入了官方的规范，下面我们分别来使用这些标记。

我们新建一个如下的页面：

```html
html, * {
    margin: 0;
    padding: 0;
}
.content {
    width: 100%;
    marign: 0 auto;
}
.content img {
    display: block;
    width: 100%;
    max-width: 100%;
}

<div class="content">
    <img class="image" src="http://sandbox.runjs.cn/uploads/rs/496/pkutja85/1-480.png" alt="这里是图片" />
</div>
```

这个页面很简单，就是载入了一副 480x480 的图像

`srcset` 是 `img` 标签中的一个属性，这个属性里面是可以以逗号隔开的一个或者多个字符串列表，这些字符串用来代表一系列可能使用的图片，每个字符串的格式如下：

```html
<img class="iamge" src="img/1-480.png" 
      srcset="img/1-480.png 480w, img/1-800.png 800w, img/1-1600.png 1600w" />
```

480w、800w、1600w就是尺寸描述符，一般是w结尾，也就是width的意思，以上声明了三张不同规格的图片，对于支持srcset的浏览器会怎么做呢？

[在线预览](http://sandbox.runjs.cn/show/dhdzk8e5)，[查看源码](http://sandbox.runjs.cn/code/dhdzk8e5)

从效果上看浏览器会自动感知，这个感知包含一系列因素如屏幕大小、网速、dpr等，然后去选中一张合适的图片进行加载。我们先把界面变小，如下面所示：   
![](http://dunizb.b0.upaiyun.com/article/201703/responsive_image/responsive-1.gif)

我们可以看到，当我们加载到最大的图片后，然后再把屏幕缩小后，加载的图片却并没有改变，这是什么样的一个逻辑 呢？

这个逻辑就是：**浏览器会根据屏幕的宽度和我们声明的图片的尺寸描述符去决定应该使用哪张图片，但是呢，因为我们浏览器已经在大的分辨率下加载了大的图片，那么浏览器会默认你的图片已经放在缓存中了，使用大的图片不会再有网络的消耗，那么当然是使用更大的图片更好的了，所以在我们再缩小屏幕的时候浏览器不会再去使用小图片了，这是浏览器的一个默认的行为。**

## 三、sizes 解决 srcset坑

上面使用`srcset`让我们的开发变的非常的简单，只需要声明一下将几个图片和它的宽度，就可以让浏览器自动的去选择，那么如果我们只设置`srcset`的话，会有一个小小的坑，我们试的把这个`content`它的宽度设置成 50%。看一下页面的效果，我还是把`dpr`设置为 1，这样的话他会精确到像素，让我们看得更加的清晰。

设置成 50% 之后这个图片只是占了 50% 的宽度来显示，但是当我们去加宽这个宽度的时候，可以看到，当我们到 480 的时候，图片就会改变，但其实这个图片真实的宽度只有 480/2，也就是只有 240，只在 240 的时候这个宽度就改变了，这时候我们就会会发现这个图片，它的这个自动的加载变得不是那么智能了，那么我们图片的容器变小，但是图片还是按照百分之百的宽度逻辑进行缩放，这是什么样什么样一个原因呢？这是因为呢其实，`srcset` 这一个属性还需要配合一个属性来使用，就是刚才说到的`sizes`。

sizes属性有两个值：第一个是媒体条件；第二个是源图尺寸值，在特定媒体条件下，此值决定了图片的宽度。

```html
<img srcset="360.jpg 360w,
            768.jpg 768w,
            1200.jpg 1200w,
            1920.jpg 1920w"
     sizes="
     (max-width: 360px) 100vw,
     (max-width: 768px) 90vw,
     (max-width: 1980px) 80vw,
     768px"
     src="360.jpg" alt="">
```

我们来逐条读这一个img标签的信息

* srcset，我们给浏览器准备了四个质量的图像，分别为360 768 1200 1920
* sizes，我们来告诉浏览器，在不同的环境下图像的宽度
* 当视口不大于360时，图像显示宽度为100vw，当视口不大于768的时候，图像显示宽度为90vw，以此类推。
* 最后一个src作为默认图像url引入，并且是天然的回退方案，当浏览器不认以上属性的时候，直接读取src渲染。

这样说不够直观，我们看个demo  
![](https://isux.tencent.com/static/upload/pics/8/13/2017Phjx3FJHHU10mhH7QDCdILC3.jpg)

在iphone4（320）下，图像宽度和我们设置的100vw一致，但是为什么浏览器选择了768的图像而没有选择360的?因为4的dpr是2呀^\_^，浏览器很智能的选择了质量最合适的768.

再看一下6p（414），很听话的按照我们的设置，显示了90vw。因为他的dpr更高，浏览器聪明的选择了1200质量的图像。

这里我们可以欺骗一下浏览器：

```
360.jpg 1200w,
1200.jpg 9999w
```

我们把360的图像，骗浏览器说这是1200的，然后把原本1200的扔天上去  
![](https://isux.tencent.com/static/upload/pics/8/13/2017ZgtRdX7Pv3z1JPmO9TCS5fj4.png)

浏览器果然上当了，他把360的图当成1200的来用了。这里可能有些疑问，图像的宽度为什么不是90vw了哪？因为浏览器被骗了但是自己却不知道，他依然按照1200的图像，去适配dpr。414_90%_（360/1200）约等于111.7。这种方式很智能，浏览器去根据你的sizes，从w列表里选择最适合的图像来调用显示。正因为他太智能了，在实际操作中可控性较差，有些我们想精确控制的图像显示，有时候并不能如意。而且在做lazyload的时候要处理的东西也比较复杂。

## 四、picture标签，可精确把控

一般来说对于仅仅是需要缩放的图片，我们在`srcset`属性里面列出图片的地址、像宽度，用`sizes`属性来告诉浏览器这个图片在什么样的情况下它的宽度显示成怎么样，写出这些就足够了，但是有的时候我们会希望除了缩放之外还通过其他的方式进行图片的自适应，这个时候我们就要夺回一些我们选择图片的一个控制权，所谓的夺回控制权就是说浏览器的选择逻辑我们要把它拿过来一些，我们自己来控制，那么`picture`属性就该出场了，比如我们有一个天安门的全景图，宽宽的一个图片：   
![](http://www.fansimg.com/uploads2011/05/userid280713time20110508081514.jpg)

他在大屏幕下显示非常的效果非常的好，但是在小屏幕下这个全景图显示就显得太小了，如果我们可以在手机上能放大，能展现出来一个更突出的一个裁切后的全景图那样就更好了，那么如果我们在一个`srcset`中的引入一个裁切后的版本，那么我们就无法分辨他们何时应该去选用何时不应该去选用，而使用了`picture`我们就能明确的表达出我们的意图，比如说我们只在视口宽度超过 800 像素的时候加载宽的版本，比这个小的视口中我们总是加载裁切后的版本。代码如下：

```html
<div class="content">
    <picture>
        <source media="(max-width:36em)"
                srcset="http://www.fansimg.com/uploads2011/05/userid280713time20110508081514.jpg 768w"/>
        <source srcset="http://www.fansimg.com/uploads2011/05/userid280713time20110508081514.jpg 1800w"/>
        <img src="http://www.fansimg.com/uploads2011/05/userid280713time20110508081514.jpg" alt="这里是图片"/>
    </picture>
</div>
```

效果如下  
![](http://dunizb.b0.upaiyun.com/article/201703/responsive_image/responsive-2.gif)

[在线演示](http://sandbox.runjs.cn/show/w4ncyudb)，[源码](http://sandbox.runjs.cn/cod/w4ncyudb)

source 中除了可以有媒体查询之外，还可以正对媒体查询设置多组不同格式的文件，比如 source 中还有`type`属性，可以是`image/svg+xml`等格式文件：

```html
<source type="image/svg+xml" srcset="logo.svg 480w, logo.svg 800w, logo.svg 1600w "/>
<source type="image/webp" srcset="logo.webp 480w, logo-m.webp 800w, logo-l.webp 1600w "/>
```

而且这个写法的懒加载非常好处理，只需要在传统的lazyload策略上稍加改进

* data-src
* data-srcset

在加载到的时候更换为

* src
* srcset

就轻松解决了。

## 五、使用svg

svg是可缩放的矢量图形，它是基于可扩展标记语言来生成的，svg图像可以用任何的文本编辑器来创建，但是一般人很少会去用文本编辑器来创建svg图片，因为这个非常的麻烦，除非你就只需要几条横线几个圆圈什么的。

什么是矢量图形？**矢量图形就是这些图形在变化的时候它都不会失真和变形，这些图形它不是基于像素的，而是基于一个绘制的规则**，一旦我们确定了绘制的规则，比如说颜色、形状、轮廓、大小、屏幕位置等等，确定了这些规则，即使我们发生了缩放也不会影响这些图形的绘制，所以这些图像当然也不会发生失真，但是这些矢量图形的和位图比起来其实有缺点的，**最大的缺点的就是它很难去表现色彩层次丰富和逼真的图像效果**。比如让我们的一个照片变成一个矢量图形，那么这个这个矢量图形肯定要大了去了，要变的非常的非常的大，因为每个像素他可能就要有一个绘制的规则，所以根据我们的需要可以去选择你使用矢量图形还是要位图，一般来说针对一些logo，简单的图标，比如说回收站、消息等等这样一些图标，我们可以使用svg这种矢量图形。

说到矢量图形，肯定就要想怎么样去画它了，刚才说用文本编辑器肯定不行的，这样太麻烦了，那么其实大部分支持矢量图绘制的工具其实都支持绘制svg。

相比`srcset`和`picture`，`picture`对于IE浏览器是完全不兼容的，对于老的Safari浏览器和老的安卓浏览器也是不兼容的，对于srcset，比picture稍微好一点，对于老的IE和安卓浏览器也是不兼容的。而SVG兼容性就好的多了，除了IE8不支持外，其他的浏览器几乎都支持SVG。

## 六、使用第三方库

由于我们的广告的一看就是一个位图的图形，它的里边色彩比较丰富，所以我们使用svg不太合适，使用位图要实现响应式就要配合srcset或者picture，由于兼容性的问题一般在使用srcset或者picture的时候一般会使用一个polyfill。

什么是polyfill？它来自于一个家装产品，这个家装产品是一个刮墙的产品，中国人一般把这个产品的叫做腻子，就是刮墙的腻子，它可以把墙的一些裂缝给填平，比如说我们墙皮时间长了会出现一些裂缝，这个腻子可以把它给填平，让砖块变得更平滑，在js的世界里polyfill他的意思就是一个在浏览器上的一个腻子，就是填平浏览器上面的兼容性的一些东西，比如我们使用picture，如果浏览器支持那就没有问题，我们正常的使用就可以了，如果浏览器不支持那么就需要使用一个替代的方案来解决，polyfill就是填平了这些浏览器的兼容性的一些坑。

经常使用解决兼容性的一个polyfill，polyfill其实是一种想法，一种脚本，针对某些浏览器的兼容性就会有某些的polyfill出现，对于响应式图片最有名的polyfill就是[picturefill](http://scottjehl.github.io/picturefill/)库。

我们只需要引入这个库就行了（picturefill.min.js）

```html
<div class="ad">
    <div class="owl-carousel owl-theme">
        <div class="item">
        <picture>
            <source srcset="img/ad001-l.png" media="(min-width:50em)">
            <source srcset="img/ad001-m.png" media="(min-width:30em)">
            <img src="img/ad001.png" alt="2015年度报告">
        </picture>
        </div>
        <div class="item">
        <picture>
            <source srcset="img/ad002-l.png" media="(min-width:50em)">
            <source srcset="img/ad002-m.png" media="(min-width:30em)">
            <img src="img/ad002.png" alt="新年红包">
        </picture>
        </div>
        <div class="item">
        <picture>
            <source srcset="img/ad003-l.png" media="(min-width:50em)">
            <source srcset="img/ad003-m.png" media="(min-width:30em)">
            <img src="img/ad003.png" alt="新手秘籍">
        </picture>
        </div>
    </div>
</div>

<script src="http://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js"></script>
<script src="js/vendor/OwlCarousel2/owl.carousel.min.js"></script>
<script src="js/vendor/picturefill.min.js"></script>
```

[在线查看](http://sandbox.runjs.cn/show/msx5rkla)，[源码](http://sandbox.runjs.cn/code/msx5rkla)

更多代码及完整响应式网页案例请查到这里GitHub查看。

---

**参考文章**

* [web图像常见的应用策略与技巧](https://isux.tencent.com/articles/59.html)



