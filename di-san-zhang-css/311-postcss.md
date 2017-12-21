# 第11节 使用PostCSS进行CSS处理

## 一、什么是PostCSS

**PostCSS 是一套利用JS插件实现的用来改变CSS的工具。**这些插件能够支持变量和混合语法，转换未来CSS语法，内联图片，还有更多。它负责把 CSS 代码解析成抽象语法树结构（Abstract Syntax Tree，AST），再交由插件来进行处理。插件基于 CSS 代码的 AST 所能进行的操作是多种多样的，比如可以支持变量和混入（mixin），增加浏览器相关的声明前缀，或是把使用将来的 CSS 规范的样式规则转译（transpile）成当前的 CSS 规范支持的格式。从这个角度来说，PostCSS 的强大之处在于其不断发展的插件体系。目前 PostCSS 已经有 200 多个功能各异的插件。开发人员也可以根据项目的需要，开发出自己的 PostCSS 插件。

PostCSS 的**主要功能**只有两个：

* 第一个就是前面提到的把 CSS 解析成 JavaScript 可以操作的 AST
* 第二个就是调用插件来处理 AST 并得到结果。

因此，不能简单的把 PostCSS 归类成 CSS 预处理或后处理工具。PostCSS 所能执行的任务非常多，同时涵盖了传统意义上的预处理和后处理。PostCSS 是一个全新的工具，给前端开发人员带来了不一样的处理 CSS 的方式。

## 二、使用PostCSS

PostCSS 一般不单独使用，而是与已有的构建工具进行集成。PostCSS 与主流的构建工具，如 Webpack、Grunt 和 Gulp 都可以进行集成。完成集成之后，选择满足功能需求的 PostCSS 插件并进行配置。下面将具体介绍如何在 Webpack、Grunt 和 Gulp 中使用 PostCSS 的 Autoprefixer 插件。

### 2.1 Webpack

Webpack 中使用 postcss-loader 来执行插件处理。在代码清单 1 中，postcss-loader 用来对.css 文件进行处理，并添加在 style-loader 和 css-loader 之后。通过一个额外的 postcss 方法来返回所需要使用的 PostCSS 插件。require\('autoprefixer'\) 的作用是加载 Autoprefixer 插件。

代码清单 1

```js
var path = require('path');

module.exports = {
 context: path.join(__dirname, 'app'),
 entry: './app',
 output: {
   path: path.join(__dirname, 'dist'),
   filename: 'bundle.js'
 },
 module: {
   loaders: [
     {
       test:   /\.css$/,
       loader: "style-loader!css-loader!postcss-loader"
     }
   ]
 },
 postcss: function () {
   return [require('autoprefixer')];
 }
}
```

### 2.2 Gulp

Gulp 中使用 gulp-postcss 来集成 PostCSS。在代码清单 2 中，CSS 文件由 gulp-postcss 处理之后，产生的结果写入到 dist 目录。

清单 2. 在 Gulp 中使用 PostCSS 插件

```js
var gulp = require('gulp');

gulp.task('css', function() {
 var postcss = require('gulp-postcss');
 return gulp.src('app/**/*.css')
   .pipe(postcss([require('autoprefixer')]))
   .pipe(gulp.dest('dist/'));
});
```

## 三、常用插件

### Autoprefixer

Autoprefixer 是一个流行的 PostCSS 插件，其作用是为 CSS 中的属性添加浏览器特定的前缀。开发人员在编写 CSS 时只需要使用 CSS 规范中的标准属性名即可。

### cssnext

cssnext 插件允许开发人员在当前的项目中使用 CSS 将来版本中可能会加入的新特性。cssnext 负责把这些新特性转译成当前浏览器中可以使用的语法。从实现角度来说，cssnext 是一系列与 CSS 将来版本相关的 PostCSS 插件的组合。比如，cssnext 中已经包含了对 Autoprefixer 的使用，因此使用了 cssnext 就不再需要使用 Autoprefixer。

更多内容请参看参考资料1

## 四、开发 PostCSS 插件

虽然 PostCSS 已经有 200 多个插件，但在开发中仍然可能存在已有插件不能满足需求的情况。这个时候可以开发自己的 PostCSS 插件。开发插件是一件很容易的事情。每个插件本质只是一个 JavaScript 方法，用来对由 PostCSS 解析的 CSS AST 进行处理。

每个 PostCSS 插件都是一个 NodeJS 的模块。使用 postcss 的 plugin 方法来定义一个新的插件。插件需要一个名称，一般以“postcss-”作为前缀。插件还需要一个进行初始化的方法。该方法的参数是插件所支持的配置选项，而返回值则是另外一个方法，用来进行实际的处理。该处理方法会接受两个参数，css 代表的是表示 CSS AST 的对象，而 result 代表的是处理结果。

```js
var postcss = require('postcss');

module.exports = postcss.plugin('postcss-checkcolor', function(options) {
 return function(css, result) {
   css.walkDecls('color', function(decl) {
     if (decl.value == 'black') {
       result.warn('No black color.', {decl: decl});
     }
   });
 };
})
```

上面代码给出了一个简单的 PostCSS 插件。该插件使用 css 对象的 walkDecls 方法来遍历所有的“color”属性声明，并对“color”属性值进行检查。如果属性值为 black，就使用 result 对象的 warn 方法添加一个警告消息。

PostCSS 插件一般通过不同的方法来对当前的 CSS 样式规则进行修改。如通过 insertBefore 和 insertAfter 方法来插入新的规则。

---

**参考资料**

1. [使用 PostCSS 进行 CSS 处理](https://www.ibm.com/developerworks/cn/web/1604-postcss-css/index.html)
2. 参考 [PostCSS 官方网站](http://postcss.org/)，了解 PostCSS 的更多内容。
3. 了解 [Autoprefixer](https://github.com/postcss/autoprefixer) 的相关内容。
4. 了解 [cssnext](http://cssnext.io/) 的相关内容。



