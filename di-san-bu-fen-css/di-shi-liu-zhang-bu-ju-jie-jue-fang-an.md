# 第十六章 布局解决方案

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











