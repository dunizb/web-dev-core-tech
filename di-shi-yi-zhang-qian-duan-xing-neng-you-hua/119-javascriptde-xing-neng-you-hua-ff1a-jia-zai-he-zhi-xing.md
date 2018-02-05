# 第9节 JavaScript的性能优化：加载和执行

随着Web2.0技术的不断推广，越来越多的应用使用 JavaScript 技术在客户端进行处理，从而使JavaScript在浏览器中的性能成为开发者所面临的最重要的可用性问题。而这个问题又因JavaScript的阻塞特性变的复杂，也就是说当浏览器在执行JavaScript代码时，不能同时做其他任何事情。

本文详细介绍了如何正确的加载和执行 JavaScript代码，从而提高其在浏览器中的性能。

## 一、页面如何被阻塞 {#一、页面如何被阻塞}

无论当前JavaScript代码是内嵌还是在外链文件中，页面的下载和渲染都必须停下来等待脚本执行完成。JavaScript执行过程耗时越久，浏览器等待响应用户输入的时间就越长。**浏览器在下载和执行脚本时出现阻塞的原因在于，脚本可能会改变页面或JavaScript的命名空间，它们对后面页面内容造成影响。**一个典型的例子就是在页面中使用`document.write()`。

```html
<html>
<head>
<title>Source Example</title>
</head>
<body>
<p>
<script type="text/javascript">
document.write("Today is " + (new Date()).toDateString());
</script>
</p>
</body>
</html>
```

当浏览器遇到`<script>`标签时，当前HTML页面无从获知JavaScript是否会向`<p>`标签添加内容，或引入其他元素，或甚至移除该标签。因此，这时浏览器会停止处理页面，先执行JavaScript代码，然后再继续解析和渲染页面。

同样的情况也发生在使用`src`属性加载JavaScript的过程中，浏览器必须先花时间下载外链文件中的代码，然后解析并执行它。在这个过程中，页面渲染和用户交互完全被阻塞了。

## 二、脚本位置 {#二、脚本位置}

HTML 4 规范指出 `<script>` 标签可以放在 HTML 文档的`<head>`或`<body>`中，并允许出现多次。Web 开发人员一般习惯在 `<head>` 中加载外链的 JavaScript，接着用 `<link>` 标签用来加载外链的 CSS 文件或者其他页面信息。例如清单 2

清单 2 低效率脚本位置示例
```html
<html>
<head>
<title>Source Example</title>
<script type="text/javascript" src="script1.js"></script>
<script type="text/javascript" src="script2.js"></script>
<script type="text/javascript" src="script3.js"></script>
<link rel="stylesheet" type="text/css" href="styles.css">
</head>
<body>
<p>Hello world!</p>
</body>
</html>
```

然而这种常规的做法却隐藏着严重的性能问题。在清单 2 的示例中，当浏览器解析到 `<script>` 标签（第 4 行）时，浏览器会停止解析其后的内容，而优先下载脚本文件，并执行其中的代码，这意味着，其后的 styles.css 样式文件和`<body>`标签都无法被加载，由于`<body>`标签无法被加载，那么页面自然就无法渲染了。

因此在该JavaScript代码完全执行完之前，页面都是一片空白。图 1 描述了页面加载过程中脚本和样式文件的下载过程。
![图 1 JavaScript 文件的加载和执行阻塞其他文件的下载](/assets/js_loading.jpg)

我们可以发现一个有趣的现象：第一个 JavaScript 文件开始下载，与此同时阻塞了页面其他文件的下载。此外，从 `script1.js` 下载完成到 `script2.js` 开始下载前存在一个延时，这段时间正好是 `script1.js` 文件的执行过程。每个文件必须等到前一个文件下载并执行完成才会开始下载。在这些文件逐个下载过程中，用户看到的是一片空白的页面。

从 IE 8、Firefox 3.5、Safari 4 和 Chrome 2 开始都允许并行下载 JavaScript 文件。这是个好消息，因为`<script>`标签在下载外部资源时不会阻塞其他`<script>`标签。遗憾的是，JavaScript 下载过程仍然会阻塞其他资源的下载，比如样式文件和图片。尽管脚本的下载过程不会互相影响，但页面仍然必须等待所有 JavaScript 代码下载并执行完成才能继续。因此，尽管最新的浏览器通过允许并行下载提高了性能，但问题尚未完全解决，脚本阻塞仍然是一个问题。

由于脚本会阻塞页面其他资源的下载，因此推荐将所有`<script>`标签尽可能放到`<body>`标签的底部，以尽量减少对整个页面下载的影响。例如清单 3
```html
<html>
<head>
<title>Source Example</title>
<link rel="stylesheet" type="text/css" href="styles.css">
</head>
<body>
<p>Hello world!</p>
<!-- Example of efficient script positioning -->
<script type="text/javascript" src="script1.js"></script>
<script type="text/javascript" src="script2.js"></script>
<script type="text/javascript" src="script3.js"></script>
</body>
</html>
```

这段代码展示了在 HTML 文档中放置`<script>`标签的推荐位置。尽管脚本下载会阻塞另一个脚本，但是页面的大部分内容都已经下载完成并显示给了用户，因此页面下载不会显得太慢。**这是优化 JavaScript 的首要规则：将脚本放在底部。**

## 三、组织脚本 {#三、组织脚本}

由于每个`<script>`标签初始下载时都会阻塞页面渲染，所以减少页面包含的`<script>`标签数量有助于改善这一情况。这不仅针对外链脚本，内嵌脚本的数量同样也要限制。浏览器在解析 HTML 页面的过程中每遇到一个`<script>`标签，都会因执行脚本而导致一定的延时，因此最小化延迟时间将会明显改善页面的总体性能。

这个问题在处理外链 JavaScript 文件时略有不同。考虑到 HTTP 请求会带来额外的性能开销，因此下载单个 100Kb 的文件将比下载 5 个 20Kb 的文件更快。**也就是说，减少页面中外链脚本的数量将会改善性能。**



