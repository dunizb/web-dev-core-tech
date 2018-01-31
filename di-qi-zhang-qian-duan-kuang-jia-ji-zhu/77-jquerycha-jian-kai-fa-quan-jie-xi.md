# 第7节 jQuery插件开发全解析

有些WEB开发者，会引用一个jQuery类库，然后在网页上写一写 $\("\#"\)，$\("."\)，写了几年就对别人说非常熟悉JQuery。我曾经也是这样的人，直到有一次公司里的技术交流，我才改变了自己对自己的看法。

不定义JQuery插件，不要说会JQuery！

## 一、jQuery插件知识普及

### 1.1 知识1

用JQuery写插件时，最核心的方法有如下两个：

* $.extend\(object\) 可以理解为jQuery 添加一个静态方法。也就是**类级别的插件**。
* $.fn.extend\(object\) 可以理解为jQuery实例添加一个方法。就是**对象级别的插件**。

基本的定义与调用：

```js
/* $.extend 定义与调用 */ 
$.extend({ 
    fun1: function () { 
        alert("执行方法一"); 
    } 
});  
$.fun1();

/*  $.fn.extend 定义与调用 */ 
$.fn.extend({ 
    fun2: function () { 
        alert("执行方法2"); 
    } 
});  
$(this).fun2();  
//等同于  
$.fn.fun3 = function () { 
    alert("执行方法三"); 
}  
$(this).fun3(); 
```

### 1.2 知识2

jQuery\(function \(\) { }\); 与 \(function \($\) { }\)\(jQuery\);的区别：

```js
jQuery(function () { });  
//相当于  
$(document).ready(function () { });  
/* * * * * * * * * * * * * * * / 
(function ($) { })(jQuery);  
//相当于  
var fn = function ($) { };  
fn(jQuery); 
```

jQuery\(function \(\) { }\);是某个DOM元素加载完毕后执行方法里的代码。

\(function \($\) { }\)\(jQuery\); 定义了一个匿名函数，其中jQuery代表这个匿名函数的实参。通常用在JQuery插件开发中，起到了定义插件的私有域的作用。

## 二、类级别的插件开发

类级别的插件开发最直接的理解就是给jQuery类添加类方法，可以理解为添加静态方法。典型的例子就是$.AJAX\(\)这个函数，将函数定义于jQuery的命名空间中。关于类级别的插件开发可以采用如下几种形式进行扩展：

* 添加一个新的全局函数
* 增加多个全局函数
* 使用jQuery.extend\(object\)
* 使用命名空间

### 2.1 添加一个新的全局函数

添加一个全局函数，我们只需如下定义：

```js
jQuery.foo = function() {    
    alert('This is a test. This is only a test.');   
};
```

### 2.2 增加多个全局函数

添加多个全局函数，可采用如下定义：

```js
jQuery.foo = function() {    
    alert('This is a test. This is only a test.');   
};   
jQuery.bar = function(param) {    
    alert('This function takes a parameter, which is "' + param + '".');   
};    
```

调用时和一个函数的一样的:jQuery.foo\(\);jQuery.bar\(\);或者

```js
$.foo();
$.bar('bar'); 
```

### 2.3 使用jQuery.extend\(object\)

```js
jQuery.extend({       
    foo: function() {       
        alert('This is a test. This is only a test.');       
    },       
    bar: function(param) {       
       alert('This function takes a parameter, which is "' + param +'".');       
    }      
});  
```

### 2.4 使用命名空间

虽然在jQuery命名空间中，我们禁止使用了大量的javaScript函数名和变量名。但是仍然不可避免某些函数或变量名将于其他jQuery插件冲突，因此我们习惯将一些方法封装到另一个自定义的命名空间。

```js
jQuery.myPlugin = {           
    foo:function() {           
        alert('This is a test. This is only a test.');           
    },           
    bar:function(param) {           
       alert('This function takes a parameter, which is "' +        param + '".');     
    }          
};   
```

采用命名空间的函数仍然是全局函数，调用时采用的方法：

```js
$.myPlugin.foo();          
$.myPlugin.bar('baz');  
```

通过这个技巧（使用独立的插件名），我们可以避免命名空间内函数的冲突。

## 三、对象级别的插件开发

对象级别的插件开发需要如下的两种形式。

形式1:

```js
(function($){      
    $.fn.extend({      
    pluginName:function(opt,callback){      
          // Our plugin implementation code goes here.        
    }      
})      
})(jQuery);     
```

