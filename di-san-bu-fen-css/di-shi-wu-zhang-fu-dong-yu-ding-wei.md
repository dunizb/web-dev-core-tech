# 第15章 浮动

## 一、浮动的原理

以下是网上大神关于float的优秀文章：

[《CSS浮动\(float,clear\)通俗讲解》](https://link.jianshu.com/?t=http://www.cnblogs.com/iyangyuan/archive/2013/03/27/2983813.html)

[《CSS float浮动的深入研究、详解及拓展\(一\)》](http://www.zhangxinxu.com/wordpress/2010/01/css-float浮动的深入研究、详解及拓展一/)

[《CSS float浮动的深入研究、详解及拓展\(二\)》](http://www.zhangxinxu.com/wordpress/2010/01/css-float浮动的深入研究、详解及拓展二/)

[《那些年我们一起清除过的浮动》](http://www.iyunlu.com/view/css-xhtml/55.html)

## 二、清除浮动

对于一个web前端工程师来说浮动清除是我们的必修课，如何精益求精将代码写到极致，小编觉得我们必须花时间多对比、多揣摩。遍搜网络，清除浮动的方法大致有一以下几种方法：

### 方法1：给父级div定义 高度

```css
<style type=”text/css”>
.div1{background:#000;border:1px solid red; /*解决代码*/height:200px;}
.div2{background:#f00;border:1px solid red;height:100px;margin-top:10px}

.left{float:left;width:20%;height:200px;background:#DDD}
.right{float:right;width:70%;height:80px;background:#DDD}
</style>

<div class=”div1″>
    <div class=”left”>我是左浮动</div>
    <div class=”right”>我是右浮动</div>
</div>
<div class=”div2″>我是div2</div>
```

原理：给父级DIV定义固定高度（height），能解决父级DIV 无法获取高度得问题。

优点：代码简洁

缺点：高度被固定死了，是适合内容固定不变的模块。（不推荐使用）

### 方法2：使用DIV空元素&lt;div class="clear"&gt;&lt;/div&gt; \(.clear{clear:both}\)

```html
<div class=”div1″>
   <div class=”left”>我是左浮动</div>
   <div class=”right”>我是右浮动</div>

   <div class=”clear”></div>
</div>
<div class=”div2″>我是div2</div>
```

原理：添加一对空的DIV标签，利用css的clear:both属性清除浮动，让父级DIV能够获取高度。

优点：浏览器支持好

缺点：多出了很多空的DIV标签，如果页面中浮动模块多的话，就会出现很多的空置DIV了，这样感觉视乎不是太令人满意。（不推荐使用）

### 方法3：使用BR空元素&lt;br class=”clear”/&gt; \(.clear{clear:both}\)

```html
<div class=”div1″>
   <div class=”left”>我是左浮动</div>
   <div class=”right”>我是右浮动</div>

   <br class=”clear” />
</div>
<div class=”div2″>我是div2</div>
```

原理及有优缺点同方法2，可做了解，亦不推荐使用。

### 方法4：让父级div 也一并浮起来

这样做可以初步解决当前的浮动问题。但是也让父级浮动起来了，又会产生新的浮动问题。 不推荐使用

### 方法5：父级div定义 display:table

原理：将div属性强制变成表格

优点：不解

缺点：会产生新的未知问题。（不推荐使用）

### 方法6：父元素设置 overflow：hidden；

```html
<style type=”text/css”>
.div1{background:#000;border:1px solid red; /*解决代码*/overflow:hidden;zoom:1}
.div2{background:#f00;border:1px solid red;height:100px;margin-top:10px}

.left{float:left;width:20%;height:200px;background:#DDD}
.right{float:right;width:70%;height:80px;background:#DDD}
</style>

<div class=”div1″>
    <div class=”left”>我是左浮动</div>
    <div class=”right”>我是右浮动</div>
</div>
<div class=”div2″>我是div2</div>
```

原理：通过设置父元素overflow值设置为hidden；在IE6中还需要触发 hasLayout（zoom：1）

优点：代码简介，不存在结构和语义化问题

缺点：无法显示需要溢出的元素（亦不太推荐使用）

### 方法7：父元素设置 overflow：auto；

原理：原理同方法6，在IE6中还需要触发 hasLayout（zoom：1）

优点：代码简介，不存在结构和语义化问题

缺点：firefox早期版本会无故产生focus，多个嵌套后，firefox某些情况会造成内容全选；IE中 mouseover 造成宽度改变时会出现最外层模块有滚动条等。

### 方法8：父级div定义 伪类:after 和 zoom

```css
<style type=”text/css”>
.div1{background:#000;border:1px solid red; /*解决代码*/overflow:hidden;zoom:1}
.div2{background:#f00;border:1px solid red;height:100px;margin-top:10px}

.left{float:left;width:20%;height:200px;background:#DDD}
.right{float:right;width:70%;height:80px;background:#DDD}
.clearfix:after { 
   content: “.”;
   display: block;
   height: 0;
   clear: both;
   visibility: hidden; 
}
.clearfix {zoom:1;}
</style>

<div class=”div1 clearfix”>
   <div class=”left”>我是左浮动</div>
   <div class=”right”>我是右浮动</div>
</div>
<div class=”div2″>我是div2</div>
```

原理：IE8以上和非IE浏览器才支持:after，原理和方法2有点类似，zoom\(IE转有属性\)可解决ie6,ie7浮动问题

优点：结构和语义化完全正确,代码量也适中，可重复利用率（建议定义公共类）

缺点：代码不是非常简洁（极力推荐使用）

**本方法进益求精写法**

相对于空标签闭合浮动的方法代码似乎还是有些冗余，通过查询发现Unicode字符里有一个“零宽度空格”，也就是U+200B，这个字符本身是不可见的，所以我们完全可以省略掉 visibility:hidden了

```css
.clearfix:after {
    content:”\200B”; 
    display:block; 
    height:0; 
    clear:both; 
}
.clearfix { *zoom:1; } 照顾IE6，IE7就可以了
```

---

参考文章：[http://www.wfuns.com/?p=21](http://www.wfuns.com/?p=21)

