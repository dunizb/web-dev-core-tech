# 第6节 使用JavaScript 写Web路由

从网上找了两个示例，第一个是[@KrasimirTsonev](https://twitter.com/KrasimirTsonev)用[100行代码写的一个示例](https://krasimirtsonev.com/blog/article/A-modern-JavaScript-router-in-100-lines-history-api-pushState-hash-url)，另一个是[@Joakim Carlstein](https://joakim.beng.se/)用[20行代码写的一个示例](https://joakim.beng.se/blog/posts/a-javascript-router-in-20-lines.html)。首先来看第一个示例。

## 一、示例1：使用100行代码写一个Web路由

单页面现在是一种很流行的应用程序，可以说是到处可见。而单页面中有一个非常重要的概念，那就是路由机制，也就是说单页面应用程序要能正常的运行，就意味着需要一个可靠的路由机制。接下来看看@KrasimirTsonev是怎么实现的。

### 1.1 目标

这个路由将会具备：

* 代码少于100行
* 支持hash类型的URL，比如http://site.com\#products/list
* 使用HTML History API
* 提供易于使用的API
* 不自动运行,只是需要的改变的时候才运行

### 1.2 实现思路

这个示例设计的是只有一个路由器实例。当然这可能不是一个很好的选择，那是因为我们在一个应用程序中，特别是在一个复杂的运用程序中，需要多个路由器才能实现。如果我们实现单例的模式，就不需要将路由器从对象传递到对象。所以我们先创建一个对象Router：

```js
let Router = {
    routes: [],
    mode: null,
    root: '/'
}
```

Router对象中设置了三个属性：

* routes：用于保持当前注册的路由
* mode：可以使用hash或者history模式
* root：应用程序的根URL路径，只有当我们使用了history.pushState\(\)时才需要它

接下来需要创建一个路由器的方法，有两件事情需要处理，可以在一个函数内来处理：

```js
let Router = {
    routes: [],
    mode: null,
    root: '/',
    config: function (options) {
        this.mode = options && options.mode && (options.mode = 'history') && !!(history.pushState) ? 'history' : 'hash';

        this.root = options && options.root ? '/' + this.clearSlashes(options.root) + '/' : '/'

        return this
    }
}
```

只有当我们需要和只有在支持pushState的情况下，mode值等于history，否则将会会使用URL中的hash。root默认设置为一个/。

路由器中重要的一部分是获取当前URL，因为它会告诉我们此刻在哪里。获取当前URL有两种模式，所以这里使用if语句来处理：

```js
getFragment: function () {
    let fragment = '';

    if (this.mode === 'history') {
        fragment = this.clearSlashes(decodeURI(location.pathname + location.search));
        fragment = fragment.replace(/\?(.*)$/, '');
        fragment = this.root != '/' ? fragment.replace(this.root, '') : fragment;
    } else {
        let match = window.location.href.match(/#(.*)$/);
        fragment = match ? match[1] : '';
    }

    return this.clearSlashes(fragment);
}
```

在这两种情况下，我们都在使用全局的window.location对象。在history的mode中，需要删除URL的根部分。除此之外，还应该删除所有的GET参数，所以用正则\(/\?\(.\*\)$/\)来完成。正如大家所看到的，hash的值获取就简单多了。注意clearSlashes\(\)函数的使用方法。它的工作就是从字符串的开头和结尾删除/。这一点也是非常必要的，因为我们不想强制开发人员使用特定的url格式。不管开发者传递的是什么，都将处理成同样的值。

```js
clearSlashes: function (path) {
    return path.toString().replace(/\$/, '').replace(/^\//, '')
}
```

在实际工作中提供的东西总应该尽量给开发人员提供尽可能多的可控制的功能。在几乎所有的路由器实现中，路由被定义为字符串。不过，我更喜欢直接使用一个正则表达式。因为这样更为灵活。

同样的，开发者在使用一个路由器时，他能方便的添加和删除。也就是说，需要加上添加和删除路由的功能。

```js
add: function (re, handler) {
    if (typeof re == 'function') {
        handler = re;
        re = '';
    }
}

remove: function (param) {
    for (let i = 0; i < this.routes.length, r = this.routes[i]; i++) {
        if (r.handler === param || r.re.toString() === param.toString()) {
            this.routes.splice(i, 1);
            return this;
        }
    }

    return this;
}
```

有时候，我们可能需要重新初始化类。因此，在这种情况下，可以像下面这样使用flush方法：

```js
flush: function () {
    this.routes = [];
    this.mode = null;
    this.root = '/;
    return this
}
```

我们有一个添加和删除url的API。我们也能得到当前的URL。因此，下一步要做的就是比较注册的条目：

```js
check: function (f) {
    let fragment = f || this.getFragment();
    for (let i = 0; i < this.routes.length; i++) {
        let match = fragment.match(this.routes[i].re);
        if (match) {
            match.shift();
            this.routes[i].handler.apply({}, match);
            retrun this;
        }
    }
    return this;
}
```

我们使用getFragment方法或接受它作为函数的参数来获取片段。在此之后，我们在routes上执行一个正常的循环，并试图找到相匹配的。如果正则表达式不匹配，则有一个值 为null相匹配。否则它的值是这样的：

```js
["products/12/edit/22", "12", "22", index: 1, input: "/products/12/edit/22"]
```

像数组一样的对象，它包含匹配的字符串和所有可记住的子字符串。这意味着，如果我们改变第一个元素，将得到一个动态的数组。例如：

```js
Router.add(/about/, function (){
    console.log(about)
})
.add(/products\/(.*)\/edit\/(.*)/, function(){
    console.log('products', arguments)
})
.add(function(){
    console.log('default')
})
.check('/products/12/edit/22')
```

输出的结果：

```js
products ["12", "22"]
```

这就是如何处理动态url的方法。

在实际的项目当中，我们不可能一直运行这个方法来检测。那么就需要一个提供另外的东西，比如说添加一个逻辑，它会通知我们的地址栏的变化，甚至包括点击浏览器的后退或前进按钮等操作。在History API有一个popstate事件。当URL更改时，将会触发这个事件。然而，有一些浏览器，在页面加载时就会发送这个事件。这样一来，我想了另一个解决办法。使用setinterval来写一个监视，即使mode的值设置为hash。

```js
listen: function () {
    let self = this;
    let current = self.getFragment();
    let fn = function () {
        if (current !== self.getFragment()) {
            current = self.getFragment();
            self.check(current);
        }
    }

    clearInterval(this.interval);
    this.interval = setInterval(fn, 50);

    return this;
}
```

我们需要保留最新的URL，以便我们能够将它与新的URL进行比较。

最后，我们的路由器需要一个能改变当前地址的函数，当然也需要触发路由的处理器。

```js
navigate: function (path) {
    path = path ? path : '';
    if (this.mode === 'history') {
        history.pushState(null, null, this.root + this.clearSlashes(path));
    } else {
        window.location.href = window.location.href.replace(/#(.*)$/. '') + '#' + path;
    }

    return this;
}
```

同样，根据mode属性做不同的事情。如果History API可用，我们就使用pushState，否则就使用window.location。

到此，一个路由器就完成了，最终的代码如下：

```js
let Router = {
    routes: [],
    mode: null,
    root: '/',
    config: function (options) {
        this.mode = options && options.mode && (options.mode == 'history') && !!(history.pushState) ? 'history' : 'hash';
        this.root = options && options.root ? '/' + this.clearSlashes(options.root) + '/' : '/' 
        return this;
    },
    getFragment: function () {
        let fragment = '';
        if (this.mode === 'history') {
            fragment = this.clearSlashes(decodeURI(location.pathname + location.search));
            fragment = fragment.replace(/\?(.*)$/, '');
            fragment = this.root != '/' ? fragment.replace(this.root, '') : fragment;
        } else {
            let match = window.location.href.match(/#(.*)$/);
            fragment = match ? match[1] : '';
        }
        return this.clearSlashes(fragment);
    },
    clearSlashes: function (path) {
        return path.toString().replace(/\/$/, '').replace(/^\//, '');
    },
    add: function (re, handler) {
        if (typeof re == 'function') {
            handler = re;
            re = '';
        }
        this.routes.push({
            re: re,
            handler: handler
        });
        return this;
    },
    remove: function (param) {
        for (let i  = 0; i < this.routes.length, r = this.routes[i]; i++) {
            if (r.handler === param || r.re.toString() === param.toString()) {
                this.routes.splice(i, 1);
                return this;
            }
        }
        return this;
    },
    flush: function () {
        this.routes = [];
        this.mode = null;
        this.root = '/';
        return this;
    },
    check: function (f) {
        let fragment = f || this.getFragment();
        for (let i = 0; i < this.routes.length; i++) {
            let match = fragment.match(this.routes[i].re);
            if (match) {
                match.shift();
                this.routes[i].handler.apply({}, match);
                return this;
            }
        }
        return this;
    },
    listen: function () {
        let  self = this;
        let current = self.getFragment();
        let fn = function () {
            if (current !== self.getFragment()) {
                current = self.getFragment();
                self.check(current);
            }
        }
        clearInterval(this.interval);
        this.interval = setInterval(fn, 50);
        return this;
    },
    navigate: function (path) {
        path = path ? path :  '';
        if (this.mode === 'history') {
            history.pushState(null, null, this.root + this.clearSlashes(path));
        } else {
            window.location.href = window.location.href.replace(/#(.*)$/, '') + '#' + path;
        }
        return this;
    }
}

// 配置
Router.config({mode: 'history'});

// 返回到初始状态
Router.navigate()

// 添加路由
Router.add(/about/, function () {
    console.log('about');
})
.add(/products\/(.*)\/edit\/(.*)/, function () {
    console.log('products', arguments);
})
.add(function(){
    console.log('default');
})
.check('/products/12/edit/22').listen();

// 转发
Router.navigate('/about')
```

## 二、示例2：使用20行代码写一个Web路由

上面我们看完了[@KrasimirTsonev](https://twitter.com/KrasimirTsonev)用[100行代码写的Web路由](https://krasimirtsonev.com/blog/article/A-modern-JavaScript-router-in-100-lines-history-api-pushState-hash-url)。接下来，咱位再看看[@Joakim Carlstein](https://joakim.beng.se/)是怎么用[20行代码写的一个Web路由](https://joakim.beng.se/blog/posts/a-javascript-router-in-20-lines.html)。

@Joakim Carlstein想出20行代码创建一个简单客户端路由的思路主要来源于[用20行代码写一个模板](https://krasimirtsonev.com/blog/article/Javascript-template-engine-in-just-20-line)，而这个灵感却又来自于[John Resig在同一主题上的文章](https://ejohn.org/blog/javascript-micro-templating/)。是不是很有趣，很鼓舞人心。

@Joakim Carlstein是怎么用20行代码写一个路由。如果你对这方面感兴趣的话，请继续往下阅读。

首先创建一个HTML模板：

```js
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <title>创建一个Web路由</title>
        <script>
            // Johe的模板引擎代码放在这里
            // http://ejohn.org/blog/javascript-micro-templating/
        </script>
    </head>
</html>
```

模板中使用了&lt;script&gt;标签，并且设置type="text/html"。这将使浏览器不解析它们的内容，这也是我们想要的那样。

```js
<script type="text/html" id="home">
    <h1>Router FTW!</h1>
</script>

<script type="text/html" id="template1">
    <h1>Page 1: <%= greeting %></h1>
    <p><%= moreText %></p>
</script>

<script type="text/html" id="template2">
    <h1>Page 2: <%= heading %></h1>
    <p>Lorem ipsum...</p>
</script>
```

正如你所看到的，这是非常基础的部分，那是因为我们把主要精力会放在路由器的那部分，所以不想花太多的精力在模板上面。

对于这个路由，作者将使用URL的hash，也就是URL中\#符号后面的那部分。例如http://example.com/\#our/url/here中的our/url/here。本来可以使用HTML的History API，但这个示例中将不采用这个。

路由器将使用onhashchange事件来处理页面加载后的路由更改和通常的onload事件，用来处理页面加载url的任何路由。

先从注册路由函数开始：

```js
// 使用一个hash来存储我们的路由
let routes = {}

// 路由注册函数
function route (path, templateId, controller) {
    routes[path] = {
        templateId: templateId,
        controller: controller
    }
}
```

现在我们就可以创建新的路由。请注意，下面的代码模仿了AngularJS的控制器：

```js
route('/', 'home', function () {});

route('/page1', 'template1', function () {
    this.greeting = 'Hello world!';
    this.moreText = 'Bacon ipsum...';
});

route('/page2', 'template2', function () {
    this.heading = 'I\'m page two!';
});
```

结果上面的代码并没有起任何的作用，那是因为我们还没有处理好路由。要上面的代码能有作用，就需要添加路由的处理程序。也就是说要构建路由器。但是首先需要一个地方来呈现我们的页面。

```js
let el = null;

function router () {
    // 延迟加载view元素
    el = el || document.getElementById('view');

    // 当前路由url, 删除`#`
    let url = location.hash.clice(1) || '/';

    let router = routes[url];

    if (el && route.controller) {
        // 使用John Resig的模板引擎，渲染路由模板
        el.innerHTML = tmpl(route.templateId, new route.controller());
    }
}

// 监听hash的变化
window.addEventListener('hashchange', router);

// 监听页面加载
window.addEventListener('load', router);
```

这就是最基本的路由。另外，导航中的链接应该也能工作，也就是说可以通过浏览器直接进入特定的路由。比如path/to/your/router.html\#/page1，你应该可以看到page1对应的内容。

为了使路由器变得理有用，可以添加单向数据绑定，以便在控制器中数据发生变化时自动更新视图。这需要使用到Object.observe\(\)。

在上面的路由函数上添加一个对象观察者，它会得新运行当前视图：

```js
let el = null, current = null;

function router () {
    el = el || document.getElementById('view');

    if (current) {
        Object.unobserve(current.controller, current.render);
        current = null
    }

    let url = location.hash.slice(1) || '/';
    let route = routes[url];

    if (el && route.controller) {
        current = {
            controller: new route.controller,
            template: tmpl(route.templateId),
            render: function () {
                el.innerHTML = this.template(this.controller);
            }
        };

        current.render();

        Object.observe(current.controller, current.render.bind(current));
    }
}
```

就这些代码了可以帮助我们实现单向数据绑定的功能。为了验证上面的路由是否有效，可以测试一下：

```js
route('/page1', 'template1', function () {
    this.greeting = 'Hello world!';
    this.moreText = 'Loading...';

    setTimeout(function () {
        this.moreText = 'Bacon ipsum...';
    }.bind(this), 500);
});
```

如果你感兴趣的话，[可以查阅读最终代码](https://gist.github.com/joakimbeng/7918297)。

## 总结

上面两个示例，通过不上一百行的JavaScript代码，写出不同的Web路由，是不是很有意思。虽然这些路由功能还不是非常的强，也有一定的缺陷。但对于帮助我们学习如何使用 JavaScript写Web路由还是很有帮助的。如果你感兴趣，也可以尝试一下，用极少数的代码，写一个Web路由。

---

原文：[https://www.w3cplus.com/javascript/a-javascript-router.html](https://www.w3cplus.com/javascript/a-javascript-router.html)

