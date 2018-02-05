# 第2节 Web图像优化

前端优化有很多，图像优化也是其中的一部分。无论是渐进增强还是优雅降级，图像优化成为了开发上不可忽视的一部分。

常见的优化方案：

1. 使用 Data URI 即（base64）编码代替图片：适用于图片大小于 2 KB，页面上引用图片总数不多的情况，原理是将图片转换为 base64 编码字符串 inline 到页面或 CSS 中，可以减少 HTTP 请求。  
2. 合并雪碧图（sprite）：移动端多图情况下，可以将多图合并到一个图中，通过 CSS 定位背景图的形式来引用图片，可以有效减少 HTTP 请求。  
3. 使用 CSS、svg、canvas 或者 iconfont 代替图片：适用于移动端或高级的浏览器，而且绘制的图片比较简单。  
4. 懒加载图片（lazyload）  
5. 使用 cdn 提供访问图片的入口。  
6. 响应式图片

本文主要介绍如下内容：

1. 真的要用图片吗？
2. 位图与矢量图区别
3. 图片格式的选择
4. 优化JPG和PNG
5. 优化SVG
6. 自动优化
7. WebP全方位介绍

## 一、真的要用图片吗？

要实现需要的效果，真的需要图片吗？这是首先要问自己的问题。浏览器和Web标准的发展速度极快，记得数年前我在用微软Silverlight 1.0写视频播放器的时候，中文还不能使用自定义字体显示，所以那时候写了很多糟糕的代码把需要的文字在服务器上生成图片并缓存起来。用户下载起来很慢，搜索引擎也完全无法检索这些文字。但是现在不一样了，很多特效（渐变、阴影、圆角等等）都可以用纯粹的HTML、CSS、SVG等加以实现，实现这些效果少则寥寥数行代码，多则加载额外的库（一张普通的照片比[非常强大的效果库](http://daneden.github.io/animate.css/)也大了许多）。这些效果不但需要的空间很小，而且在多设备、多分辨率下都能很好的工作，在低级浏览器上也可以实现较好的功能降级。**因此在存在备选技术的情况下，应该首先选择这些技术，只有在不得不使用图片的时候才加入真正的图片。**

备选技术：

* CSS效果、CSS动画。提供与分辨率无关的效果，在任何分辨率和缩放级别都可以显示得非常清晰，占用的空间也很小。
* 网络字体。现在连很多图标库都是用字体方式提供，保持文字的可搜索性同时扩展显示的样式。

## 二、位图与矢量图区别

图像优化的前提是需要了解图像的基本原理。常规的图像格式分为矢量图和位图。

### 2.1 概念

**矢量图**，使用线段和曲线描述图像,所以称为矢量,同时图形也包含了色彩和位置信息。  
**位图**，使用像素点来描述图像，也称为点阵图像。

矢量图  
![矢量图](http://mmbiz.qpic.cn/mmbiz_jpg/d8tibSEfhMMoHRYtjC4DHwSVQ3LU7x9uoHDbdOV0cZfoaAWNJ4amSEptpamicmREVLqSnmKnf8pUT3yyrZO4GTLA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1)

位图  
![位图](http://mmbiz.qpic.cn/mmbiz_jpg/d8tibSEfhMMoHRYtjC4DHwSVQ3LU7x9uo21ZdnCrexrNsJQdwyNaO1cicTCXrwuiaib81Nj2BiabMSjPibZfTAHHlA4A/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1)

### 2.2 与分辨率的相关性

**矢量图**与分辨率无关，可以将它缩放到任意大小和以任意分辨率在输出设备上打印出来，都不会影响清晰度。  
**位图**是由一个一个像素点产生，当放大图像时，像素点也放大了，但每个像素点表示的颜色是单一的，所以在位图放大后就会出现马赛克状。

### 2.3 色彩丰富度

矢量图 色彩不丰富，无法表现逼真的实物，矢量图常常用来表示标识、图标、Logo等简单直接的图像。

位图 表现的色彩比较丰富，可以表现出色彩丰富的图象，可逼真表现自然界各类实物。

### 2.4 文件类型

矢量图 格式很多，如AdobeIllustrator的 _.AI、_.EPS和SVG、AutoCAD的 _.dwg和dxf、Corel DRAW的 _.cdr等。

位图 的文件类型也很多，如 _.bmp、_.pcx、_.gif、_.jpg、_.tif、_.png、photoshop的 \*.psd等。

### 2.5 占用空间

矢量图 表现的图像颜色比较单一，所以所占用的空间会很小。

位图 表现的色彩比较丰富，所以占用的空间会很大，颜色信息越多，占用空间越大，图像越清晰，占用空间越大。

### 2.6 相互转化

经过软件矢量图可以很轻松的转化为位图，而位图要想转换为矢量图必须经过复杂而庞大的数据处理，而且生成的矢量图质量也会有很大的出入。

## 三、图片格式的选择

如果效果真的需要图片来表现，那么选择图片格式是优化的第一步。我们经常听到的词语包括矢量图、标量图、SVG、有损压缩、无损压缩等等，我们首先说明各种图片格式的特点

| 图片格式 | 压缩方式 | 透明度 | 动画 | 浏览器兼容性 | 适应场景 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| JPEG | 有损压缩 | 不支持 | 不支持 | 所有 | 复杂颜色及形状、尤其是照片 |
| GIF | 无损压缩 | 支持 | 支持 | 所有 | 简单颜色，动画 |
| PNG | 无损压缩 | 支持 | 不支持 | 所有 | 需要透明时 |
| APNG | 无损压缩 | 支持 | 支持 | Firefox、Safari、iOS Safari、Chrome | 需要半透明效果的动画 |
| WebP | 有损压缩 | 支持 | 支持 | Opera、Android Chrome、Android Browser | 复杂颜色及形状、浏览器平台可预知 |
| SVG | 无损压缩 | 支持 | 支持 | 所有（IE8以上） | 简单图形，需要良好的放缩体验，需要动态控制图片特效 |

其中**APNG**和**WebP**格式出现的较晚，尚未被Web标准所采纳，只有在特定平台或浏览器环境可以预知的情况下加以采用，虽然均可以在不支持的环境中较好的功能降级，但本节暂不讨论这两种格式。图片格式选择过程如下。  
![图片格式选择过程](http://dl2.iteye.com/upload/attachment/0104/5524/032d9970-36b2-36f9-82c9-d84c17417122.jpg)

**颜色丰富的照片，JPG是通用的选择**

* 人眼的结构很适合查看JPG压缩后的照片，可以充分的忽略并在脑中补齐细节
* JPG在压缩率不高时保留的细节还是不错的
* WebP能够比JPG减少30%的体积，但目前兼容性较差

**如果需要较通用的动画，GIF是唯一可用的选择**

* GIF支持的颜色范围为256色，而且仅支持完全透明/完全不透明
* GIF在显示颜色丰富的动画时可能出现颜色不全、边缘锯齿等问题

**如果图片由标准的几何图形组成，或需要使用程序动态控制其显示特效，可以考虑SVG格式**

* SVG是使用XML定义的矢量图形，生成的图片在各种分辨率下均可自由放缩
* SVG中可以通过JavaScript等接口自由变换图片特效，可以完成其中部分元素的自由旋转、移动、变换颜色等

**如果需要清晰的显示颜色丰富的图片，PNG比较好**

* PNG-8能够显示256种颜色，但能够同时支持256阶透明，因此颜色数较少但需要半透明的情景（如微信动画大表情）可以考虑PNG-8
* PNG-24可以显示真彩色，但不支持透明，颜色丰富的图片推荐使用（如屏幕截图、界面设计图）
* PNG-32可以显示真彩色，同时支持256阶透明，效果最好但尺寸也最大

## 四、优化JPG和PNG

选择了正确的图片格式，按照正确的大小生成了图片后，我们还需要对图片进行进一步优化，这种优化一般分两步进行：  
1. 有损优化，删除没有出现或极少出现过的颜色，合并相邻的相近颜色。这一步并不必须，如PNG格式就直接进入下一步  
2. 无损优化，压缩数据，删除不必要的信息

JPG和PNG格式的图片生成后，一般还有进一步优化的空间，例如JPG格式的照片中，可能携带有相机的Exif信息，PNG格式的图片中可能带有Fireworks等软件的图层信息等。去除这些额外信息后，还可以通过减小图片的调色板，去除没有出现过的颜色，以及合并相邻的相同颜色等手段来进行优化。原理性的内容这里不再赘述，仅介绍工程中可用的优化工具。

不同格式的图片有一系列工具，这些工具有有更多种参数调节方案，常见的几种调节工具有：

| 工具 | 用途 |
| :--- | :--- |
| [jpegtran](http://jpegclub.org/jpegtran/) | 优化JPG图片 |
| [OptiPNG](http://optipng.sourceforge.net/) | 无损PNG优化 |
| [AdvPNG](http://advancemame.sourceforge.net/doc-advpng.html) | 无损PNG优化 |
| [PNGQuant](http://pngquant.org/) | 有损PNG优化 |

如果你真的需要追求各种图片的极限压缩，可以参阅这些工具的文档，但是对于一般的Web应用，面对的图片种类多样，几乎不可能在工程中实现对每种工具的独立配置，因此推荐使用以下工具来进行优化。这些工具往往使用了上表中的一种或几种优化工具。

[**mageOptim \(Mac\)**](https://imageoptim.com/)

Mac平台下非常赞的图片优化工具，只需要把需要优化的图片拖拽进ImageOptim，就能够完成对图片的优化。设置选择的也很丰富，目前支持JPG和PNG的优化。这是我在写文章时最常用到的工具，把网站用到的图片拖进去，优化就完成了~  
![](http://dl2.iteye.com/upload/attachment/0104/5528/cbf610aa-5906-3912-a4ad-c829c7e2a968.jpg)

[**Kraken \(Web\)**](https://kraken.io/)

在免费模式下可以上传图片，优化后打包下载，很多国外企业也选择了它的收费服务。亲自测试Kraken的图片优化结果比ImageOptim一般要小3%左右，效果不错，当然价格也不错。适合偶尔有图片优化需求，或者不在开发机上没有优化软件可以使用的情况。  
![](http://dl2.iteye.com/upload/attachment/0104/5530/ad8f3150-68c7-3132-8bf8-d94e2b210751.jpg)

[**智图 \(Web\)**](http://zhitu.tencent.com/)

腾讯ISUX团队有篇文章介绍智图：[http://isux.tencent.com/zhitu.html](http://isux.tencent.com/zhitu.html)  
国货当自强，腾讯的智图工具推出不久，但实测效果很好，而且提供了[Gulp的自动化支持](https://www.npmjs.org/package/gulp-imageisux)，这部分会在后面自动优化章节介绍。只想建议一句，Kraken的首页比智图美好几百倍…… 而且把压缩前的PNG和压缩后的JPG放在一起对比大小，真的没关系么~  
![](http://dl2.iteye.com/upload/attachment/0104/5532/6ad6ecca-e2c6-3a8c-b675-f0c207159d6e.jpg)

## 五、优化SVG

所有较新的浏览器都支持可缩放矢量图\(SVG\)，SVG是基于XML的图片格式，适用于二维图片。可以将SVG标记直接嵌入网页，也可以作为外部资源嵌入。可以通过大多数基于矢量的绘图软件创建SVG文件。这是一段简单的SVG图形：  
![](http://dl2.iteye.com/upload/attachment/0104/5534/63dc1405-f187-3905-a90b-08c8c2144656.jpg)

这个圆形轮廓为黑色，背景为红色，从Adobe Illustrator直接导出。可以从中看到大量元数据，例如图层信息、注释和XML名称空间等等，在浏览器中呈现资源时，通常不需要这些数据。因此我们需要使用一些工具去除这些不必要的元数据，仅保留必须的标记。

[SVGO](https://github.com/svg/svgo)工具可以缩减SVG文件的体积，在这个的例子中，SVGO能够将Illustrator生成的SVG文件大小减小58%，从470字节缩减到199字节。

由于SVG是基于XML的格式，本质上是纯文本，所以，还可以采用GZIP压缩来减小传输大小，当然这需要一些服务器配置，例如在apache服务器中设置：

```
AddType image/svg+xml .svg
AddOutputFilterByType DEFLATE image/svg+xml
```

来对SVG文件启用GZip压缩（当然你还需要先加载deflate模块并进行适当配置，GZip的配置超出了本文的范畴，这部分内容请自行Google）

## 六、自动优化

前面说了太多关于如何优化各种不同格式图片的方法和工具，优化图片需要大量重复性的劳动，作为工程师显然不会忍受这一点，因此也产生出了很多工具对图片进行自动优化，这里主要介绍CDN、Grunt/Gulp、Google PageSpeed三种方式。

### 6.1 自动优化：CDN

使用CDN对图片自动进行优化，我在国外的CDN提供商处很少见到这类服务，倒是国内的两大新秀CDN七牛和又拍在这方面都做了大量工作。其工作方式为，向CDN请求图片的URL参数中包含了图片处理的参数（格式、宽高等），CDN服务器根据请求生成所需的图片，发送到用户浏览器。

七牛云存储的[图片处理接口](http://developer.qiniu.com/docs/v6/api/reference/fop/image/)极其丰富，覆盖了图片的大部分基本操作，例如：

* 图片裁剪，支持多种裁剪方式（如按长边、短边、填充、拉伸等）
* 图片格式转换，支持JPG, GIF, PNG, WebP等，支持不同的图片压缩率
* 图片处理，支持图片水印、高斯模糊、重心处理等

七牛云存储的图片处理接口使用并不复杂，例如下面这张原图：

![](http://dl2.iteye.com/upload/attachment/0104/5542/68b3f97a-eb0c-3c50-b8a4-f0fb003ac902.jpg)

我们通过如下URL请求，裁剪正中部分，等比缩小生成200×200缩略图：

```
http://qiniuphotos.qiniudn.com/gogopher.jpg?imageView2/1/w/200/h/200
```

![](http://dl2.iteye.com/upload/attachment/0104/5560/ccfa5ea0-bf53-3562-af64-912f1ade6e29.jpg)

### 6.2 自动优化：Gulp

使用gulp进行图片压缩首先要寻找合适的插件，本文主要介绍两种：

* [gulp-imagemin](https://link.jianshu.com/?t=https://www.npmjs.com/package/gulp-imagemin)
* [gulp-smushit](https://link.jianshu.com/?t=https://www.npmjs.com/package/gulp-smushit)

**gulp-imagemin**

这个插件可以用来压缩PNG, JPEG, GIF和SVG图片

优势：

* 有很多定制选项
* 可以引入更多第三方优化插件，例如pngquant \(见下面例子\)
* 可以处理多种图片格式

劣势：没有verbose输出选项

gulpfile.js （包含pngquant）

```js
var gulp = require('gulp');
var imagemin = require('gulp-imagemin'),
    pngquant = require('imagemin-pngquant');
gulp.task('imagemin', function () {
    return gulp.src('src/*')
        .pipe(imagemin({
            progressive: true,
            use: [pngquant()]
        }))
        .pipe(gulp.dest('imagemin-dist'));
});
```

输出结果如下，可以看到图片体积减小了57.6KB \(2.2%\)

```
[10:37:25] Using gulpfile ~/Desktop/Work/Practice/gulp-compressor/gulpfile.js
[10:37:25] Starting 'imagemin'...
[10:37:39] gulp-imagemin: Minified 2 images (saved 57.6 kB - 2.2%)
[10:37:39] Finished 'imagemin' after 14 s
```

**gulp-smushit**

Smushit是Yahoo开发的一款用来优化PNG和JPG的插件，它的原理是移除图片文件中不必要的数据。这是一个无损压缩工具，这意味着优化不会改变图片的显示效果和质量。

优势：易配置

劣势：只能处理JPG和PNG

gulpfile.js：

```js
var gulp = require('gulp');
var smushit = require('gulp-smushit');
gulp.task('smushit', function () {
    return gulp.src('src/*')
        .pipe(smushit({
            verbose: true
        }))
        .pipe(gulp.dest('smushit-dist'));
});
```

输出结果：

```
[10:48:26] Using gulpfile ~/Desktop/Work/Practice/gulp-compressor/gulpfile.js
[10:48:26] Starting 'smushit'...
[10:50:31] /Users/fengyu/Desktop/Work/Practice/gulp-compressor/src/IMG_3881.jpg
[10:50:31] gulp-smushit: Compress rate % 0
[10:50:31] gulp-smushit: 1188874 bytes to 1188223 bytes
[10:51:09] /Users/fengyu/Desktop/Work/Practice/gulp-compressor/src/winkpax@2x.png
[10:51:09] gulp-smushit: Compress rate % 69
[10:51:09] gulp-smushit: 1458533 bytes to 446429 bytes
[10:51:09] Finished 'smushit' after 2.7 min
```

这个例子可以看出smushit对PNG图片具有更好的压缩效果，压缩率高达69%，而且压缩前后图片质量没有明显的改变，这足以说明图片压缩的必要性。

## 七、WebP 全方位介绍

[**WebP**](http://blog.oneapm.com/tags-WebP.html)的新型图片格式，旨在在不影响[用户体验](http://blog.oneapm.com/tags-user-experience.html)的情况下减少图片的大小。

### 7.1 WebP是什么?

WebP 是由谷歌开发的一种图像格式，与 JPEG 图像相比，这种格式最多可以减少图片文件大小的 34%。从而显著优化页面加载时间和带宽使用情况。 
![WebP是什么](http://blog.teamtreehouse.com/wp-content/uploads/2014/01/file-size-comparison.png)

上图是 JPEG 和 80% 压缩质量的 WebP 图像之间的比较

根据谷歌团队的介绍，自从去年 Chrome Web Store 转而使用 WebP 后，整个网站图片的大小平均减少 30%。这相当于每天节省了数 tb 的带宽！谷歌的 Play Store 目前也使用 WebP 格式储存图像。

WebP 格式支持 无损 和 有损 的图像压缩、alpha 通道透明度、颜色配置文件、元数据和动画，这些特性使 WebP 格式成为一个为网络上所使用的图像提供的一站式的解决方案。

俯视过去几年互联网浏览趋势的演变，你就会发现，开发一个新的图片格式越来越重要。移动浏览现在占全球互联网流量的 15%，这一数字仍在上升，然而，这些移动设备的网络依赖的数据并没有以同样的速度提高。大部分人的移动浏览仍然被低带宽连接所限制，网页的加载速度慢得令人沮丧，而使用 WebP 之类技术来减少 web 页面的整体负载则有助于缓解这一现象。

### 7.2 使用 WebP 的利弊

与传统图像格式如 JPEG、PNG 或 GIF 相比，使用 WebP 有很多优势：
- 更小的文件尺寸
- 更高的质量——与其他相同大小不同格式的压缩图像比较
- 完全免费——开源的 WebP 是 2010 年由谷歌根据 BSD 协议所提供的
- 一种格式可以取代所有其他格式—— WebP 有能力取代 JPEG、 PNG 和 GIF，成为在 Web 上图像的单一格式

但是，尽管自 2010 年起 WebP 便已推出，但它的支持仍然是有限的，这是现今使用 WebP 的主要缺点：
- 浏览器支持——WebP 目前支持桌面上的 Chrome 和 Opera 浏览器。手机支持仅限于原生的 Android 浏览器和 Android 系统上的 Chrome 浏览器,后面会介绍关于如何处理这个限制的方法。
- 本地操作系统支持——WebP 目前不被任何操作系统原生支持。谷歌只是基本的开发了 Web 上的格式，但要将其添加到 Windows 成像组件中还需要有编解码器支持，在这里给大家附个 [下载链接](https://developers.google.com/speed/webp/docs/webp_codec) .

> 注 : WebP 图像也可以使用在 Android 应用程序和 iOS 应用程序上，但在这篇文章中我们将先关注于 Web 应用程序.

### 7.3 真实的页面响应时间

评价网站性能好坏的一个主要指标就是 **页面响应时间** ，也就是说用户打开完整页面的时间。任何一项技术的使用都是有风险的，更何况是在公司的网站上，你必须要有一定的数据和证据来说服你的 Boss 或者相关负责人才行。

现在业内的很多前端监控工具都是基于拨测的模拟访问。假设，在 **网络良好、用户机器良好、用户使用pc有线网、运营商及DNS无任何问题等等情况下** 的访问，这是真实的用户访问么？！！

只有像类似于下图这样的针对用户访问时间的真实监控才能用来作为推动某项技术落地于网站的有力证据。
![](http://blog.oneapm.com/content/images/2015/12/QQ--20151229104612.jpg)

同样重要的是要定位到 **图片资源加载** 的时间，如果拖慢网站页面加载的主要原因就是图片资源的话，那就算你不抓紧的话，老板也会逼着你让你去解决这个问题，这个时候，WebP 就派上用场了。
![](http://blog.oneapm.com/content/images/2015/12/----_2015-12-29T03-19-03-099Z.png)

之前做过前端优化的工作，国内外的 [前端性能优化](http://blog.oneapm.com/tags-qian-duan-xing-neng-you-hua.html) 工具也使用了不少，现阶段可以较好实现上面两个功能的工具有： [OneAPM Browser Insight](http://www.oneapm.com/bi/feature.html) 、 [AppDynamics](http://www.appdynamics.com/) 、 [Ruxit](https://ruxit.com/) ，大家有兴趣的话可以去尝试下。

### 7.4 将图像转换为WebP

现在大家应该了解了为什么 WebP 与众不同，为什么考虑在 Web 应用程序中使用它，下面介绍的是如何将你现有的图像转换为 WebP 格式。

谷歌已开发了大量实用的命令行将图像转换为 WebP，每个人都可以从谷歌开发者网站下载。当你有一个实用程序的副本之后，你可能想要将实用程序的 bin 文件夹添加到您的本地路径，这可以通过将以下代码添加到你的主目录 下的 `.bash_profile` 文件中来实现(针对 Mac/Linux 系统)。
```shell
PATH=$PATH:"/path/to/libwebp-utilities/bin"  
export PATH
```

你需要更新下引用路径来表示你的系统上 WebP 实用程序文件夹的位置。重新启动终端止，就能够访问命令行实用工具了。

另外，Mac 可以使用 homebrew 来安装实用程序。
```
brew install webp
```

> 注 :homebrew 不是总能匹配项目网站的最新版的。

安装实用程序完成后，就可以使用 cwebp 将 JPEG 或 PNG 图像转换成 WebP 格式。
```
cwebp [options] -q quality input.jpg -o output.webp
```

质量选项应该是 0 (差)到 100 (很好)之间的数字，典型的质量值大约是 80，但是你也可以多多尝试，直到文件质量和大小都让你满意。

完整的选项列表，可以使用此实用工具运行带有 -longhelp 的 cwebp 命令，或者查看 [帮助文档](https://developers.google.com/speed/webp/docs/cwebp) 。

> 注 :也可以使用 dwebp 实用程序将 WebP 图像转换回 PNG、PAM、PPM 或 PGM 图像。
> `dwebp input_file.webp [options] [-o output_file]`

### 7.5 PageSpeed 自动转换模块

很高兴有工具可以手动将图像格式转换成 WebP 。

但正如我们之前看到的，并不是所有的浏览器都支持这种图像格式，因此需要一种可以预览 WebP 图像，并且使不支持 WebP 格式的浏览器可以用 JPEGs 或 PNGs 替代的服务。本来可以写一些复杂的服务器端代码，找出用户的浏览器是否支持 WebP 然后提供适当的文件，但幸运的是我们不需要这么做。

由谷歌开发的 PageSpeed 模块有一个功能，会自动将图像转换成 WebP 格式和服务端的浏览器所支持的格式。很神奇，就像魔术一样，而且设置也很简单，只需要将一行代码添加到你的主机配置中，启用这个特性。
```
ModPagespeedEnableFilters convert_jpeg_to_webp
```

> 注 :如果你不熟悉 PageSpeed 模块，可以看下这个英文的帮助文档，关于 [如何在 Apache Web 服务器上设置 mod_pagespeed ](http://blog.teamtreehouse.com/automating-web-performance-best-practices-mod_pagespeed).

使用 `convert_jpeg_to_webp` 选择器可以使 PageSpeed 模块在适当的地方开启图像优化和自动转换 WebP 图像的服务。最初这只适用于 JPEG 图像，但你也可以通过开启 `convert_png_to_jpeg` 选择器使其支持 png 图像。
```
ModPagespeedEnableFilters convert_png_to_jpeg
```

根据谷歌报导,目前有超过 300000 个网站使用 PageSpeed 模块(或服务)为用户提供 WebP 图像.

在自己的服务器上使用 PageSpeed 模块的方法非常简单，可以充分利用 WebP 的优势。


---

**参考资料**

* [位图与矢量图区别](http://blog.csdn.net/huangfei711/article/details/51490790)
* [浅谈Web图像优化](https://zhuanlan.zhihu.com/p/30362177)
* [使用gulp进行图片优化](https://www.jianshu.com/p/d6c11d6619e0)
* [网站性能优化— WebP 全方位介绍](http://soqu.me/share?data=MTQwMjAyMTQ3ODQzNTI5NTQ2MzE4NTMyMiYyQ29IbXE=)



