# 第十五章 浮动

## 一、浮动的原理

以下是网上大神关于float的优秀文章：

[《CSS浮动\(float,clear\)通俗讲解》](https://link.jianshu.com/?t=http://www.cnblogs.com/iyangyuan/archive/2013/03/27/2983813.html)

[《CSS float浮动的深入研究、详解及拓展\(一\)》](http://www.zhangxinxu.com/wordpress/2010/01/css-float浮动的深入研究、详解及拓展一/)

[《CSS float浮动的深入研究、详解及拓展\(二\)》](http://www.zhangxinxu.com/wordpress/2010/01/css-float浮动的深入研究、详解及拓展二/)

[《那些年我们一起清除过的浮动》](http://www.iyunlu.com/view/css-xhtml/55.html)

## 二、清楚浮动

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



