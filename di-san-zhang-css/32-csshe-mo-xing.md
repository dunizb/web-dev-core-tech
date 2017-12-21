# 第2节 CSS盒模型

本章内容：CSS 实际上如何工作？盒模型是什么？margin叠加是什么？

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

## 三、margin叠加

外边距叠加是一个相当简单的概念。 但是，在实践中对网页进行布局时， 它会造成许多混淆。 简单的说， 当两个或更多个垂直边距相遇时， 它们将形成一个外边距。这个外边距的高度等于两个发生叠加的外边距的高度中的较大者。但是注意只有普通文档流中块框的垂直外边距才会发生外边距叠加。 行内框、 浮动框或绝对定位框之间的外边距不会叠加。

一般来说， 垂直外边距叠加有三种情况：

* 元素自身叠加 当元素没有内容（即空元素）、内边距、边框时， 它的上下边距就相遇了， 即会产生叠加（垂直方向）。 当为元素添加内容、 内边距、 边框任何一项， 就会取消叠加。
* 相邻元素叠加 相邻的两个元素， 如果它们的上下边距相遇，即会产生叠加。
* 包含（父子）元素叠加 包含元素的外边距隔着 父元素的内边距和边框， 当这两项都不存在的时候， 父子元素垂直外边距相邻， 产生叠加。 添加任何一项即会取消叠加。

## 四、JS如何设置获取盒模型对应的宽和高？

* **dom.style.width/height**，这种方式只能获取元素的内联样式
* **dom.currentStyle.width/height**，获取即时运行的（渲染后的）元素样式，不管是用哪种方式定义的样式，但是这个属性只有IE支持
* **window.getComputedStyle\(dom\).width/height**，作用同currentStyle，但是兼容Firefox和Chrome，通用性更好
* **dom.getBoundingClientReact\(\).width/height**，也能拿到元素渲染后的真实的宽高，这个API经常用来计算一个元素的绝对位置，这个位置是相对于视窗（Viewport）左上角左顶点，返回left、top、width、height。

## 五、BFC（边距重叠解决方案）

BFC\(Block formatting context\)直译为"块级格式化上下文"。它是一个独立的渲染区域，只有 Block-level box 参 与， 它规定了内部的 Block-level Box 如何布局，并且与这个区域外部毫不相干。

**BFC的渲染规则**

* BFC这个元素的垂直方向的边距会发生重叠
* BFC的区域不会与浮动元素的box重叠（清除浮动原理）
* BFC在页面上是一个独立的容器，外面的元素不会影响它里面的元素，反过来它里面的元素也不会影响外面的元素
* 计算BFC的高度的时候，浮动元素也会参与计算

**如何创建BFC？**

* overflow属性不为visible
* float属性不为none
* position属性为absolute或fixed
* display属性为inline-block、table-cell、table-caption、flex、inline-flex

**BFC的使用场景**

他的很常用的一个应用场景就是解决边距重叠的问题.

---

参考资料：

* [MDN CSS盒子模型](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Introduction_to_the_CSS_box_model)
* [《前端工程师手册》](https://leohxj.gitbooks.io/front-end-database/html-and-css-basic/box-module.html)



