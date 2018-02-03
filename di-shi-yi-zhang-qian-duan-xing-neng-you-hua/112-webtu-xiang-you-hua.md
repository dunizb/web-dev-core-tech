# 第2节 Web图像优化

前端优化有很多，图像优化也是其中的一部分。无论是渐进增强还是优雅降级，图像优化成为了开发上不可忽视的一部分。

常见的优化方案：  
1. 使用 Data URI 即（base64）编码代替图片：适用于图片大小于 2 KB，页面上引用图片总数不多的情况，原理是将图片转换为 base64 编码字符串 inline 到页面或 CSS 中，可以减少 HTTP 请求。  
2. 合并雪碧图（sprite）：移动端多图情况下，可以将多图合并到一个图中，通过 CSS 定位背景图的形式来引用图片，可以有效减少 HTTP 请求。  
3. 使用 CSS、svg、canvas 或者 iconfont 代替图片：适用于移动端或高级的浏览器，而且绘制的图片比较简单。  
4. 懒加载图片（lazyload）  
5. 使用 cdn 提供访问图片的入口。  
6. 响应式图片

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

---

**参考资料**

* [位图与矢量图区别](http://blog.csdn.net/huangfei711/article/details/51490790)
* [浅谈Web图像优化](https://zhuanlan.zhihu.com/p/30362177)
* [使用gulp进行图片优化](https://www.jianshu.com/p/d6c11d6619e0)



