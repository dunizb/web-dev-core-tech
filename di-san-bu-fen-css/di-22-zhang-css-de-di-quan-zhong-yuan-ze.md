# 第22章 CSS的低权重原则

CSS设置的样式是可以层叠的

```css
<style type="text/css">
    span{font-size: 40px;}
    .test{color:red;}
</style>

<span class="test">CSS的低权重原则</span>
```

“CSS的低权重原则”既可以的到“font-size:40px”的样式，又可以得到“color： red”的样式。如果两个不同选择符设置的样式有冲突，又会如何？如下

```css
<style type="text/css">
    span{font-size: 40px;color:green}
    .test{color:red;}
</style>

<span class="test">CSS的低权重原则</span>
```

“CSS的低权重原则”颜色会是什么呢？是对“span”设置的“color:green”呢，还是第“.test”设置的“color:red”呢？这就涉及CSS选择符的权重问题了。

