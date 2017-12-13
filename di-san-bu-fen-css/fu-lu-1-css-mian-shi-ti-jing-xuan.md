# 附录1：CSS面试题精选

### 1. CSS隐藏元素的几种方法（至少说出三种）

* **Opacity**:元素本身依然占据它自己的位置并对网页的布局起作用。它也将响应用户交互;
* **Visibility**:与 opacity 唯一不同的是它不会响应任何用户交互。此外，元素在读屏软件中也会被隐藏;
* **Display**:display 设为 none 任何对该元素直接打用户交互操作都不可能生效。此外，读屏软件也不会读到元素的内容。这种方式产生的效果就像元素完全不存在;
* **Position**:不会影响布局，能让元素保持可以操作;
* **Clip-path**:clip-path 属性还没有在 IE 或者 Edge 下被完全支持。如果要在你的 clip-path 中使用外部的 SVG 文件，浏览器支持度还要低;

### 2. rgba\(\)和opacity的透明效果有什么不同？

rgba\(\)和opacity都能实现透明效果，但最大的不同是opacity作用于元素，以及元素内的所有内容的透明度，

而rgba\(\)只作用于元素的颜色或其背景色。（设置rgba透明的元素的子元素不会继承透明效果！）

### 3. CSS中link和@import的区别是什么

Link属于html标签，而@import是CSS中提供的

在页面加载的时候，link会同时被加载，而@import引用的CSS会在页面加载完成后才会加载引用的CSS

@import只有在ie5以上才可以被识别，而link是html标签，不存在浏览器兼容性问题

Link引入样式的权重大于@import的引用（@import是将引用的样式导入到当前的页面中）

### 4. 选择器的优先级顺序为？

!important&gt;行内样式&gt;id 选择器&gt;类选择器&gt;标签选择器&gt;通配符&gt;继承

important 比 内联优先级高

