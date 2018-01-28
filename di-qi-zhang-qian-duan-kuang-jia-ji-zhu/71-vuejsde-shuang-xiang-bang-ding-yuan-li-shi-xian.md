# 第1节 Vue.js的双向绑定原理实现

Vue.js 最核心的功能有两个，一是响应式的数据绑定系统，二是组件系统。本文仅探究双向绑定是怎样实现的。先讲涉及的知识点，再用简化得不能再简化的代码实现一个简单的 hello world 示例。

## 一、访问器属性

访问器属性是对象中的一种特殊属性，它不能直接在对象中设置，而必须通过 defineProperty\(\) 方法单独定义。

```js
var obj = { };
// 为obj定义一个名为 hello 的访问器属性
Object.defineProperty(obj, "hello", {
   get: function () {
      console.log('get方法被调用了');
   },
   set: function (val) {
      console.log('set方法被调用了，参数是:' + val);
   }
})
obj.hello // get方法被调用了
obj.hello = 'abc' // set方法被调用了，参数是:abc
```

访问器属性的"值"比较特殊，读取或设置访问器属性的值，实际上是调用其内部特性：get和set函数。

get 和 set 方法内部的 this 都指向 obj，这意味着 get 和 set 函数可以操作对象内部的值。另外，访问器属性的会"覆盖"同名的普通属性，因为访问器属性会被优先访问，与其同名的普通属性则会被忽略。

## 二、极简双向绑定的实现

```js
<input type="text" id="a" />
<span id="b"></span>

<script>
var obj = {};
Object.definedProperty(obj, 'hello', {
    set: function(newVal) {
        document.getElementById('a').value = newVal;
        document.getElementById('b').innerHTML = newVal;
    }
});

document.addEventListener('keyup', function(e) {
    obj.hello = e.target.value;
});
</script>
```

此例实现的效果是：随文本框输入文字的变化，span 中会同步显示相同的文字内容；在js或控制台显式的修改 obj.hello 的值，视图会相应更新。这样就实现了 model =&gt; view 以及 view =&gt; model 的双向绑定。

## 三、分解任务

上述示例仅仅是为了说明原理。我们最终要实现的是：

```js
<div id="app">
    <input type="text" v-model="text">
    {{ text }}
</div>

var vm = new Vue({
    el: '#app',
    data: {
        text: 'hello world'
    }
});
```

首先将该任务分成几个子任务：

1. 输入框以及文本节点与 data 中的数据绑定
2. 输入框内容变化时，data 中的数据同步变化。即 view =&gt; model 的变化。
3. data 中的数据变化时，文本节点的内容同步变化。即 model =&gt; view 的变化。

要实现任务一，需要对 DOM 进行编译，这里有一个知识点：**DocumentFragment**。

## 四、DocumentFragment

DocumentFragment（文档片段）可以看作节点容器，它可以包含多个子节点，当我们将它插入到 DOM 中时，只有它的子节点会插入目标节点，所以把它看作一组节点的容器。使用 DocumentFragment 处理节点，速度和性能远远优于直接操作 DOM。Vue 进行编译时，就是将挂载目标的所有子节点劫持（真的是劫持，通过 append 方法，DOM 中的节点会被自动删除）到 DocumentFragment 中，经过一番处理后，再将 DocumentFragment 整体返回插入挂载目标。

```js
<div id="app">
    <input type="text" v-model="text"/>
</div>
<script>
const app = document.getElementById("app");
const nodetoFragment = (node) => {
    const flag = document.createDocumentFragment();
    let child;
    whild(child = node.firstChild){
        flag.appendChild(child);//不断劫持挂载元素下的所有dom节点到创建的DocumentFragment
    }
    return flag
}
const dom = nodetoFragment(app);
</script>
```

## 五、数据初始化绑定

当已经获取到所有的dom元素之后，则需要对数据进行初始化绑定，这里简单涉及到了模板的编译。

```js
//  编译HTML模板
const compile = (node,vm) => {
    const regex = /\{\{(.*)\}\}/;//为临时正则表达式，为demo而生
    //如果节点类型为元素的话
    if(node.nodeType === 1){
        const attrs = node.attributes;//学到一个新属性。。。
        for(let i = 0;i < attrs.length; i++){
            let attr = attrs[i];
            if(attr.nodeName === "v-model"){
                let name = attr.nodeValue;
                node.addEventListener("input",function (e) {
                    vm.data[name] = e.target.value;
                });
                node.value = vm.data[name];
                node.removeAttribute("v-model");
            }
        }
    }
    //如果节点类型为文本的话
    if(node.nodeType === 3){
        if(regex.test(node.nodeValue)){
            let name = RegExp.$1;//获取搭配匹配的字符串，又学到了。。。
            name = name.trim();
            node.nodeValue = vm.data[name];
        }
    }
};

//劫持挂载元素到虚拟dom
let nodeToFragment = (node,vm) => {
    const flag = document.createDocumentFragment();
    let child;
    while(child = node.firstChild){
        compile(child,vm);//绑定数据，插入到虚拟DOM中
        flag.appendChild(child);
    }
    return flag;
};

//初始化
class Vue {
    constructor(option){
        this.data = option.data;
        let id = option.el;
        let dom = nodeToFragment(document.getElementById(id),this);
        document.getElementById(id).appendChild(dom);
    }
}

const vm = new Vue({
    el : "app",
    data : {
        text : "hello world"
    }
})
```