形式2：

```js
(function($) {        
    $.fn.pluginName = function() {      
        // Our plugin implementation code goes here.      
    };      
})(jQuery);      
```

上面定义了一个jQuery函数,形参是$，函数定义完成之后,把jQuery这个实参传递进去.立即调用执行。这样的好处是,我们在写jQuery插件时,也可以使用$这个别名,而不会与prototype引起冲突。

### 3.1 在jQuery名称空间下申明一个名字

这是一个单一插件的脚本。如果你的脚本中包含多个插件，或者互逆的插件（例如： $.fn.doSomething\(\) 和 $.fn.undoSomething\(\)），那么你需要声明多个函数名字。但是，通常当我们编写一个插件时，力求仅使用一个名字来包含它的所有内容。我们的示例插件命名为“highlight“

```js
$.fn.hilight = function() {     
  // Our plugin implementation code goes here.     
};       
```

我们的插件通过这样被调用：

```js
$('#myDiv').hilight();   
```

但是如果我们需要分解我们的实现代码为多个函数该怎么办？有很多原因：设计上的需要；这样做更容易或更易读的实现；而且这样更符合面向对象。

这真是一个麻烦事，把功能实现分解成多个函数而不增加多余的命名空间。出于认识到和利用函数是javascript中最基本的类对象，我们可以这样做。就像其他对象一样，函数可以被指定为属性。因此我们已经声明“hilight”为jQuery的属性对象，任何其他的属性或者函数我们需要暴露出来的，都可以在"hilight" 函数中被声明属性。稍后继续。

### 3.2 接受options参数以控制插件的行为

让我们为我们的插件添加功能指定前景色和背景色的功能。我们也许会让选项像一个options对象传递给插件函数。例如：

```js
// plugin definition     
$.fn.hilight = function(options) {     
  var defaults = {     
    foreground: 'red',     
    background: 'yellow'     
  };     
  // Extend our default options with those provided.     
  var opts = $.extend(defaults, options);     
  // Our plugin implementation code goes here.     
};     
```

我们的插件可以这样被调用：

```js
$('#myDiv').hilight({     
  foreground: 'blue'     
});
```

### 3.3 暴露插件的默认设置

我们应该对上面代码的一种改进是暴露插件的默认设置。这对于让插件的使用者更容易用较少的代码覆盖和修改插件。接下来我们开始利用函数对象。

```js
// plugin definition     
$.fn.hilight = function(options) {     
  // Extend our default options with those provided.     
  // Note that the first arg to extend is an empty object -     
  // this is to keep from overriding our "defaults" object.     
  var opts = $.extend({}, $.fn.hilight.defaults, options);     
  // Our plugin implementation code goes here.     
};     
// plugin defaults - added as a property on our plugin function     
$.fn.hilight.defaults = {     
  foreground: 'red',     
  background: 'yellow'     
};      
```

现在使用者可以包含像这样的一行在他们的脚本里：

```js
//这个只需要调用一次，且不一定要在ready块中调用   
$.fn.hilight.defaults.foreground = 'blue';     
```

接下来我们可以像这样使用插件的方法，结果它设置蓝色的前景色：

```js
$('#myDiv').hilight();   
```

如你所见，我们允许使用者写一行代码在插件的默认前景色。而且使用者仍然在需要的时候可以有选择的覆盖这些新的默认值：

```js
// 覆盖插件缺省的背景颜色 
$.fn.hilight.defaults.foreground = 'blue'; 
// … 
// 使用一个新的缺省设置调用插件 
$('.hilightDiv').hilight(); 
// … 
// 通过传递配置参数给插件方法来覆盖缺省设置 
$('#green').hilight({ 
    foreground: 'green' 
});  
```

### 3.4 适当的暴露一些函数

这段将会一步一步对前面那段代码通过有意思的方法扩展你的插件（同时让其他人扩展你的插件）。例如，我们插件的实现里面可以定义一个名叫"format"的函数来格式化高亮文本。我们的插件现在看起来像这样，默认的format方法的实现部分在hiligth函数下面。

```js
// 插件定义
$.fn.hilight = function(options) {     
  // 迭代和重新格式化每个匹配的元素     
  return this.each(function() {     
    var $this = $(this);     
    // …     
    var markup = $this.html();     
    // 调用我们的format函数     
    markup = $.fn.hilight.format(markup);     
    $this.html(markup);     
  });     
};     
// 定义我们的format函数     
$.fn.hilight.format = function(txt) {     
    return '<strong>' + txt + '</strong>';     
}; 
```

