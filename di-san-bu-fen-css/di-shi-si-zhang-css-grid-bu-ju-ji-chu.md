# 第十四章 CSS Grid布局基础

Grid 布局是网站设计的基础，CSS Grid 是创建网格布局最强大和最简单的工具。

CSS Grid 今年也获得了主流浏览器（Safari，Chrome，Firefox，Edge）的原生支持，所以我相信所有的前端开发人员都必须在不久的将来学习这项技术。

在本文中，我将尽可能快速地介绍CSS网格的基本知识。我会把你不应该关心的一切都忽略掉了，只是为了让你了解最基础的知识。

## 一、你的第一个 Grid 布局

CSS Grid 布局由两个核心组成部分是 **wrapper（父元素）**和 **items（子元素）**。 wrapper 是实际的 grid\(网格\)，items 是 grid\(网格\) 内的内容。

下面是一个 wrapper 元素，内部包含6个 items ：

```html
<div class="wrapper">
  <div>1</div>
  <div>2</div>
  <div>3</div>
  <div>4</div>
  <div>5</div>
  <div>6</div>
</div>
```

要把 wrapper 元素变成一个 grid\(网格\)，只要简单地把其 display 属性设置为 grid 即可：

```css
.wrapper {
    display: grid;
}
```

但是，这还没有做任何事情，因为我们没有定义我们希望的 grid\(网格\) 是怎样的。它会简单地将6个 div 堆叠在一起。

![](http://newimg88.b0.upaiyun.com/newimg88/2017/12/1_vTY7C5FMIp8OLkjrgp-vBg.png)

我已经添加了一些样式，但是这与 CSS Grid 没有任何关系。

## 二、Columns\(列\) 和 rows\(行\)

为了使其成为二维的网格容器，我们需要定义列和行。让我们创建3列和2行。我们将使用 grid-template-row 和 grid-template-column属性。

```css
.wrapper {
    display: grid;
    grid-template-columns: 100px 100px 100px;
    grid-template-rows: 50px 50px;
}
```

正如你所看到的，我们为 grid-template-columns 写入了 3 个值，这样我们就会得到 3 列。 我们想要得到 2 行，因此我们为 grid-template-rows 指定了2个值。

这些值决定了我们希望我们的列有多宽（ 100px ），以及我们希望行数是多高（ 50px ）。 结果如下：

![](http://newimg88.b0.upaiyun.com/newimg88/2017/12/1_fJNIdDiScjhI9CZjdxv3Eg.png)

为了确保你能正确理解这些值与网格外观之间的关系，请看一下这个例子。

```css
.wrapper {
    display: grid;
    grid-template-columns: 200px 50px 100px;
    grid-template-rows: 100px 30px;
}
```

请尝试理解上面的代码，思考一下以上代码会产生怎样的布局。

这是上面代码的布局的结果：

![](http://newimg88.b0.upaiyun.com/newimg88/2017/12/1_M9WbiVEFcseUCW6qeG4lSQ.png)

非常好理解，使用起来也非常简单是不是？下面我们来加大一点难度。













