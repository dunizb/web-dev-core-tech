# 附录2：在HTML5中被废弃的元素和属性

## 废弃的元素（Element）

下面的元素被废弃的原因是用CSS处理可以更好地替代他们：

* basefont
* big
* center
* font
* strike
* tt

下面的元素被废弃的原因是他们的使用破坏了可使用性和可访问性：

* frame
* frameset
* noframes

下面的元素被废弃的原因是不经常使用他们，也会引起混乱，而且其它元素也可以很好地实现他们的功能：

* acronym被废弃是因为它经常使页面错乱，可以使用abbr代替
* applet被废弃是因为可以使用object代替
* isindex被废弃是因为使用表单控件代替
* dir被废弃是因为使用ul代替

最后，noscript元素只能在HTML里使用，而不能在XML里使用。

## 废弃的属性（Attribute）

HTML4里的一些属性不会再被允许在HTML5里使用了，规范里详细说明了如何处理现有的文档，并且以后新文档不能再使用这些属性，因为他们会标记成不合法的属性。

HTML5的规范里有对这些属性的代替方案，[点击访问](http://www.whatwg.org/specs/web-apps/current-work/multipage/obsolete.html#non-conforming-features)。

| 对应元素 | 属性名称 |
| :--- | :--- |
| link,a | rev, charset |
| a | shape, coords |
| img, iframe | longdesc |
| link | target |
| area | nohref |
| head | profile |
| html | version |
| img | name |
| meta | scheme |
| object | archive, classid, codebase, codetype, declare, standby |
| param | valuetype, type |
| td,th | axis, abbr |
| td | scope |
| table | summary |

另外， 在HTML5里，以下元素的视觉属性也将被废弃，因为这些功能可用CSS来实现：

| ID | 对应元素 | 属性名称 |
| :--- | :--- | :--- |
| 01 | caption, iframe, img, input, object, legend, table, hr, div, h1, h2, h3, h4, h5, h6, p, col, colgroup, tbody, td, tfoot, th, thead, tr | align |
| 02 | body | alink, link, text, vlink, background |
| 03 | table, tr, td, th, body | bgcolor |
| 04 | object | border |
| 05 | table | cellpadding, cellspacing, frame |
| 06 | col, colgroup, tbody, td, tfoot, th, thead, tr | char, charoff |
| 07 | br | clear |
| 08 | dl, menu, ol, ul | compact |
| 09 | iframe | frameborder, marginheight, marginwidth, scrolling |
| 10 | td, th | height, nowrap |
| 11 | img, object | hspace, vspace |
| 12 | hr | noshade, size |
| 13 | hr, table, td, th, col, colgroup, pre | width |
| 14 | col, colgroup, tbody, td, tfoot, th, thead, tr | valign |
| 15 | li, ol, ul | type |

---

原文：[http://www.cnblogs.com/TomXu/archive/2011/12/17/2269168.html](http://www.cnblogs.com/TomXu/archive/2011/12/17/2269168.html)

