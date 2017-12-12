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

按钮是我们网页设计中十分重要的一环，而按钮的设计也与用户体验息息相关。让用户更容易的点击到按钮无疑能很好的增加用户体验，尤其是在移动端，按钮通常都很小，但是有时由于设计稿限制，我们不能直接去改变按钮元素的高宽。那么这个时候有什么办法在不改变按钮原本大小的情况下去增加他的点击热区呢？

这里，借助透明的 border 可以轻松帮我们实现（我 之前一篇文章写到过，利用伪元素也可以实现），利用一层透明的 border:20px solid transparent 我们可以这样写：

```css
<div>Btn</div>

div{
  width:140px;line-height:48px;
  text-align:center;
  margin:50px auto;
  color:#333;
  cursor:pointer;
  background:hsl(200, 60%, 60%);
  border:20px solid transparent;
  background-clip: padding-box;
}
div:hover{
  background:hsl(200, 60%, 50%);
  background-clip: padding-box;
}
div:active{
  background:hsl(200, 60%, 70%);
  background-clip: padding-box;
}
```

试着将光标靠近 Btn，会发现在还未到达有颜色区域之前，就已经触发了鼠标的交互响应事件 hover，利用这一点在移动端可以很好的扩大按钮的可点击区域又不至于改变按钮本身的形状。像这样：

![](http://images2015.cnblogs.com/blog/608782/201605/608782-20160527112625428-906375003.gif)

这里我们将 border 用于了扩大鼠标点击区域，然而真实情况是有的时候我们的按钮必须要用到 border，而 border 又只能设置一重（无法像 box-shadow和 渐变一样设置多重 border），这个时候如果还需要运用这种方法，可以使用内阴影 box-shadow模拟一层 border，像这样：

```css
div{
  width:140px;line-height:48px;
  text-align:center;
  margin:50px auto;
  color:hsla(328, 100%, 54%,0.8);
  cursor:pointer;
  border:20px solid transparent;
  background-clip: padding-box;
  box-shadow:inset 0 0 0 1px hsla(328, 100%, 54%,0.8);
}
div:hover{
  background:hsla(328, 100%, 54%,0.8);
  color:white;
  background-clip: padding-box;
}

div:active{
  background:hsla(328, 100%, 54%,1);
  color:white;
  background-clip: padding-box;
}
```

### transparent 用于 background，绘制背景图

transparent 用于 background，通常可以制造出各种各样的背景图像。这里举个简单的例子，利用透明渐变，实现一个切角图形：









