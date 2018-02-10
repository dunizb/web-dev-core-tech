# 第1节 Vue keep-alive实践总结

`<keep-alve>`是Vue的内置组件，能在组件切换过程中将状态保留在内存中，防止重复渲染DOM。

`<keep-alive>` 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。和 `<transition>` 相似，`<keep-alive>` 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中。

props
* include: 字符串或正则表达式。只有匹配的组件会被缓存。
* exclude: 字符串或正则表达式。任何匹配的组件都不会被缓存。

在2.1.0版本Vue中

## 一、常见用法

有如下组件
```js
// 组件
export default {
   name: 'test-keep-alive',
   data () {
      return {
          includedComponents: "test-keep-alive"
      }
   }
}
```

各种情况如下
```html
<keep-alive include="test-keep-alive">
    <!-- 将缓存name为test-keep-alive的组件 -->
    <component></component>
</keep-alive>
 
<keep-alive include="a,b">
    <!-- 将缓存name为a或者b的组件，结合动态组件使用 -->
    <component :is="view"></component>
</keep-alive>
 
<!-- 使用正则表达式，需使用v-bind -->
<keep-alive :include="/a|b/">
    <component :is="view"></component>
</keep-alive>
 
<!-- 动态判断 -->
<keep-alive :include="includedComponents">
    <router-view></router-view>
</keep-alive>
 
<keep-alive exclude="test-keep-alive">
    <!-- 将不缓存name为test-keep-alive的组件 -->
    <component></component>
</keep-alive>
```
## 二、结合router，缓存部分页面

`router-view` 也是一个组件，如果直接被包在 `keep-alive` 里面，所有路径匹配到的视图组件都会被缓存：
```html
<keep-alive>
    <router-view>
        <!-- 所有路径匹配到的视图组件都会被缓存！ -->
    </router-view>
</keep-alive>
```

如果只想 `router-view` 里面某个组件被缓存，怎么办？
* 使用 `include`和`exclude`
* 增加 `router.meta` 属性

### 2.1 使用 include/exclude

组件a
```js
// 组件 a
export default {
  name: 'a',
  data () {
    return {}
  }
}
```
```html
<keep-alive include="a">
    <router-view>
        <!-- 只有路径匹配到的视图 a 组件会被缓存！ -->
    </router-view>
</keep-alive>
```

exclude 例子类似。

**缺点：需要知道组件的 name，项目复杂的时候不是很好的选择**
### 2.2 增加 router.meta 属性

使用`$route.meta`的`keepAlive`属性：
```html
<keep-alive>
    <router-view v-if="$route.meta.keepAlive"></router-view>
</keep-alive>
<router-view v-if="!$route.meta.keepAlive"></router-view>
```

需要在`router`中设置`router`的元信息`meta`：
```js
//...router.js
export default new Router({
    routes: [{
        path: '/',
        name: 'Hello',
        component: Hello,
        meta: {
            keepAlive: false // 不需要缓存
        }
    },{
        path: '/page1',
        name: 'Page1',
        component: Page1,
        meta: {
            keepAlive: true // 需要被缓存
        }
    }]
})
```

**优点：不需要例举出需要被缓存组件名称**

以上面 router 的代码为例：
```html
<!-- Page1页面 -->
<template>
  <div class="hello">
    <h1>Vue</h1>
    <h2>{{msg}}</h2>
    <input placeholder="输入框"></input>
  </div>
</template>

<!-- Hello页面 -->
<template>
  <div class="hello">
    <h1>{{msg}}</h1>
  </div>
</template>
```

在Page1页面输入框输入“asd”，然后手动跳转到Hello页面；

回到Page1页面发现之前输入的"asd"依然保留，说明页面信息成功保存在内存中；
![](https://images2017.cnblogs.com/blog/899224/201708/899224-20170830174311562-549603139.png)

图1 进入Page1页面，并输入"asd"
![](https://images2017.cnblogs.com/blog/899224/201708/899224-20170830174444155-423592300.png)

图2 跳转到Hello
![](https://images2017.cnblogs.com/blog/899224/201708/899224-20170830174311562-549603139.png)

图3 返回Page1页面，输入框数据会被保留

当然，也可以通过动态设置`route.meta`的`keepAlive`属性来实现其他需求，[借鉴一下 vue-router 之 keep-alive，作者：RoamIn](http://www.jianshu.com/p/0b0222954483)这篇博客中的例子：
* 首页是A页面
* B页面跳转到A，A页面需要缓存
* C页面跳转到A，A页面不需要被缓存

思路是在每个路由的 `beforeRouteLeave(to, from, next)` 钩子中设置 `to.meta.keepAlive`：

A路由
```js
{
    path: '/',
    name: 'A',
    component: A,
    meta: {
        keepAlive: true // 需要被缓存
    }
}
....
export default {
    data() {
        return {};
    },
    methods: {},
    beforeRouteLeave(to, from, next) {
         // 设置下一个路由的 meta
        to.meta.keepAlive = true;  // B 跳转到 A 时，让 A 缓存，即不刷新
        next();
    }
};
```
### 2.3 keep-alive生命周期钩子函数：activated、deactivated

使用`<keep-alive>`会将数据保留在内存中，如果要在每次进入页面的时候获取最新的数据，需要在`activated`阶段获取数据，承担原来`created`钩子中获取数据的任务。

## 三、业务使用场景

### 3.1 现有需求

每个项目中都存在许多列表数据展示页面，而且通常包含一些筛选条件以及分页。
![](https://images2015.cnblogs.com/blog/907768/201702/907768-20170225155301210-954801611.png)

并且，点击表格中的某一列需要进行路由跳转进入到详情／添加／编辑页面。

比较好的用户体验就是，从详情／添加／编辑页面返回之后希望列表页保留上次离开时的筛选条件和页码。但是正常情况下路由跳转之后实例已经被销毁。

### 3.2 可选的解决方案

**保留状态数据**
利用`state`存储状态，如筛选的品牌，时间范围，分页。在路由跳转到详情／添加／编辑的时候存储到`state（vuex）`中，如果跳转到其它路由就重置。

缺点：每个列表的筛选条件有差别，还要在每个列表实例创建时读取状态（麻烦），但是也可以使用`computed`属性返回状态。

**保留页面，保留实例**
`keep-alive`在路由跳转时并没有销毁实例。

-------

**参考文章**

* [vue-router 之 keep-alive](https://www.jianshu.com/p/0b0222954483)
* [Vue keep-alive实践总结](http://www.cnblogs.com/sysuhanyf/p/7454530.html)
* [vue.js+vue-router+webpack keep-alive用法](http://www.cnblogs.com/nekoooo/p/6442077.html)



