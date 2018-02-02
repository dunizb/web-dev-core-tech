# 第2节 Ajax与XMLHttpRequest

AJAX 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。

Ajax的三点认识：

1. AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。
2. AJAX 不是新的编程语言，而是一种使用现有标准的新方法。
3. AJAX 是与服务器交换数据并更新部分网页的艺术，在不重新加载整个页面的情况下。

XMLHttpRequest 是 AJAX 的基础。

## 一、XMLHttpRequest 对象

所有现代浏览器均支持 XMLHttpRequest 对象（IE5 和 IE6 使用 ActiveXObject）。XMLHttpRequest 用于在后台与服务器交换数据。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

### 1.1 创建 XMLHttpRequest

创建 XMLHttpRequest 对象的语法：

```js
var htr = new XMLHttpRequest();
```

老版本的 Internet Explorer （IE5 和 IE6）使用 ActiveX 对象

```js
var htr = new ActiveXObject("Microsoft.XMLHTTP");
```

为了应对所有的现代浏览器，包括 IE5 和 IE6，请检查浏览器是否支持 XMLHttpRequest 对象。如果支持，则创建 XMLHttpRequest 对象。如果不支持，则创建 ActiveXObject ：

```js
var xmlhttp;
if (window.XMLHttpRequest) {// code for IE7+, Firefox, Chrome, Opera, Safari
  xmlhttp=new XMLHttpRequest();
} else { // code for IE6, IE5
  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
}
```

### 1.2 向服务器发送请求

如需将请求发送到服务器，我们使用 XMLHttpRequest 对象的 open\(\) 和 send\(\) 方法：

```js
xmlhttp.open("GET","test1.txt",true);
xmlhttp.send();
```

open方法规定请求的类型、URL 以及是否异步处理请求。

* method：请求的类型；GET 或 POST
* url：文件在服务器上的位置
* async：true（异步）或 false（同步）

为了避免被缓存这种情况，请向 URL 添加一个唯一的 ID：

```js
xmlhttp.open("GET","demo_get.asp?t=" + Math.random(),true);
xmlhttp.send();
```

如果需要像 HTML 表单那样 POST 数据，请使用 setRequestHeader\(\) 来添加 HTTP 头。然后在 send\(\) 方法中规定您希望发送的数据：

```js
xmlhttp.open("POST","ajax_test.asp",true);
xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");
xmlhttp.send("fname=Bill&lname=Gates");
```

### 1.3 服务器响应

如需获得来自服务器的响应，请使用 XMLHttpRequest 对象的 responseText 或 responseXML 属性。

如果来自服务器的响应并非 XML，请使用 responseText 属性。

responseText 属性返回字符串形式的响应，因此您可以这样使用：

```js
document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
```

### 1.4 onreadystatechange 事件

当请求被发送到服务器时，我们需要执行一些基于响应的任务。

每当 readyState 改变时，就会触发 onreadystatechange 事件。

readyState 属性存有 XMLHttpRequest 的状态信息。

下面是 XMLHttpRequest 对象的三个重要的属性：

* **onreadystatechange**存储函数（或函数名），每当 readyState 属性改变时，就会调用该函数。
* **readyState**，存有 XMLHttpRequest 的状态。从 0 到 4 发生变化。
  * 0: 请求未初始化
  * 1: 服务器连接已建立
  * 2: 请求已接收
  * 3: 请求处理中
  * 4: 请求已完成，且响应已就绪
* **status**，200: "OK"，404: 未找到页面

在 onreadystatechange 事件中，我们规定当服务器响应已做好被处理的准备时所执行的任务。

当 readyState 等于 4 且状态为 200 时，表示响应已就绪：

```js
xmlhttp.onreadystatechange = function() {
  if (xmlhttp.readyState ==4 && xmlhttp.status == 200) {
     document.getElementById("myDiv").innerHTML = xmlhttp.responseText;
  }  
}
```

## 二、使用Ajax

使用jQuery等开源库的Ajax功能就不说了。自己实现一个Ajax

```js
var Ajax={
    get: function(url, fn) {
        var xhr = new XMLHttpRequest();  // XMLHttpRequest对象用于在后台与服务器交换数据          
        xhr.open('GET', url, true);
        xhr.onreadystatechange = function() {
            if (xhr.readyState == 4 && xhr.status == 200 || xhr.status == 304) { // readyState == 4说明请求已完成
                fn.call(this, xhr.responseText);  //从服务器获得数据
            }
        };
        xhr.send();
    },
    post: function (url, data, fn) {         // datat应为'a=a1&b=b1'这种字符串格式，在jq里如果data为对象会自动将对象转成这种字符串格式
        var xhr = new XMLHttpRequest();
        xhr.open("POST", url, true);
        xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");  // 添加http头，发送信息至服务器时内容编码类型
        xhr.onreadystatechange = function() {
            if (xhr.readyState == 4 && (xhr.status == 200 || xhr.status == 304)) {  // 304未修改
                fn.call(this, xhr.responseText);
            }
        };
        xhr.send(data);
    }
}
```

## 三、AJAX的优缺点

### 3.1 AJAX的优点

