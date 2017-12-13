# 第23章 BEM命名规范

## 一、什么是BEM

BEM的意思就是块（block）、元素（element）、修饰符（modifier）,是由Yandex团队提出的一种前端命名方法论。这种巧妙的命名方法让你的CSS类对其他开发者来说更加透明而且更有意义。BEM命名约定更加严格，而且包含更多的信息，它们用于一个团队开发一个耗时的大项目。

重要的是要注意，我使用的基于BEM的命名方式是经过[Nicolas Gallagher修改过的](http://nicolasgallagher.com/about-html-semantics-front-end-architecture)。这篇文章中介绍的这种命名技术并不是原始的BEM，但却是一个我更喜欢的改进版。无论实际使用了什么样的符号，它们其实都是基于同样的BEM原则。

## 二、命名约定的模式

命名约定的模式如下：

```
.block{}
.block__element{}
.block--modifier{}
```

* .block 代表了更高级别的抽象或组件。
* .block\_\_element 代表.block的后代，用于形成一个完整的.block的整体。
* .block--modifier代表.block的不同状态或不同版本。

之所以使用两个连字符和下划线而不是一个，是为了让你自己的块可以用单个连字符来界定，如：

```
.site-search{} /* 块 */
.site-search__field{} /* 元素 */
.site-search--full{} /* 修饰符 */
```

**BEM的关键是光凭名字就可以告诉其他开发者某个标记是用来干什么的。**通过浏览HTML代码中的class属性，你就能够明白模块之间是如何关联的：有一些仅仅是组件，有一些则是这些组件的子孙或者是元素,还有一些是组件的其他形态或者是修饰符。我们用一个类比/模型来思考一下下面的这些元素是怎么关联的：

```
.person{}
.person__hand{}
.person--female{}
.person--female__hand{}
.person__hand--left{}
```

顶级块是‘person’，它拥有一些元素，如‘hand’。一个人也会有其他形态，比如女性，这种形态进而也会拥有它自己的元素。下面我们把他们写成‘常规’CSS

```
.person{}
.hand{}
.female{}
.female-hand{}
.left-hand{}
```

这些‘常规’CSS都是有意义的，但是它们之间却有些脱节。就拿.female来说，是指女性人类还是某种雌性的动物？还有.hand，是在说一只钟表的指针（译注：英文中hand有指针的意思）？还是一只正在玩纸牌的手？使用BEM我们可以获得更多的描述和更加清晰的结构，单单通过我们代码中的命名就能知道元素之间的关联。BEM真是强大。

## 三、举例1

再来看一个之前用‘常规’方式命名的.site-search的例子：

```html
<form class="site-search  full">
  <input type="text" class="field">
  <input type="Submit" value ="Search" class="button">
</form>	
```

这些CSS类名真是太不精确了，并不能告诉我们足够的信息。尽管我们可以用它们来完成工作，但它们确实非常含糊不清。用BEM记号法就会是下面这个样子：

```html
<form class="site-search  site-search--full">
  <input type="text" class="site-search__field">
  <input type="Submit" value ="Search" class="site-search__button">
</form>	
```

我们能清晰地看到有个叫.site-search的块，他内部是一个叫.site-search\_\_field的元素。并且.site-search还有另外一种形态叫.site-search--full。

## 四、举例2

我们再来举个例子……

如果你熟悉OOCSS（面向对象CSS），那么你对media对象一定也不陌生。用BEM的方式，media对象就会是下面这个样子：

```
.media{}
.media__img{}
.media__img--rev{}
.media__body{}	
```

从这种CSS的写法上我们就已经知道.media\_\_img 和.media\_\_body一定是位于.media内部的，而且.media\_\_img--rev是.media\_\_img的另一种形态。仅仅通过CSS选择器的名字我们就能获取到以上全部信息。

BEM的另外一个好处是针对下面这种情况：

```html
<div class="media">
  <img src="logo.png" alt="Foo Corp logo" class="img-rev">
  <div class="body">
    <h3 class="alpha">Welcome to Foo Corp</h3>
    <p class="lede">Foo Corp is the best, seriously!</p>
  </div>
</div>	
```

光从上面的代码来看，我们根本不明白.media和.alpha两个class彼此之间是如何相互关联的？同样我们也无从知晓.body和.lede之间，或者.img-rev 和.media之间各是什么关系？从这段HTML（除非你对那个media对象非常了解）中我们也不知道这个组件是由什么组成的和它还有什么其他的形态。如果我们用BEM方式重写这段代码：

```html
<div class="media">
  <img src="logo.png" alt="Foo Corp logo" class="media__img--rev">
  <div class="media__body">
    <h3 class="alpha">Welcome to Foo Corp</h3>
    <p class="lede">Foo Corp is the best, seriously!</p>
  </div>
</div>	
```

我们立马就能明白.media是一个块，.media\_\_img--rev是一个加了修饰符的.media\_\_img的变体，它是属于.media的元素。而.media\_\_body是一个尚未被改变过的也是属于.media的元素。所有以上这些信息都通过它们的class名称就能明白，由此看来BEM确实非常实用。

