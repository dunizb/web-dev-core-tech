# 第八章 理解HTML的语义化

## 一、什么是HTML的语义化

在HTML 5出来之前，我们用div来表示页面章节，但是这些div都没有实际意义。（即使我们用css样式的id和class形容这块内容的意义）。这些标签只是我们提供给浏览器的指令，只是定义一个网页的某些部分。但现在，那些之前没“意义”的标签因为因为html5的出现消失了，这就是我们平时说的“语义”。

看下图没有用div标签来布局

![](/assets/html-yuyi.png)

如上图那个页面结构没有一个div，都是采用html5语义标签（用哪些标签，关键取决于你的设计目标）。

但是也不要因为html5新标签的出现，而随意用之，错误的使用肯定会事与愿违。所以有些地方还是要用div的，就是因为div没有任何意义的元素，他只是一个标签，仅仅是用来构建外观和结构。因此是最适合做容器的标签。

W3C定义了这些语义标签，不可能完全符合我们有时的设计目标，就像制定出来的法律不可能流传100年都不改变，更何况它才制定没多久，不可能这些语义标签对所以设计目标的适应。只是一定程度上的“通用”，我们的目标是让爬虫读懂重要的东西就够了。

**不能因为有了HTML 5标签就弃用了div，每个事物都有它的独有作用的。**

## 二、为什么要语义化

1. 为了在没有CSS的情况下，页面也能呈现出很好地内容结构、代码结构:**为了裸奔时好看**；
2. **用户体验**：例如title、alt用于解释名词或解释图片信息、label标签的活用；
3. **有利于SEO**：和搜索引擎建立良好沟通，有助于爬虫抓取更多的有效信息：爬虫依赖于标签来确定上下文和各个关键字的权重；
4. **方便其他设备解析**（如屏幕阅读器、盲人阅读器、移动设备）以意义的方式来渲染网页；
5. **便于团队开发和维护**，语义化更具可读性，是下一步吧网页的重要动向，遵循W3C标准的团队都遵循这个标准，可以减少差异化。

## 三、写HTML代码时应注意什么？

1. 尽可能少的使用无语义的标签div和span；
2. 在语义不明显时，既可以使用div或者p时，尽量用p, 因为p在默认情况下有上下间距，对兼容特殊终端有利；
3. 不要使用纯样式标签，如：b、font、u等，改用css设置。
4. 需要强调的文本，可以包含在strong或者em标签中（浏览器预设样式，能用CSS指定就不用他们），strong默认样式是加粗（不要用b），em是斜体（不用i）；
5. 使用表格时，标题要用caption，表头用thead，主体部分用tbody包围，尾部用tfoot包围。表头和一般单元格要区分开，表头用th，单元格用td；
6. 表单域要用fieldset标签包起来，并用legend标签说明表单的用途；
7. 每个input标签对应的说明文本都需要使用label标签，并且通过为input设置id属性，在lable标签中设置for=someld来让说明文本和相对应的input关联起来。

## 四、HTML5新增了哪些语义标签

header元素













---

参考：[http://www.html5jscss.com/html5-semantics-section.html](http://www.html5jscss.com/html5-semantics-section.html)