**无刷新更新数据**

AJAX最大优点就是能在不刷新整个页面的前提下与服务器通信维护数据。这使得Web应用程序更为迅捷地响应用户交互，并避免了在网络上发送那些没有改变的信息，减少用户等待时间，带来非常好的用户体验。

**异步与服务器通信**

AJAX使用异步方式与服务器通信，不需要打断用户的操作，具有更加迅速的响应能力。优化了Browser和Server之间的沟通，减少不必要的数据传输、时间及降低网络上数据流量。

**前端和后端负载平衡**

AJAX可以把以前一些服务器负担的工作转嫁到客户端，利用客户端闲置的能力来处理，减轻服务器和带宽的负担，节约空间和宽带租用成本。并且减轻服务器的负担，AJAX的原则是“按需取数据”，可以最大程度的减少冗余请求和响应对服务器造成的负担，提升站点性能。

**基于标准被广泛支持**

AJAX基于标准化的并被广泛支持的技术，不需要下载浏览器插件或者小程序，但需要客户允许JavaScript在浏览器上执行。随着Ajax的成熟，一些简化Ajax使用方法的程序库也相继问世。同样，也出现了另一种辅助程序设计的技术，为那些不支持JavaScript的用户提供替代功能。

**界面与应用分离**

Ajax使WEB中的界面与应用分离（也可以说是数据与呈现分离），有利于分工合作、减少非技术人员对页面的修改造成的WEB应用程序错误、提高效率、也更加适用于现在的发布系统。

### 3.2 AJAX的缺点

**AJAX干掉了Back和History功能，即对浏览器机制的破坏。**

在动态更新页面的情况下，用户无法回到前一个页面状态，因为浏览器仅能记忆历史记录中的静态页面。一个被完整读入的页面与一个已经被动态修改过的页面之间的差别非常微妙；用户通常会希望单击后退按钮能够取消他们的前一次操作，但是在Ajax应用程序中，这将无法实现。

后退按钮是一个标准的web站点的重要功能，但是它没法和js进行很好的合作。这是Ajax所带来的一个比较严重的问题，因为用户往往是希望能够通过后退来取消前一次操作的。那么对于这个问题有没有办法？答案是肯定的，用过Gmail的知道，Gmail下面采用的Ajax技术解决了这个问题，在Gmail下面是可以后退的，但是，它也并不能改变Ajax的机制，它只是采用的一个比较笨但是有效的办法，即用户单击后退按钮访问历史记录时，通过创建或使用一个隐藏的IFRAME来重现页面上的变更。（例如，当用户在Google Maps中单击后退时，它在一个隐藏的IFRAME中进行搜索，然后将搜索结果反映到Ajax元素上，以便将应用程序状态恢复到当时的状态。）

但是，虽然说这个问题是可以解决的，但是它所带来的开发成本是非常高的，并与Ajax框架所要求的快速开发是相背离的。这是Ajax所带来的一个非常严重的问题。

一个相关的观点认为，使用动态页面更新使得用户难于将某个特定的状态保存到收藏夹中。该问题的解决方案也已出现，大部分都使用URL片断标识符（通常被称为锚点，即URL中\#后面的部分）来保持跟踪，允许用户回到指定的某个应用程序状态。（许多浏览器允许JavaScript动态更新锚点，这使得Ajax应用程序能够在更新显示内容的同时更新锚点。）这些解决方案也同时解决了许多关于不支持后退按钮的争论。

**AJAX的安全问题**

AJAX技术给用户带来很好的用户体验的同时也对IT企业带来了新的安全威胁，Ajax技术就如同对企业数据建立了一个直接通道。这使得开发者在不经意间会暴露比以前更多的数据和服务器逻辑。Ajax的逻辑可以对客户端的安全扫描技术隐藏起来，允许黑客从远端服务器上建立新的攻击。还有Ajax也难以避免一些已知的安全弱点，诸如跨站点脚步攻击、SQL注入攻击和基于Credentials的安全漏洞等等。

**对搜索引擎支持较弱**

对搜索引擎的支持比较弱。如果使用不当，AJAX会增大网络数据的流量，从而降低整个系统的性能。

**破坏程序的异常处理机制**

至少从目前看来，像Ajax.dll，Ajaxpro.dll这些Ajax框架是会破坏程序的异常机制的。关于这个问题，曾在开发过程中遇到过，但是查了一下网上几乎没有相关的介绍。后来做了一次试验，分别采用Ajax和传统的form提交的模式来删除一条数据……给我们的调试带来了很大的困难。

**违背URL和资源定位的初衷**

例如，我给你一个URL地址，如果采用了Ajax技术，也许你在该URL地址下面看到的和我在这个URL地址下看到的内容是不同的。这个和资源定位的初衷是相背离的。

---

**参考资料**

* [Ajax教程](https://www.sogou.com/link?url=DSOYnZeCC_qVpflvw39qZqv6pKsEBAojsL590GKHR1Gt5pUc3zy0dg..)
* [AJAX工作原理及其优缺点](https://www.cnblogs.com/SanMaoSpace/archive/2013/06/15/3137180.html)



