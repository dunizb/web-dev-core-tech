# 第16章 布局解决方案

了解 CSS 中属性的值及其特性， 透彻分析问题和需求才可以选择和设计最适合的布

## 一、居中布局

### 1.1 水平居中

![](https://li-xinyang.gitbooks.io/frontend-notebook/content/img/L/layout-center-horizontal.png)子元素于父元素水平居中且其（子元素与父元素）宽度均可变。

**inline-block + text-align**

```css
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .child {
    display: inline-block;
  }
  .parent {
    text-align: center;
  }
</style>
```

优点：兼容性佳（甚至可以兼容 IE 6 和 IE 7）

**table + margin**

```css
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .child {
    display: table;
    margin: 0 auto;
  }
</style>
```

> NOTE: display: table 在表现上类似 block 元素，但是宽度为内容宽。

优点：无需设置父元素样式 （支持 IE 8 及其以上版本）

> NOTE：兼容 IE 8 一下版本需要调整为 &lt;table&gt; 的结果

**absolute + transform**

```css
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    position: relative;
  }
  .child {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
  }
</style>
```

优点：绝对定位脱离文档流，不会对后续元素的布局造成影响。

缺点：transform 为 CSS3 属性，有兼容性问题

**flex + justify-content**

```css
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    display: flex;
    justify-content: center;
  }

  /* 或者下面的方法，可以达到一样的效果 */

  .parent {
    display: flex;
  }
  .child {
    margin: 0 auto;
  }
</style>
```

优点：只需设置父节点属性，无需设置子元素

缺点：有兼容性问题

### 1.2 垂直居中

![](https://li-xinyang.gitbooks.io/frontend-notebook/content/img/L/layout-center-vertical.png)

子元素于父元素垂直居中且其（子元素与父元素）高度均可变。

**table-cell + vertical-align**

```css
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    display: table-cell;
    vertical-align: middle;
  }
</style>
```

优点：兼容性好（支持 IE 8，以下版本需要调整页面结构至 table）

**absolute + transform**

```css
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    position: relative;
  }
  .child {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
  }
</style>
```

优点：绝对定位脱离文档流，不会对后续元素的布局造成影响。但如果绝对定位元素是唯一的元素则父元素也会失去高度。

缺点：transform 为 CSS3 属性，有兼容性问题

**flex + align-items**

```css
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    display: flex;
    align-items: center;
  }
</style>
```

优点：只需设置父节点属性，无需设置子元素

缺点：有兼容性问题

### 1.3 水平与垂直居中

![](https://li-xinyang.gitbooks.io/frontend-notebook/content/img/L/layout-center-center.png)

子元素于父元素垂直及水平居中且其（子元素与父元素）高度宽度均可变。

**inline-block + text-align + table-cell + vertical-align**

```css
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    text-align: center;
    display: table-cell;
    vertical-align: middle;
  }
  .child {
    display: inline-block;
  }
</style>
```

优点：兼容性好

**absolute + transform**

```css
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    position: relative;
  }
  .child {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
  }
</style>
```

优点：绝对定位脱离文档流，不会对后续元素的布局造成影响。但如果绝对定位元素是唯一的元素则父元素也会失去高度。

缺点：transform 为 CSS3 属性，有兼容性问题

**flex + justify-content + align-items**

```css
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>
```

优点：只需设置父节点属性，无需设置子元素

缺点：有兼容性问题

## 二、多列布局

多列布局在网页中非常常见（例如两列布局），多列布局可以是两列定宽，一列自适应， 或者多列不定宽一列自适应还有等分布局等。

### 2.1 一列定宽，一列自适应 ![](https://li-xinyang.gitbooks.io/frontend-notebook/content/img/L/layout-multicolumn-0.jpg)

**float + margin**

```css
<div class="parent">
  <div class="left">
    <p>left</p>
  </div>
  <div class="right">
    <p>right</p>
    <p>right</p>
  </div>
</div>

<style>
  .left {
    float: left;
    width: 100px;
  }
  .right {
    margin-left: 100px
    /*间距可再加入 margin-left */
  }
</style>
```

NOTE：IE 6 中会有3像素的 BUG，解决方法可以在 .left 加入 margin-left:-3px。

**float + margin + \(fix\) 改造版**

```css
<div class="parent">
  <div class="left">
    <p>left</p>
  </div>
  <div class="right-fix">
    <div class="right">
      <p>right</p>
      <p>right</p>
    </div>
  </div>
</div>

<style>
  .left {
    float: left;
    width: 100px;
  }
  .right-fix {
    float: right;
    width: 100%;
    margin-left: -100px;
  }
  .right {
    margin-left: 100px
    /*间距可再加入 margin-left */
  }
</style>
```

NOTE：此方法不会存在 IE 6 中3像素的 BUG，但 .left 不可选择， 需要设置 .left {position: relative} 来提高层级。 此方法可以适用于多版本浏览器（包括 IE6）。缺点是多余的 HTML 文本结构。

**float + overflow**

```css
<div class="parent">
  <div class="left">
    <p>left</p>
  </div>
  <div class="right">
    <p>right</p>
    <p>right</p>
  </div>
</div>

<style>
  .left {
    float: left;
    width: 100px;
  }
  .right {
    overflow: hidden;
  }
</style>
```

设置 overflow: hidden 会触发 BFC 模式（Block Formatting Context）块级格式化文本。 BFC 中的内容与外界的元素是隔离的。

优点：样式简单

缺点：不支持IE6

**table**

```css
<div class="parent">
  <div class="left">
    <p>left</p>
  </div>
  <div class="right">
    <p>right</p>
    <p>right</p>
  </div>
</div>

<style>
  .parent {
    display: table;
    width: 100%;
    table-layout: fixed;
  }
  .left {
    display: table-cell;
    width: 100px;
  }
  .right {
    display: table-cell;
    /*宽度为剩余宽度*/
  }
</style>
```

table 的显示特性为每列的单元格宽度合一定等与表格宽度。 table-layout: fixed; 可加速渲染，也是设定布局优先。

NOTE：table-cell 中不可以设置 margin 但是可以通过 padding 来设置间距。

**flex**

```css
<div class="parent">
  <div class="left">
    <p>left</p>
  </div>
  <div class="right">
    <p>right</p>
    <p>right</p>
  </div>
</div>

<style>
  .parent {
    display: flex;
  }
  .left {
    width: 100px;
    margin-left: 20px;
  }
  .right {
    flex: 1;
    /*等价于*/
    /*flex: 1 1 0;*/
  }
</style>
```

**缺点**

* 低版本浏览器兼容问题
* 性能问题，只适合小范围布局。

### 2.2 两列定宽，一列自适应

![](https://li-xinyang.gitbooks.io/frontend-notebook/content/img/L/layout-multicolumn-1.png)

```css
<div class="parent">
  <div class="left">
    <p>left</p>
  </div>
  <div class="center">
    <p>center<p>
  </div>
  <div class="right">
    <p>right</p>
    <p>right</p>
  </div>
</div>

<style>
  .left, .center {
    float: left;
    width: 100px;
    margin-right: 20px;
  }
  .right {
    overflow: hidden;
    /*等价于*/
    /*flex: 1 1 0;*/
  }
</style>
```

多列定宽的实现可以更具单列定宽的例子进行修改与实现。

### 2.3 一列不定宽加一列自适应

![](https://li-xinyang.gitbooks.io/frontend-notebook/content/img/L/layout-multicolumn-2.png)不定宽的宽度为内容决定，下面为可以实现此效果的方法：

* float + overflow，此方法在 IE6 中有兼容性问题
* table，此方法在 IE6 中有兼容性问题
* flex，此方法在 IE9及其以下版本中有兼容性问题

### 1.6 多列等分布局

![](https://li-xinyang.gitbooks.io/frontend-notebook/content/img/L/layout-multicolumn-4.png)

每一列的宽度和间距均相等，下面为多列等分布局的布局特定。

---

跟多布局解决方案：[https://li-xinyang.gitbooks.io/frontend-notebook/content/chapter4/02\_layout.html](https://li-xinyang.gitbooks.io/frontend-notebook/content/chapter4/02_layout.html)