通过以上代码先实现了第一个要求，文本框和文本节点已经出现了hello woeld了

## 六、响应式的数据绑定

接下来我们要实现数据双向绑定的第一步，即view-&gt;model的绑定。根据之前那个简单的例子看到，我们实时获取input中的值，通过Object.defineProperty将data中的text设置为vm的访问器属性，通过set方法，当我们在设置vm.data的值时，实现数据层的绑定。在这一步，set中要做的操作是更新属性的值。

```js
let defineReactive = (obj,key,val) => {
    Object.defineProperty(obj,key,{
        get(val){
            return val;
        }
        set(newVal,oldVal){
            if(newVal === oldVal) return;
            val = newVal;
        }
    })
};

//监听数据
let observe = (obj,vm) => {
    Object.keys(obj).forEach((key)=>{
        defineReactive(vm.data,key,obj[key]);
    })
};
```

## 七、订阅/发布模式（subscribe&publish）

text 属性变化了，set方法触发了，可以通过view层的改变实时改变数据，可是并没有改变文本节点的数据。一个新的知识点：订阅发布模式。

订阅发布模式（又称为观察者模式）定义了一种一对多的关系，让多个观察者同时监听一个主题对象，这个主体对象的改变会通知所有观察者对象。

发布者发出通知=&gt;主题对象收到通知并推送给订阅者=&gt;订阅者执行操作

```js
// 三个订阅者
let sub1 = {updata(){console.log(1);}};
let sub2 = {updata(){console.log(2);}};
let sub3 = {updata(){console.log(3);}};

// 一个主题发布器
class Dep{
  constructor(){
    this.subs = [sub1,sub2,sub3];
  }
  notify(){
    subs.forEach((sub) => {
      sub.updata();
    })
  }
}
const dep = new Dep();

// 一个发布者
const pub = {
  publish(){
    dep.notipy();
  }
};
pub.publish();
```

上图为一个简单实例，发布者执行发布命令，所有这个主题的订阅者执行更新操作。接下去我们要做的就是，当set方法触发后，input作为发布者，改变了text属性；而文本节点作为订阅者，在收到消息后执行更新操作。

## 八、双向绑定的实现

每次new一个新的vue对象时，主要是做了两件事，一件是监听数据：observer\(监听数据\)，第二个是编译HTML，nodeToFragement\(id\)。

在监听数据的过程中，会为data中的每一个属性生成一个主题对象。

而在编译HTML的过程中，会为每个与数据绑定的相关节点生成一个订阅者watcher，订阅者watcher会将自己订阅到相应属性的dep中。

在前面的方法中已经实现了：修改输入框内容=&gt;再时间回调中修改属性值=&gt;触发属性的set方法。

接下来要做的是发出通知dep.notify=&gt;发出订阅者的update方法=&gt;更新视图。

那么如何将watcher添加到关联属性的dep中呢。

编译HTML过程中，为每一个与data关联的节点生成一个watcher，那么watcher中又发生了什么？

```js
//  每一个属性节点的watcher
class Watcher{
  constructor(vm,node,name){
    Dep.target = this;
    this.name = name;
    this.node = node;
    this.vm = vm;
    this.update();
    Dep.target = null;
  }
  update(){
    //获得最新值，然后更新视图
    this.get();
    this.node.nodeValue = this.value;
  }
  get(){
    this.value = this.vm.data[this.name];
  }
}
```

在编译HTML的过程中，生成watcher

```js
let complie = (node,vm){
    ......
    //如果节点类型为文本的话
    if(node.nodeType === 3){
        if(regex.test(node.nodeValue)){
            let name = RegExp.$1;
            name = name.trim();
            node.nodeValue = vm.data[name];

            new Watcher(vm,node,name);//在此处添加订阅者
        }
    }
}
```

首先将自己赋给了一个全局变量Dep.target;然后执行了uodate方法，进而执行了get方法，读取了vm的访问器属性，从而触发了访问器属性的get方法，get方法将相应的watcher添加到对应访问器属性的dep中。再次，获取属性的值，然后更新视图。最后将dep.target设置为空，是因为这是个全局变量也是watcher与dep之间唯一的桥梁，任何时间都只能保证只有一个值。（其实就是说全局一个主题，每个订阅者和发布者都是通过这个主题进行沟通。当执行代码时，这个主题接受到一个发布通知，通知完所有订阅者，然后注销掉，用于下一个通知发布。啰嗦了一段就是想讲为什么要设置Dep.target = null）。

```js
//  一个主题发布器
class Dep(){
    constructor(){
        this.subs = [];
    }
    notify(){
        this.subs.forEach((sub) => {
            sub.update();
        }
    }
    addSub(sub){
        this.subs.push(sub);
    }
}
let defineReactive = (obj,key,val) => {
    let dep = new Dep();
    Object.defineProperty(obk,key,{
        get(){
            if(dep.target) dep.addSub(dep.target);
        }        
        set(newVal,oldVal){
            if(newVal === oldVal) return;
            val = newVal;
            dep.notify();
        }
    })
}
```

至此，hello world 双向绑定就基本实现了。文本内容会随输入框内容同步变化，在控制器中修改 vm.text 的值，会同步反映到文本内容中。

---

原文链接：[http://www.cnblogs.com/kidney/p/6052935.html](http://www.cnblogs.com/kidney/p/6052935.html)

