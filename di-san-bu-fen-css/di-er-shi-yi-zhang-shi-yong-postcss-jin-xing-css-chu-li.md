# 第二十一章 使用PostCSS进行CSS处理

## 一、什么是PostCSS

**PostCSS 是一套利用JS插件实现的用来改变CSS的工具。**这些插件能够支持变量和混合语法，转换未来CSS语法，内联图片，还有更多。它负责把 CSS 代码解析成抽象语法树结构（Abstract Syntax Tree，AST），再交由插件来进行处理。插件基于 CSS 代码的 AST 所能进行的操作是多种多样的，比如可以支持变量和混入（mixin），增加浏览器相关的声明前缀，或是把使用将来的 CSS 规范的样式规则转译（transpile）成当前的 CSS 规范支持的格式。从这个角度来说，PostCSS 的强大之处在于其不断发展的插件体系。目前 PostCSS 已经有 200 多个功能各异的插件。开发人员也可以根据项目的需要，开发出自己的 PostCSS 插件。

PostCSS 的**主要功能**只有两个：

* 第一个就是前面提到的把 CSS 解析成 JavaScript 可以操作的 AST
* 第二个就是调用插件来处理 AST 并得到结果。

因此，不能简单的把 PostCSS 归类成 CSS 预处理或后处理工具。PostCSS 所能执行的任务非常多，同时涵盖了传统意义上的预处理和后处理。PostCSS 是一个全新的工具，给前端开发人员带来了不一样的处理 CSS 的方式。





---

**参考资料**

* [使用 PostCSS 进行 CSS 处理](https://www.ibm.com/developerworks/cn/web/1604-postcss-css/index.html)



