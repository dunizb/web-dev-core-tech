# 第14节 CSS工程化演进

CSS 是 Web 开发中不可或缺的一部分，在前端工程化的不断进步的今天，一方面在 CSS 特性随着规范的升级越来越丰富，另一方面，前端业务复杂性的增加带来的工程愈加庞大，驱使者开发者不断寻找CSS工程化的最佳实践。

## 一、Web开发模块化趋势

不可否认，无论从现代前端框架（React，Vue，Angular，Polymer等），还是从W3C的 Web Components 草案看来，组件化已经是前端开发的主流之选和未来的发展方向，正如在 reddit 上有网友说道 "Facebook.com's codebase includes over 20,000 components"。广义上看，所有页面上都可以被划分成一个个组件，相对于过去以网页作为开发单位，以组件为单位开发有着可复用，可扩展等等一系列有利于项目工程化的优点。

在这种组件化趋势的背景下，CSS 模块化也渐渐有有着各种尝试。

## 二、预处理与后处理

### 预处理

比较流行的CSS预处理器有 Sass, Less 和 Stylus，CSS 预处理器的出现主要针对于 CSS 缺少编程语言的灵活性而生的，是引入了一些编程概念而生的 DSL，开发者编写简介的语义化 DSL 代码，由预处理器编译成 CSS。

以Sass为例，该预处理器支持.scss，.sass 文件类型，其语法支持变量、选择器嵌套、继承（extend）、混合（mixin）和一些逻辑语句，同时还支持跨文件的导入功能，因而使得开发者能够很好的使用编程思想书写样式。

从实际使用情况来看，几个预处理器各有优缺点，社区活跃度上看 Sass &gt; Less &gt; Stylus，在于 Sass 是三个中间最早也是最成熟的，因而有着很多开源积累和很好编程范式，像内置了很多 Sass 的函数的 compass 框架，就是一个很好的例子；Less 相对于 Sass 的优点在于十分的轻量，也完全兼容 CSS，相对于但另一方面可编程能力也不如 Sass, Bootstrap 最新版本的 CSS 预处理器也从 Less 换成 Sass；Stylus 是来源于 node 社区，使用体验上并不输于 Sass 和 Less，无论是编译速度还是语法范式，个人看来，stylus 在某种程度上更加优于其他两个。

### 后处理

后处理器是对原生 CSS 进行处理并最终生成 CSS 的处理器，广义上还是个预处理器，与上面不同的是，它处理的对象是标准 CSS，比较典型的后处理工具有：

