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

CSS的选择符是有权重的，**当不同选择符的样式设置有冲突时，会采用权重高的选择符设置的样式**。权重的规则是这样的：**HTML标签的权重是1，class的权重是10，id的权重是100**，例如p的权重是1，“div em”的权重是1+1=2，“strong.demo”的权重是10+1=11，“\#test.red”的权重是100+10=110。

上面的代码中选择符span的权重是1，“.test”的权重是10，所以“CSS的低权重原则”的color应该是red。

但是如果是如下代码，又会如何呢？

```css
<style type="text/css">
span{font-size: 40px;}
.test{color:red;}
.test2{color: green}
</style>

<span class="test test2">CSS的低权重原则</span>
```

span的标签同时挂了test和test2两个class，他们的权重都是10，那么“CSS的低权重原则”的color又该是哪个呢？**如果 CSS选择符权重相同，那么样式就遵循就近原则，哪个选择符后定义，就采用哪个选择符的样式**。上面的的代码中test2后定义，所以color会采用“.test2”的设置为green。

如果改变“.test”和“.test2”定义的位置，如下

```css
<style type="text/css">
span{font-size: 40px;}
.test2{color: green}
.test{color:red;}
</style>

<span class="test test2">CSS的低权重原则</span>
```

那么 “CSS的低权重原则”的color就为red了。

**需要强调的是就近原则指的是选择符 的先后顺序，而不是挂class名的先后顺序**，&lt;span class="test test2"&gt;CSS的低权重原则&lt;/span&gt;和&lt;span class="test2 test"&gt;CSS的低权重原则&lt;/span&gt;没有区别。

CSS选择符权重是个容易被忽略的问题。但事实上如果不太注意选择符的权重，常会出现意向不到的小麻烦。例如下面

```css
<style type="text/css">
#test{font-size: 14px;}
</style>

<p id="test">CSS的选择符权重很重要</p>
```

现在需要将“很重要”三个字设置为红色，我们应该怎么做呢？ 

方案一，利用子选择器

```css
<style type="text/css">
#test{font-size: 14px;}
#test span{color: red}
</style>

<p id="test">CSS的选择符权重<span>很重要</span></p>
```

方案二，新建class

```css
<style type="text/css">
#test{font-size: 14px;}
.font{color: red}
</style>

<p id="test">CSS的选择符权重<span class="font">很重要</span></p>
```

很多工程师推荐使用方案一，因为使用子选择器可以避免新增class，让HTML代码更简洁，这么考虑是有道理的，但如果这时需求有变化，需要添加新的额文字进来，如下

```css
<style type="text/css">
#test{font-size: 14px;}
#test span{color: red}
</style>

<p id="test">CSS的选择符权重<span>很重要</span>，我们要小心处理</p>
```

要求我们将“小心处理”设置为绿色，我们可以这么做

```css
<style type="text/css">
#test{font-size: 14px;}
#test span{color: red}
.font{color:green}
</style>

<p id="test">CSS的选择符权重<span>很重要</span>，我们要<span class="font">小心处理</span></p>
```

本以为小心处理会被选择符font设置为绿色，但它却被选择符权重更高的“\#test span”设置成了红色，子选择器在无意中影响到了我们新添加的代码。如果想要达到我们的预期，我们需要重写

```css
<style type="text/css">
#test{font-size: 14px;}
#test span{color: red}            /* 选择符权重为100+1=101 */
#test .font{color:green}         /* 选择符权重为100+10=110 */
</style>

<p id="test">CSS的选择符权重<span>很重要</span>，我们要<span class="font">小心处理</span></p>
```

而如果使用方案二呢？

```css
<style type="text/css">
#test{font-size: 14px;}
.test{color: red}            /* 选择符权重为100+1=101 */
.font2{color:green}         /* 选择符权重为100+10=110 */
</style>

<p id="test">CSS的选择符权重<span class="font">很重要</span>，我们要<span class="font2">小心处理</span></p>
```

因为没有使用子选择器，所以我们添加新代码挂上新的class就可以顺利的完成样式的设置了。

使用子选择器，会增加CSS选择符的权重，CSS的选择符权重越高，样式越不易被覆盖，所以除非确定HTML结构非常稳定，一定不会再修改了，否则尽量使用子选择器。为了保证样式容易被覆盖，提高可维护性，CSS选择符权重尽可能低。

少用子选择器，就需要多添加class，在Web标准盛行的初期，很多工程师认为多添加class是不好的，如果能使用子选择器就应该尽量使用，使用大量的class的做法叫做“多class症”。在进过大量实践后，我并不认为多class有太大的坏处，相反，与使用子选择器相比，新添加class反而跟利于维护。

