# 第十二章 CSS盒模型

## 一、CSS 实际上如何工作？

当浏览器显示文档时，它必须将文档的内容与其样式信息结合。它分两个阶段处理文档：

1. 浏览器将 HTML 和 CSS 转化成 DOM （文档对象模型）。DOM在计算机内存中表示文档。它把文档内容和其样式结合在一起。
2. 浏览器显示 DOM 的内容。

## ![](/assets/css-rendering.png)二、盒模型

盒模型\(box model\)是CSS中的一个重要概念，它是元素大小的呈现方式。需要记住的是："every element in web design is a rectangular box"。如图：

![](/assets/css-box-model.svg)

CSS3中新增了一种盒模型计算方式：box-sizing熟悉。盒模型默认的值是content-box, 新增的值是padding-box和border-box，几种盒模型计算元素宽高的区别如下：

1. content-box，默认值，border和padding不计算入width之内
2. padding-box，padding计算入width内
3. border-box，border和padding计算入width之内