我们很容易的支持options对象中的其他的属性通过允许一个回调函数来覆盖默认的设置。这是另外一个出色的方法来修改你的插件。这里展示的技巧是进一步有效的暴露format函数进而让他能被重新定义。通过这技巧，是其他人能够传递他们自己设置来覆盖你的插件，换句话说，这样其他人也能够为你的插件写插件。

考虑到这个篇文章中我们建立的无用的插件，你也许想知道究竟什么时候这些会有用。一个真实的例子是Cycle插件.这个Cycle插件是一个滑动显示插件，他能支持许多内部变换作用到滚动，滑动，渐变消失等。但是实际上，没有办法定义也许会应用到滑动变化上每种类型的效果。那是这种扩展性有用的地方。 Cycle插件对使用者暴露"transitions"对象，使他们添加自己变换定义。插件中定义就像这样：

```js
$.fn.cycle.transitions = { 
    // … 
}; 
```

这个技巧使其他人能定义和传递变换设置到Cycle插件。

### 3.5 保持私有函数的私有性

这种技巧暴露你插件一部分来被覆盖是非常强大的。但是你需要仔细思考你实现中暴露的部分。一但被暴露，你需要在头脑中保持任何对于参数或者语义的改动也许会破坏向后的兼容性。一个通理是，如果你不能肯定是否暴露特定的函数，那么你也许不需要那样做。

那么我们怎么定义更多的函数而不搅乱命名空间也不暴露实现呢？这就是闭包的功能。为了演示，我们将会添加另外一个“debug”函数到我们的插件中。这个 debug函数将为输出被选中的元素格式到firebug控制台。为了创建一个闭包，我们将包装整个插件定义在一个函数中。

```js
(function($) {     
  // 插件定义    
  $.fn.hilight = function(options) {     
    debug(this);     
    // …     
  };     
  // 私有函数debugging     
  function debug($obj) {     
    if (window.console && window.console.log)     
      window.console.log('hilight selection count: ' + $obj.size());     
  };     
//  …     
})(jQuery); 
```

我们的“debug”方法不能从外部闭包进入,因此对于我们的实现是私有的。

### 3.6 支持Metadata插件

在你正在写的插件的基础上，添加对Metadata插件的支持能使他更强大。个人来说，我喜欢这个Metadata插件，因为它让你使用不多的"markup”覆盖插件的选项（这非常有用当创建例子时）。而且支持它非常简单。更新：注释中有一点优化建议。

```js
$.fn.hilight = function(options) {     
  // …     
  // 在元素迭代之前构建主选项     
  var opts = $.extend({}, $.fn.hilight.defaults, options);     
  return this.each(function() {     
    var $this = $(this);     
    // 构建元素特定选项     
    var o = $.meta ? $.extend({}, opts, $this.data()) : opts;     
    //…    
  }
}    
```

这些变动行做了一些事情：它是测试Metadata插件是否被安装如果它被安装了，它能扩展我们的options对象通过抽取元数据这行作为最后一个参数添加到jQuery.extend，那么它将会覆盖任何其它选项设置。现在我们能从"markup”处驱动行为,如果我们选择了“markup”。

调用的时候可以这样写： jQuery.foo\(\); 或 $.foo\(\);

```js
<!--  markup  -->     
<div class="hilight { background: 'red', foreground: 'white' }">     
  Have a nice day!     
</div>     
<div class="hilight { foreground: 'orange' }">     
  Have a nice day!     
</div>     
<div class="hilight { background: 'green' }">     
  Have a nice day!     
</div>   
```

现在我们能高亮哪些div仅使用一行脚本：

```js
$('.hilight').hilight();   
```

整合，下面使我们的例子完成后的代码：

