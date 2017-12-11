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







