# 第11节 script标签的defer和async属性

## 一、Script标签的属性

向HTML页面中插入JavaScript的主要方法，就是使用&lt;script&gt;元素。这个元素由Netscape Navigation2中首先实现。后来，这个元素被加入到正式的HTML规范中。HTML4为&lt;script&gt;元素定义了如下6个属性。

* **async**：可选。表示应该立即下载脚本，但不妨碍页面中的其它操作，比如下载其它资源或等待加载其它脚本。只对外部脚本文件有效。
* **charset**：可选。表示通过src属性指定的代码的字符集。由于大多数浏览器会忽略它的值，因此这个属性很少有人用。
* **defer**：可选。表示脚本可以延迟到文档被完全解释和显示之后再执行。只对外部脚本文件有效。IE7及更早的版本对嵌入脚本也支持这个属性。
* ~~**language**：已废弃~~。原来用于表示编写代码使用的脚本语言（如JavaScript、VBScript）。大多数浏览器会忽略掉这个属性，因此也没必要再用了。
* **src**：可选。表示包含要执行代码的外部文件。
* **type**：可选。可以看成是language的替代属性；表示编写代码使用的脚本语言的内容类型（也称为MIME类型）。虽然text/javascript和test/ecmascript都已经不被推荐使用，但人们一直以来使用的都还是text/javascript。实际上，服务器再传送JavaScript文件时使用的MIME类型通常是application/x-javascript，但再type中设置这个值却可能导致脚本被忽略。另外，在非IE浏览器中还可以使用一下值：application/javascript和application/ecmascript。考虑到约定成俗和最大限度的浏览器兼容性，目前type属性的值依旧还是text/javascript。不过，**这个属性并不是必须的，如果没有这个属性，则其默认值仍为text/javascript。**

## 二、defer延迟脚本

HTML4为script元素定义了defer属性。这个属性的用途是表明脚本再执行时不会影响页面的构造，也就是说：脚本会被延迟到整个页面都解析完成再运行。因此，在script元素中设置defer属性，**相当于告诉浏览器立即下载，但延迟执行**。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Document</title>
    <script type="text/javascript" defer="defer" src="example1.js"></script>
    <script type="text/javascript" defer="defer" src="example2.js"></script>
</head>
<body>
    <!-- 这里放内容 -->
</body>
</html>
```

在这个例子中虽然我们吧script标签放在了header中，但其中包含的脚本将延迟到浏览器遇到标签后再执行。HTML5规范要求脚本按照它们出现的先后顺序执行，因此第一个延迟脚本会先于第二个延迟脚本执行，而这两个脚本会先于DOMContentLoader事件执行。在现实中，延迟脚本并不一定会按照顺序执行，也不一定会在DOMContentLoader事件触发前执行，因此**最好只包含一个延迟脚本**。

前面提到过，defer属性只适用于外部脚本文件。这一点在HTML5中已经明确规定，因此支持HTML5的实现会忽略给嵌入的脚本设置的defer属性。

## 三、async异步脚本

HTML5为&lt;script&gt;元素定义了async属性。这个属性与defer属性类似，都是用于改变处理脚本的行为。同样与defer类似，async只适用于外部脚本文件，并告诉浏览器立即下载文件，但与defer不同的是，标记为async的脚本并不保证按照指定它们先后顺序执行。

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Document</title>
    <script type="text/javascript" async="async" src="example1.js"></script>
    <script type="text/javascript" async="async" src="example2.js"></script>
</head>
<body>
    <!-- 这里放内容 -->
</body>
</html>
```

在以上代码中，第二个脚本文件可能会在第一个脚本文件之前执行。因此，确保两者之间互不依赖非常重要。指定async属性的目的是不让页面等待两个脚本下载和执行，从而异步加载页面中其它的内容。为此，建议异步脚本不要在加载期间修改DOM。

异步脚本一定会在页面的load事件前执行，但可能会在DOMContentLoader时间触发之前或之后执行。

## 总结

defer是在HTML解析完之后才会执行，如果是多个，按照加载的顺序依次执行

async是在加载完成后立即执行，如果是多个，执行顺序和加载顺序无关