```js
// 创建一个闭包     
(function($) {     
  // 插件的定义     
  $.fn.hilight = function(options) {     
    debug(this);     
    // build main options before element iteration     
    var opts = $.extend({}, $.fn.hilight.defaults, options);     
    // iterate and reformat each matched element     
    return this.each(function() {     
      $this = $(this);     
      // build element specific options     
      var o = $.meta ? $.extend({}, opts, $this.data()) : opts;     
      // update element styles     
      $this.css({     
        backgroundColor: o.background,     
        color: o.foreground     
      });     
      var markup = $this.html();     
      // call our format function     
      markup = $.fn.hilight.format(markup);     
      $this.html(markup);     
    });     
  };     
  // 私有函数：debugging     
  function debug($obj) {     
    if (window.console && window.console.log)     
      window.console.log('hilight selection count: ' + $obj.size());     
  };     
  // 定义暴露format函数     
  $.fn.hilight.format = function(txt) {     
    return '<strong>' + txt + '</strong>';     
  };     
  // 插件的defaults     
  $.fn.hilight.defaults = {     
    foreground: 'red',     
    background: 'yellow'     
  };     
// 闭包结束     
})(jQuery);     
```

这段设计已经让我创建了强大符合规范的插件。我希望它能让你也能做到。

## 四、总结：开发jQuery插件标准结构

再来开发一个插件，回顾一下开发jQuery插件标准结构和步骤：

1. 定义作用域
2. 为jQuery扩展一个插件
3. 设置默认值
4. 支持jQuery选择器
5. 支持jQuery的链式调用
6. 插件里的方法编写

### 4.1 定义作用域

定义一个JQuery插件，首先要把这个插件的代码放在一个不受外界干扰的地方。如果用专业些的话来说就是要为这个插件定义私有作用域。外部的代码不能直接访问插件内部的代码。插件内部的代码不污染全局变量。在一定的作用上解耦了插件与运行环境的依赖。说了这么多，那要怎样定义一个插件的私有作用域？

```js
//step01 定义JQuery的作用域  
(function ($) {  
    //…. 
})(jQuery); 
```

### 4.2 为jQuery扩展一个插件

当定义好了JQuery的作用域后，最核心也是最迫切的一步就是为这个JQuery的实例添加一个扩展方法。首先我们为这个Jqury插件命名一个方法，叫easySlider，当在调用这个插件的时候，我们可以通过options来给这个插件传递一些参数。具体的定义方法看如下代码：

```js
//step01 定义JQuery的作用域  
(function ($) {  
    //step02 插件的扩展方法名称  
    $.fn.easySlider = function (options) {  
        //….  
    }  
})(jQuery); 
```

到现在为止，其实一个最简单的JQuery插件就已经完成了。调用的时候可以$\("\#domName"\).easySlider\({}\)，或者$\(".domName"\).easySlider\({}\) 或者更多的方式来调用这个插件。

### 4.3 设置默认值

定义一个jQuery插件，就像定义一个.net控件。一个完美的插件，应该是有比较灵活的属性。我们来看这段代码：

```php
<asp:TextBox ID="TextBox1" Width="20" Height="100" runat="server"></asp:TextBox>
```

TextBox控件有Width和Height属性，用户在用TextBox时，可以自由的设置控件的Height和Width，也可以不设置值，因为控件自身有默认值。

那准备开发一个jQuery插件时，在用户未指定属性时，应该有默认值，在jQuery可以分两步实现这样的定义,看如下代码step03-a，step03-b。

```js
//step01 定义JQuery的作用域  
(function ($) {  
    //step03-a 插件的默认值属性  
    var defaults = {  
        prevId: 'prevBtn',  
        prevText: 'Previous',  
        nextId: 'nextBtn',  
        nextText: 'Next' 
        //……  
    };  
    //step02 插件的扩展方法名称  
    $.fn.easySlider = function (options) {  
        //step03-b 合并用户自定义属性，默认属性  
        var options = $.extend(defaults, options);  
    }  
})(jQuery); 
```

做程序的人都喜欢创新，改改变量名呀，换一个行呀这些。当看到用var defaults = {}来表示一个默认属性时，在自己写jQuery插件时就想着与众不同，所以用var default01 ={} ，var default02 ={}来表示默认属性了。然后默认属性名五花八门，越来越糟。所以建议在写jQuery插件时，定义默认属性时，都用defaults变量来代表默认属性，这样的代码更具有可读性。

有人看到这行代码：var options = $.extend\(defaults, options\)，皱起眉头，表示不解。那我们先来看如下代码：

