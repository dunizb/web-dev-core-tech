# 第二十章 CSS预处理器之Less

## 一、CSS的短板

作为前端学习者的我们 或多或少都要学些 CSS ，它作为前端开发的三大基石之一，时刻引领着 Web 的发展潮向。 而 CSS 作为一门标记性语言，可能 给初学者第一印象 就是简单易懂，毫无逻辑，不像编程该有的样子。在语法更新时，每当新属性提出，浏览器的兼容又会马上变成绊脚石，可以说 CSS 短板不容忽视。

问题的诞生往往伴随着技术的兴起， 在 Web 发展的这几年， 为了让 CSS 富有逻辑性，短板不那么严重，涌现出了 一些神奇的预处理语言。 它们让 CSS 彻底变成一门 可以使用 变量 、循环 、继承 、自定义方法等多种特性的标记语言，逻辑性得以大大增强。

## 二、预处理语言的诞生

其中 就我所知的有三门语言：Sass、Less 、Stylus 。

Sass 诞生于 2007 年，Ruby 编写，其语法功能都十分全面，可以说 它完全把 CSS 变成了一门编程语言。另外 在国内外都很受欢迎，并且它的项目团队很是强大 ，是一款十分优秀的预处理语言。

Less 诞生于 2009 年，受Sass的影响创建的一个开源项目。 它扩充了 CSS 语言，增加了诸如变量、混合（mixin）、函数等功能，让 CSS 更易维护、方便制作主题、扩充（引用于官网）。

Stylus 诞生于 2010 年，来自 Node.js 社区，语法功能也和 Sass 不相伯仲，是一门十分独特的创新型语言。

## 三、使用 Less 的前奏

在网上讨论看来，Sass 与 Stylus 相比于 Less 功能更为丰富，但对于学习成本以及适应时间 ，Less 稍胜一筹，这也是我选择 Less 的原因。

Less 没有去掉任何 CSS 的功能，而是在现有的语法上，增添了许多额外的功能特性，所以学习 Less 是一件非常舒服的事情。

如果你之前没有接触过预处理语言，纠结应该学哪一个，不如先看看 下面 Less 的介绍，我相信你会爱上它的。

使用 Less 有两种方式：

**在页面中引入 Less.js**

可在官网下载，或使用CDN。

```js
<script src="//cdnjs.cloudflare.com/ajax/libs/less.js/2.7.2/less.min.js"></script>
```

需要注意的是，link 标签一定要在 Less.js 之前引入，并且 link 标签的 rel 属性要设置为stylesheet/less。

```js
<link rel="stylesheet/less" href="style.less">
<script src="less.min.js"></script>
```

**在命令行 使用npm安装**

```bash
npm install -g less
```

具体使用命令

```bash
$ lessc styles.less > styles.css
```

假如还有问题，[官网](http://less.bootcss.com/)已经有了明确的步骤。

如果你也是 Webpack 的使用者，还需要配合 less-loader 进行处理，具体可见我的这篇文章：[Webpack飞行手册](https://tomotoes.com/posts/4d6f8cc5/)，里面详细说明了 less 的处理方式。

如果你在本地环境，可以使用第一种方式，非常简单；但在生产环境中，性能非常重要，最好使用第二种方式。

## 四、Less语法详解

### 4.1 注释

可以使用CSS中的注释 （/\*我会被编译\*/）

也可以使用//我不会被编译注释。这个注释在编译时自动过滤掉

### 4.2 变量

Less中声明变量一定要用@开头，比如：@变量名:值

```css
//1.声明变量
@test_width:300px;
.box{
    //2.使用变量
    width: @test_width;
    height: @test_width;
    border: 1px solid white;
    background-color: yellow;
}
```

它编译后的css文件中@test\_width就直接替换为300px;了。

### 4.3 混合（Mixin）

混合（mixin）变量，例如：

```css
.border{border:solid 10px red;}
```

带参数的混合

```css
.border-radius(@radius){css代码}
```

可设定默认值

```css
border-radius(@radius:5px){css代码}
```

Less：

```css
@test_width:300px;
.box{
    //2.使用变量
    width: @test_width;
    height: @test_width;
    background-color: yellow;

    .border;
}

//混合
.border{
    border: 5px solid pink;
}

.box2{
    .box;
    margin-left: 100px;
}
```

带参数的混合，Less：

```css
//混合 - 可带参数的
.border_02(@border_width){
    border: solid yellow @border_width;
}

.test_hunhe{
    .border_02(30px);
}
```

带默认值参数的混合，Less：

```css
.border_03(@border_width:10px){
    border: solid green @border_width;
}
.text_hunhe_03{
    .border_03();
}
```

在引用.border\_03的时候没有传递至，那么默认的值就是10px。

### 4.4 匹配模式

* 相当于JS中的if，但不完全是
* 满足条件后才能匹配

我们来看一个画三角的例子，如果你知道怎么画更好： 

```css
<div class="sanjiao"></div>

.sanjiao{
    width: 0;
    height: 0;
    overflow: hidden;
    border-width: 10px;
    border-color: transparent transparent red transparent;
    border-style: dashed dashed solid dashed;
}
```

这是画一个向上的三角，如果我们要改变三角的朝向，我们得改变border-color，我们得写几遍大部分都一样的代码，而用来模式匹配之后： 

```css
//模式匹配
.triangle(top,@width:5px,@color:#ccc){
    border-width: @width;
    border-color: transparent transparent @color transparent;
    border-style: dashed dashed solid dashed;
}
.triangle(bottom,@width:5px,@color:#ccc){
    border-width: @width;
    border-color: @color transparent transparent transparent;
    border-style: dashed dashed solid dashed;
}
.triangle(left,@width:5px,@color:#ccc){
    border-width: @width;
    border-color: transparent @color transparent transparent;
    border-style: dashed solid dashed dashed;
}
.triangle(right,@width:5px,@color:#ccc){
    border-width: @width;
    border-color: transparent transparent transparent @color;
    border-style: dashed dashed dashed solid;
}
```

然后我们需要什么方向调用的时候就传什么方向：

```css
.sanjiao{
    width: 0;
    height: 0;
    overflow: hidden;
    .triangle(right,100px);
}
```

发现了没？就是一个“@\_”参数！无论你选择top是right方向，带@\_参数的triangle都会被带上，调用的时候就没必要再写width，height等。

