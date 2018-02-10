# 附录1：Vue.js知识点整理

## 1. vue.js 计算属性与$watch的区别?

计算属性：用于简单场合，比如计算，合并字符串等；  
watch属性：用于耗时的，可以异步的获取远程服务上的数据这样的操作。

> 来自知乎：[https://www.zhihu.com/question/55846720](https://www.zhihu.com/question/55846720)

## 2. Vue2.0 v-for 中 :key 到底有什么用？

简单的说就是为了提高遍历性能。

当 `Vue.js` 用 `v-for` 正在更新已渲染过的元素列表时，它默认用“就地复用”策略。如果数据项的顺序被改变，`Vue` 将不会移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素。

`2.2.0+` 的版本里，当在组件中使用 `v-for` 时，`key` 现在是必须的。

## 3. 数组更新检测不能检测哪些情况？

**1. 非变异方法**  
例如：`filter()`, `concat()` 和 `slice()` 。这些不会改变原始数组，但总是返回一个新数组。当使用非变异方法时，可以用新数组替换旧数组。

**2. 不能检测以下变动的数组：**

* 当你利用索引直接设置一个项时，例如：`vm.items[indexOfItem] = newValue`
* 当你修改数组的长度时，例如：`vm.items.length = newLength`

为了解决第一类问题，以下两种方式都可以实现和 `vm.items[indexOfItem] = newValue` 相同的效果，同时也将触发状态更新：

```js
// Vue.set
Vue.set(example1.items, indexOfItem, newValue)

// Array.prototype.splice
example1.items.splice(indexOfItem, 1, newValue)
```

为了解决第二类问题，你可以使用 `splice`：

```js
example1.items.splice(newLength)
```

**3. 不能检测对象属性的添加或删除**  
解决方法：`vm.$set`  
例如：你可以添加一个新的 age 属性到嵌套的 userProfile 对象：

```js
vm.$set(vm.userProfile, 'age', 27)
```

有时你可能需要为已有对象赋予多个新属性，比如使用 `Object.assign()` 或 `_.extend()`。在这种情况下，你应该用两个对象的属性创建一个新的对象。所以，如果你想添加新的响应式属性，不要像这样：

```js
Object.assign(vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

你应该这样做：

```js
vm.userProfile = Object.assign({}, vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

## 4. v-for with v-if 的优先级谁高？

当它们处于同一节点，`v-for` 的优先级比 `v-if` 更高，这意味着 `v-if` 将分别重复运行于每个 `v-for` 循环中。

## 5. 事件修饰符有哪些？
- .stop，阻止事件冒泡
- .prevent，提交事件不再重载页面
- .capture，添加事件监听器时使用事件捕获模式，即元素自身触发的事件先在此处处理，然后才交由内部元素进行处理
- .self，只当在 `event.target` 是当前元素自身时触发处理函数，即事件不是从内部元素触发的
- .once（2.1.4），点击事件将只会触发一次
- .passive（2.3.0），告诉浏览器你不想阻止事件的默认行为，尤其能够提升移动端的性能。
- .native，在某个组件的根元素上监听一个原生事件
- .sync（2.3.0）作为一个编译时的语法糖存在，它会被扩展为一个自动更新父组件属性的 `v-on` 监听器。当子组件需要更新 foo 的值时，它需要显式地触发一个更新事件`update`。