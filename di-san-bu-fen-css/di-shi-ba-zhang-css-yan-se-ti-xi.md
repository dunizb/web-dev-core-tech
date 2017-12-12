# 第十八章 CSS 颜色体系

说到 CSS 颜色，相比大家都不会陌生，本文是我个人对 CSS 颜色体系的一个系统总结与学习，分享给大家。

先用一张图直观的感受一下与 CSS 颜色相关大概覆盖了哪些内容。

![](http://images2015.cnblogs.com/blog/608782/201606/608782-20160628102556546-1962104922.png)

接下来的行文内容大概会按照这个顺序进行，内容十分基础，可选择性跳到相应内容处阅读。

## 一、色彩关键字

色彩关键字很好理解。它表示一个具体的颜色值，且它不区分大小写。譬如这样 color:red 的 red 即是一个色彩关键字。

在 CSS3 之前，也就是 CSS 标准 2，一共包含了 17 个基本颜色，分别是：

![](http://images2015.cnblogs.com/blog/608782/201606/608782-20160628102620374-2108376967.jpg)而到了 CSS3，色彩关键字得到了极大的扩充，达到了 147 个。下面仅仅是列出了一部分:

![](http://images2015.cnblogs.com/blog/608782/201606/608782-20160628102630812-127687153.jpg)

[完整的 CSS3 色彩关键字戳我查看](http://sbco.cc/demo/color/html/)

值得注意的是，未知的关键字会让 CSS 属性无效。

### 哪些属性可以设置颜色

所有可以用到颜色值的地方，都可以用色彩关键字替代，那么在 CSS 中，什么地方可以用到颜色值呢？

* 文本的颜色 color:red
* 元素的背景色 background-color:red （包含各类渐变）
* 元素的边框 border-color:red
* 元素的盒阴影或文字阴影 box-shadow:0 0 0 1px red \| text-shadow:5px 5px 5px red
* 运用在一些滤镜当中 filter: drop-shadow\(16px 16px 20px red\)
* &lt;hr /&gt; 水平线的颜色

一些无法直接设置，但是可以被得到或者继承当前元素 currentColor 的属性：

* &lt;img&gt; 的 alt 文本。也就是，当无法显示图像时，代替图像出现的文本，会继承这个颜色值。
* ul 列表项的小点

一些比较常见的就不举例了，说一下 &lt;hr/&gt; 、 &lt;img&gt; 的 alt 文本和 ul 列表项的小点。

经过测试， &lt;hr/&gt;的颜色值，可以通过设置它的 border 的颜色值来表示。

&lt;img&gt; 的 alt 文本和 ul 列表项的小点则会继承当前元素 currentColor 的属性。

对于表单控件 &lt;input type="radio"&gt; &lt;input type="checkbox"&gt; ，暂时没有找到很好的直接改变颜色的方法，如果有知道希望不吝赐教。

## 二、transparent

transparent 的字面意思就是透明。它用来表示一个完全透明的颜色，即该颜色看上去将是背景色。

也可以理解为它是 rgba\(0,0,0,0\) 的简写。

**值得注意的是：**在 CSS3 之前，transparent 关键字不是一个真实的颜色，只能用于 background-color 和 border-color中，表示一个透明的颜色。而在支持 CSS3 的浏览器中，它被重新定义为一个真实的颜色，transparent 可以用于任何需要 color 值的地方，像 color 属性。

那么这个透明值有什么用呢？简单列举一些例子：

### **transparent 用于 border，绘制三角形**

这算是 transparent 最常见的一个用法，用于绘制三角形。

```css
<div class='div1'></div>
<div class='div2'></div>

.div1{
  width:0px;
  height:0px;
  margin:20px auto;
  border-top:50px solid yellowgreen;
  border-bottom:50px solid deeppink;
  border-left:50px solid bisque;
  border-right:50px solid chocolate;
}
.div2{
  width:0px;
  height:0px;
  margin:20px auto;
  border-bottom:50px solid deeppink;
  border-left:50px solid transparent;
  border-right:50px solid transparent;
}
```

像上文说的，由于 transparent 在低版本浏览器中（IE78）可以使用在 border、background 中，所以此方法兼容性很好，可以利用于很多场景。

### transparent 用于 border，实现增大点击热区







