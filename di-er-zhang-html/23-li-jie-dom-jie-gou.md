# 第3节 理解DOM结构

DOM: Document Object Module, 文档对象模型。我们通过JavaScript操作页面的元素，进行添加、移动、改变或移除的方法和属性, 都是DOM提供的。

### W3C DOM 标准

被分为 3 个不同的部分:

* 核心 DOM - 针对任何结构化文档的标准模型
* XML DOM - 针对 XML 文档的标准模型
* HTML DOM - 针对 HTML 文档的标准模型

### DOM节点

根据 W3C 的 HTML DOM 标准，HTML 文档中的所有内容都是节点：

* 整个文档是一个文档节点
* 每个 HTML 元素是元素节点
* HTML 元素内的文本是文本节点
* 每个 HTML 属性是属性节点
* 注释是注释节点

### HTML DOM 节点树

HTML文本会被解析为DOM树, 树中的所有节点均可通过 JavaScript 进行访问。所有 HTML 元素（节点）均可被修改，也可以创建或删除节点。

![](/assets/html-dom.png)

### 节点的关系

父（parent）、子（child）和同胞（sibling）等术语用于描述这些关系。父节点拥有子节点。同级的子节点被称为同胞（兄弟或姐妹）:

* 在节点树中，顶端节点被称为根（root）
* 每个节点都有父节点、除了根（它没有父节点）
* 一个节点可拥有任意数量的子节点
* 同胞是拥有相同父节点的节点

![](/assets/dom-guanxi.png)

---

原文：[https://leohxj.gitbooks.io/front-end-database/html-and-css-basic/learn-dom-tree.html](https://leohxj.gitbooks.io/front-end-database/html-and-css-basic/learn-dom-tree.html)