* [clean-css](https://link.zhihu.com/?target=https%3A//github.com/jakubpawlowicz/clean-css) -- 压缩 CSS
* AutoPrefixer -- 自动添加 CSS3 属性各浏览器的前缀
* [Rework](https://link.zhihu.com/?target=https%3A//github.com/reworkcss/rework) -- 取代 stylus 的插件化框架
* PostCSS

![](https://pic4.zhimg.com/50/v2-d6ba3e5fe8a4bbc62f04c280f116b421_hd.jpg)

## 三、PostCSS

PostCSS 一开始是从 AutoPrefixer 项目中抽象出来的框架，它本身并不对CSS做具体的业务操作，只是将CSS解析成抽象语法树（AST），样式的操作由之后运行的插件系统完成。正如其本身所言“Tra nsforming styles with JS plugins”

![](https://pic3.zhimg.com/50/v2-60534d79dbcefe26a31aee77c763873f_hd.jpg)

更多时候我们讨论的 PostCSS ，并不止是其解析 CSS 的核心工具，更包括它创建的插件系统，而今 PostCSS 最为吸引开发者的正是其扩展性较强的插件系统和丰富的插件支持。

常用的插件：

* [autoprefixer](https://link.zhihu.com/?target=https%3A//github.com/postcss/autoprefixer) -- 自动补全CSS属性兼容性前缀
* [postcss-cssnext](https://link.zhihu.com/?target=https%3A//github.com/MoOx/postcss-cssnext) -- 使用最新的 CSS 语法
* [postcss-modules](https://link.zhihu.com/?target=https%3A//github.com/outpunk/postcss-modules) -- 组件内自动关联样式至选择器
* [stylelint](https://link.zhihu.com/?target=https%3A//github.com/stylelint/stylelint) -- CSS 语法检查器
* ... ...

当然，如果已有的插件不能满足现有的需求，完全可以[手写一个插件](https://link.zhihu.com/?target=https%3A//github.com/postcss/postcss/blob/master/docs/writing-a-plugin.md):

```js
// 示例 rem 转 px
var custom = function(css, opts){
    css.eachDecl(function(decl){
        decl.value = decl.value.replace(/\d+rem/, function(str){            return 16 * parseFloat(str) + "px";
        });
    });
};
```

当然，PostCSS 的解析并不局限于 CSS，结合它提供的[自定义](https://link.zhihu.com/?target=https%3A//github.com/postcss/postcss/blob/master/docs/syntax.md)语法解析接口，完全可以定义自己的语法。其实类似于 [postcss-scss](https://link.zhihu.com/?target=https%3A//github.com/postcss/postcss-scss) 的插件社区已经有很多了，使用这些插件，可以将原来基于SASS,LESS等预处理器的代码迁移成至 PostCSS。相对于传统的预处理器，PostCSS这种开放平台型的体系，能够不拘束开发者的开发方式，同时也促进了更多对于 CSS 解决方案的探索。

回过头来看，为什么会有对于 CSS 的预处理操作后处理操作 ？其实主要的原因在于前端项目的膨胀使得用传统手工编写并维护 CSS 变得很不堪，根本上由于 CSS 没有缺少编程语言特性，要做到对于 CSS 代码的模块化以及高复用的抽象处理，就必须引入一些编程的思想。相对于 JS 标准推进以及基础设施的完备，CSS 在于编程方面的探索更多来自于社区，也并无统一的事实标准，这也是 CSS 发展落后于JS的原因。

## 四、namespace 约束

一方面我们需要关注技术能够带来代码上的模块化，另一方面我们又要思考如何使用一个良好的风格架构起项目中的 CSS。CSS 除了代码外，另一个很重要的就是 CSS 选择标记，但 CSS 选择器的命名空间是全局的，并没有局部的概念，因而如何利用好这个全局的空间，选择良好的结构风格，也是在开发过程中必须考虑的。

## 五、OOCSS

OOCS （Object-Oriented CSS）即面向对象 CSS，主要有两个核心原则：分离结构和皮肤（separate structure and skin）

皮肤即一些重复的视觉特征，如边框、背景、颜色，分离是为了更多的复用；结构是指元素大小特征，如高度，宽度，边距等等。

```css
.button{padding:10px;box-shadow:rgba(0,0,0,.5)2px2px5px;}
.widget{overflow:auto;box-shadow:rgba(0,0,0,.5)2px2px5px;}
```

根据此原则，我们需要对公用的皮肤进行提取并分离，如下

```css
.button{padding:10px;}
.widget{overflow:auto;}
.skin{box-shadow:rgba(0,0,0,.5)2px2px5px;}
```

分离容器和内容（separate container an content）打破容器内元素对于容器的依赖，元素样式应该独立存在。

举个例子

```css
<div class="container"><h2>xxx</h2></div>
.container h2 {...}
```

上面的h2元素依赖于父元素container, 对应此原则，h2 元素需要使用一个单独的选择器，如下

```css
<div class="container">
  <h2 class="category">xxx</h2>
</div>
.category {...}
```

从实践中看出，使用OOSCC范式，遵守了 DRY 的原则，能够大量减少重复的样式代码，提高代码复用；同时，视觉元素可以意灵活组合各个类名，展示不同的效果，丰富的类名也同时使得元素有着更好的可读性；另一方面，由于容器和内容的分离，CSS 完成了与 HTML 结构解耦。

但同时也会带来一些缺点，抽象复用会使class越来越多，极端情况会产生可能产生很多原子类，这对于那些偏向于“单一来源原则”的开发者来说并不受欢迎。

## 六、SMACSS

SMACSS\(Scalable and Modular Architecture for CSS\) 即模块化架构的可扩展CSS，它主要是将规则分为5类

**基础（Base）**

tag select的样式，定义最基础全局样式，如CSS REST。

```css
html,body,form{margin:0;padding:0;}
a{color:#039;}
a:hover{color:#03C;}
```

**布局（Layout）**

将页面分为各个区域的元素块

```css
.header{}.....footer{}
```

**模块（Module）**

可复用的单元。在模块中需要注意的是选择器一律选择 class selector，避免嵌套子选择器，减少权重，方便外部覆盖。

```css
<divclass="pod pod-constrained">...</div>
<divclass="pod pod-callout">...</div>
 .pod { width: 100%; }
 .pod .pod-callout { width: 200px; }
 .pod .pod-constrained{}
```

**状态（State）**

状态 class 一般通过js动态挂载到元素上，可以根据状态覆盖元素上特定属性。

```css
.tab{background-color:purple;...}
.is-tab-active{background-color:white;}
```

**主题（Theme）**

可选的视觉外观。一般根据需求有颜色，字体，布局等等，实现是将这些样式单独抽出来，根据外部条件（ data 属性，媒体查询等）动态设置。

SMACSS 的主要优点在于按照不同的业务逻辑，将整个 CSS 结构化分更加细致，约束好命名，最小化深度，在编写的时候，使用SMACSS规范能够更好的组织好 CSS 文件结构和 class 命名。

## 七、BEM

BEM 即Block Element Modifier；类名命名规则： Block\_\_Element—Modifier

* Block 所属组件名称
* Element 组件内元素名称
* Modifier 元素或组件修饰符

其核心思想就是组件化。首先一个页面可以按层级依次划分未多个组件，其次就是单独标记这些元素。BEM通过简单的块、元素、修饰符的约束规则确保类名的唯一，同时将类选择器的语义化提升了一个新的高度。

```css
<formclass="form form--theme-xmas form--simple">
    <inputclass="form__input"type="text"/><inputclass="form__submit form__submit--disabled"type="submit"/>
</form>
    .form { } 
    .form--theme-xmas { } 
    .form--simple { } 
    .form__input { } 
    .form__submit { } 
    .form__submit--disabled { }
```

BEM 通过简单的命名规则使得关联类名元素语义性、可读性更强，利于项目管理和多人协作；同时 BEM 方案中并没有嵌套，所有类名最浅深度，并不会出现嵌套过深难以覆盖的情况，易于维护、复用；

另一方面，BEM 强调单一职责原则和单一样式来源原则，意味着传统纯手工 CSS 可能会产生大量重复的代码，但是结合各种 CSS 预处理和 PostCSS 就可以很好的避免问题的产生。另外，虽说股则简单，但在实际使用中，维护 BEM 的命名确实需要一些成本，很多时候命名反而成了一件难事。

## 八、CSS IN JS

css in js的方案一开始是由 facebook 工程师 vjeux在一次分享中提出的，针对于 CSS 在React开发中遇到的各种问题，随后社区涌现了各种各样的方案。

虽然以上模块化的命名约定可以解决风格上的问题，但正如上面而言，也引入一些成本。而对于一些高复用的组件，使用以上高度语义化的方案是个很好的选择，这种成本是必需的，但对于没有复用的业务组件来说，显然这种命名的成本大于收益，特别是在多人协作时候，另外面对现代前端框架的发展，纯靠 CSS 方案并不能很好的解决。

## 九、CSS modlue

CSS module 不同于 vjeux 的完全放弃 CSS，它只是选择了用 js 来管理样式与元素的关联，CSS Module 通过为每个本地定义的类名动态创建一个全局唯一类名，然后注入到UI上，实现编写样式规则的局部模块化。

css-loader 内置支持css-module，只需设置下查询参数，即可在JS中使用CSS文件的导入：

```js
{
  loader:'css-loader',
  query:{
    module:true,
    localInentName:'[name]__[local]--[hash:base64:5]'//}
}
```

在 JS 中导入 css 文件，最终得到的其实是一个经过 CSS 文件进过 parse 后生成的类名映射对象{\[localName\]: \[hashed-Name\], ....}

```js
// Header.jsx
import style from './Header.css'
...
console.log(style) //  {header: 'Header__header--3kSIq_0'}
export default () => <div className={style.header}></div>
```

同时 CSS 文件也会被编译成对应的类名

```css
.Header__header--3kSIq_0- {}   // from Header.css  .header{}
```

从开发体验上看，CSS-Module这种做法让开发者不必在类名的命名上小心翼翼，直接使用随机编译生成唯一标识，让类名的成为局部变量成为了可能。但同时因为也因为随机性，失去了通过此局部类名实现样式覆盖的可能性，覆盖时不得不考虑使用其他选择器（如属性选择器）。对于复用的组件而言，灵活性是必不可少的，这种局部模块化方案并不适合这种高度抽象复用的组件，而对于一次性业务组件确实能够提升开发效率。

同时CSS module 还支持使用 composes 实现CSS代码的组合复用。

```js
/* button.css */
.base{}
.normal {
    composes: base
    ...
}

// button.jsx
import style from './button.css'
export default () => <button className={style.normal}>按按</button> // <button class="button__base--180HZ_0 button__normal--x38Eh_0">按按</button>
```

当然 CSS-module还可以配合各种预处理器一起使用，只需在css-loader之前添加对于的loader，但是在编写的时候要注意CSS-module的语法要在处理器之后合法。实际使用中，对于 CSS 代码的解耦，如果引入了预处理器，代码文件的模块化就不建议使用composes来解决。

## 十、styled-components

styled-components 也是一个完全的 css-in-js 方案，先看语法：

```js
// button
import styled from'styled-compenents'
const Button=styled.button`  
  padding: 10px;
  ${props=>props.primary?'palevioletred':'white'};`
<Button>按钮</Button>
<Buttonprimary>按钮</Button>
```

其编译后也是如同 CSS-in-module 一样，随机混淆生成全局唯一类名，对应生成CSS文件。styled-component 的核心是“样式即组件”，将字符串解析成CSS，并创建对应该样式的 JSX 元素，而有着JS强大的编程能力，完全可以胜任类似于，同时让组件样式与组件逻辑耦合在一起，真正做到组件紧耦合少依赖。当然有些开发不喜欢这种耦合，也完全可以将样式组件和逻辑组件分离，而在JS中分离代码本身也是件易事。

当然，styled-components在真正的应用并不仅仅如此，它完全是一个完备的样式解决方案，有着如扩展、主题、服务端渲染、Babel 插件、React-Native 等等一系列支持，也深受一些开发者欢迎。这里比较有趣是看似奇怪的语法形式，其实是 ES6 中模板字符的特性。

styled-components本身是React社区针对于JSX产生的一种方案，当然在 Vue 中通过vue-styled-components也能使用该功能，但是使用体验一般，无论是在模板里还是在JSX中，使用组件都需在提前声明并注入到组件构建参数中，十分繁琐，而且不同于 React 纯 JSX 的组件渲染语法, Vue 中并不能对既有的组件使用styled语法。

但另一方面，将 CSS 完全写在 JS 中，社区里中也有很多人持反对态度，react-css-modules 的作者就专门发文表示反对styled-component这种完全抛弃 CSS 文件的开发模式。

## 总结

我们在开发的之前，面对各种技术方案，一定要选取并组合出最适合自己项目的方案，是选用传统的CSS预处理器， 还是选用 PostCSS? 是全局手动维护模块，还是完全交个程序随机生成类名？都需要结合业务场景、团队习惯等等因素。另一方面，CSS 本身并无编程特性，但在其工程化技术的发展中缺不乏很多优秀的编程思想，无论是自定义的 DSL 还是基于 JS，这其中带给我们思考的正是“编译思想”。

最后，为你推荐

* [带你入门 CSS Grid 布局](http://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651227178&idx=1&sn=a341dfa7ed575cf184beec29f3a996c7&chksm=bd495dae8a3ed4b8ff7ff2d3ea6a0974f9e46c96cd8a024cec4dca1fdb89b7570e3a12073dd7&scene=21#wechat_redirect)
* [6种组织CSS的方法](http://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651226768&idx=1&sn=eb05d7a5e757c4d06ecba3f478fe71c2&chksm=bd495b148a3ed202af6ee661c1520d57c5ff426dfd33d080296730c2a4fb3d8becb17243d96e&scene=21#wechat_redirect)

---

原文：[https://zhuanlan.zhihu.com/p/32117359](https://zhuanlan.zhihu.com/p/32117359)