```js
var obj01 = { 
    name: "英文名：Sam Xiao", 
    age: 29, 
    girlfriend: { 
        name: "Yang", 
        age: 29
    } 
}  
var obj02 = { 
    name: "中文名：XiaoJian", 
    girlfriend: { 
        name: "YY"
    } 
};  

var a = $.extend(obj01, obj02);  
var b = $.extend(true, obj01, obj02);  
var c = $.extend({}, obj01, obj02);  
var d = $.extend(true,{}, obj01, obj02); 
```

把代码拷贝到开发环境中，分别看一下a,b,c,d的值，就明白了var options = $.extend\(defaults, options\)的含义了。表示 options 去覆盖了defaults的值，并把值赋给了options。

在插件环境中，就表示用户设置的值，覆盖了插件的默认值；如果用户没有设置默认值的属性，还是保留插件的默认值。

### 4.4 支持jQuery选择器

jQuery选择器是jQuery的一个优秀特性，如果我们的插件写来不支持jQuery选择器确实是一个不小的遗憾。使自己的jQuery插件能支持多个选择器我们的代码应该这样定义：

```js
//step01 定义JQuery的作用域  
(function ($) {  
    //step03-a 插件的默认值属性  
    var defaults = {  
        prevId: 'prevBtn',  
        prevText: 'Previous',  
        nextId: 'nextBtn',  
        nextText: 'Next' 
        //……  
    };  
    //step02 插件的扩展方法名称  
    $.fn.easySlider = function (options) {  
        //step03-b 合并用户自定义属性，默认属性  
        var options = $.extend(defaults, options);  
        //step4 支持JQuery选择器  
        this.each(function () {  

        });  
    }  
})(jQuery); 
```

### 4.5 支持jQuery的链式调用

上边的代码看似完美了，其实也不那么完美。到目前为止还不支持链式调用。为了能达到链式调用的效果必须要把循环的每个元素return

```js
//step01 定义JQuery的作用域  
(function ($) {  
    //step03-a 插件的默认值属性  
    var defaults = {  
        prevId: 'prevBtn',  
        prevText: 'Previous',  
        nextId: 'nextBtn',  
        nextText: 'Next' 
        //……  
    };  
    //step02 插件的扩展方法名称  
    $.fn.easySlider = function (options) {  
        //step03-b 合并用户自定义属性，默认属性  
        var options = $.extend(defaults, options);  
        //step4 支持JQuery选择器  
        //step5 支持链式调用  
        return this.each(function () {  

        });  
    }  
})(jQuery); 
```

这样的定义才能支持链接调用。比如支持这样的调用：

```js
$(".div").easySlider({prevId:"",prevText:""}).css({ "border-width": "1", "border-color": "red", "border-bottom-style": "dotted" });
```

### 4.6 插件里的方法

往往实现一个插件的功能需要大量的代码，有可能上百行，上千行，甚至上万行。我们把这代码结构化，还得借助function。在第一点已经说了，在插件里定义的方法，外界不能直接调用，我在插件里定义的方法也没有污染外界环境。现在就尝试着怎么样在插件里定义一些方法：

```js
//step01 定义JQuery的作用域  
(function ($) {  
    //step03-a 插件的默认值属性  
    var defaults = {  
        prevId: 'prevBtn',  
        prevText: 'Previous',  
        nextId: 'nextBtn',  
        nextText: 'Next' 
        //……  
    };  
    //step06-a 在插件里定义方法  
    var showLink = function (obj) {  
        $(obj).append(function () { return "(" + $(obj).attr("href") + ")" });  
    }  

    //step02 插件的扩展方法名称  
    $.fn.easySlider = function (options) {  
        //step03-b 合并用户自定义属性，默认属性  
        var options = $.extend(defaults, options);  
        //step4 支持JQuery选择器  
        //step5 支持链式调用  
        return this.each(function () {  
            //step06-b 在插件里定义方法  
            showLink(this);  
        });  
    }  
})(jQuery); 
```

步骤step06-a：在插件里定义了一个方法叫showLink\(\); 这个方法在插件外是不能直接调用的，有点像C\#类里的一个私有方法，只能满足插件内部的使用。步骤step06-b演示了怎样调用插件内部的方法。

好了，本文结束了。

jQuery插件开发是不是很简单？开发只要形成了标准，然后再去阅读别人的代码就没有那么吃力了。

---

**参考文章**

* [jQuery插件开发全解析](http://www.cnblogs.com/silverLee/archive/2009/12/22/1629925.html)
* [不定义JQuery插件，不要说会JQuery](http://blog.csdn.net/a497785609/article/details/41444567)



