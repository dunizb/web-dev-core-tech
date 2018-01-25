# 第9节 深拷贝与浅拷贝

## 一、什么是深拷贝，什么是浅拷贝？

如果拷贝的时候，将数据的所有结构都拷贝一份，那么**数据在内存中独立就是深拷贝（内存完全隔离）**，比如这样：

```js
var car = { name: '法拉利' };
var p = { name: '张三', age: 19, car: car };

var pCopy = {};
pCopy.name = p.name;
pCopy.age = p.age;
pCopy.car = {};
pCopy.car.name = p.car.name;
```

如果拷贝的时候，**只正对当前对象的属性进行拷贝，而属性是引用类型这个不考虑，那么就是浅拷贝。**

所谓的拷贝，就是复制一份，指将对象数据复制一份

深拷贝和浅拷贝的示意图大致如下：

![](http://www.haorooms.com/uploads/images/copysq.png)

浅拷贝只复制指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存。但深拷贝会另外创造一个一模一样的对象，新对象跟原对象不共享内存，修改新对象不会改到原对象。

## 二、浅拷贝实现方式

### 2.1 简单的引用复制

拷贝原对象的引用，这是最简单的浅拷贝。

```js
// 对象
var o1 = {a: 1};
var o2 = o1;

console.log(o1 === o2);  // =>true
o2.a = 2; 
console.log(o1.a); // => 2

// 数组
var o1 = [1,2,3];
var o2 = o1;

console.log(o1 === o2); // => true
o2.push(4);
console.log(o1); // => [1,2,3,4]
```

拷贝原对象的实例，但是对其内部的引用类型值，拷贝的是其引用

### 2.2 Object.assign\(\)

Object.assign\(\) 方法可以把任意多个的源对象自身的可枚举属性拷贝给目标对象，然后返回目标对象。

```js
var x = {
  a: 1,
  b: { f: { g: 1 } },
  c: [ 1, 2, 3 ]
};
var y = Object.assign({}, x);
console.log(y.b.f === x.b.f);     // true
```

### 2.3 自己实现

我们也可以自己写一个简单浅拷贝函数来加深对浅拷贝的理解

```js
// 浅拷贝实现，仅供参考
function shallowClone(source) {
    if (!source || typeof source !== 'object') {
        throw new Error('error arguments');
    }
    var targetObj = source.constructor === Array ? [] : {};
    for (var keys in source) {
        if (source.hasOwnProperty(keys)) {
            targetObj[keys] = source[keys];
        }
    }
    return targetObj;
}
```

## 三、深拷贝实现方式

### 3.1 jQuery.extend\(\)

说到深拷贝，第一想到的就是jQuery.extend\(\)方法，下面我们简单看下jQuery.extend\(\)的使用。

jQuery.extend\( \[deep \], target, object1 \[, objectN \] \)，其中deep为Boolean类型，如果是true，则进行深拷贝。

```js
var target = {a: 1, b: 1};
var copy1 = {a: 2, b: 2, c: {ca: 21, cb: 22, cc: 23}};
var copy2 = {c: {ca: 31, cb: 32, cd: 34}};
var result = $.extend(true, target, copy1, copy2);   // 进行深拷贝
console.log(target);    // {a: 2, b: 2, c: {ca: 31, cb: 32, cc: 23, cd: 34}}
```

```js
var target = {a: 1, b: 1};
var copy1 = {a: 2, b: 2, c: {ca: 21, cb: 22, cc: 23}};
var copy2 = {c: {ca: 31, cb: 32, cd: 34}};
var result = $.extend(target, copy1, copy2);   // 不进行深拷贝
console.log(target);    // {a: 1, b: 1, c: {ca: 31, cb: 32, cd:34}}
```

通过上面的对比可以看出，当使用extend\(\)进行深拷贝的时候，对象的所有属性都添加到target中了。

### 3.2 Array.prototype.slice\(\)和Array.prototype.concat\(\)

Array的slice和concat方法不修改原数组，只会返回一个浅复制了原数组中的元素的一个新数组。之所以把它放在深拷贝里，是因为它看起来像是深拷贝。而实际上它是浅拷贝。原数组的元素会按照下述规则拷贝：

* 如果该元素是个对象引用 （不是实际的对象），slice 会拷贝这个对象引用到新的数组里。两个对象引用都引用了同一个对象。如果被引用的对象发生改变，则新的和原来的数组中的这个元素也会发生改变。
* 对于字符串、数字及布尔值来说（不是 String、Number 或者 Boolean 对象），slice 会拷贝这些值到新的数组里。在别的数组里修改这些字符串或数字或是布尔值，将不会影响另一个数组。

如果向两个数组任一中添加了新元素，则另一个不会受到影响。例子如下：

```js
var array = [1,2,3]; 
var array_shallow = array; 
var array_concat = array.concat(); 
var array_slice = array.slice(0); 
console.log(array === array_shallow); //true 
console.log(array === array_slice); //false，“看起来”像深拷贝
console.log(array === array_concat); //false，“看起来”像深拷贝
```

可以看出，concat和slice返回的不同的数组实例，这与直接的引用复制是不同的。而从另一个例子可以看出Array的concat和slice并不是真正的深复制，数组中的对象元素\(Object,Array等\)只是复制了引用。如下：

```js
var array = [1, [1,2,3], {name:"array"}]; 
var array_concat = array.concat();
var array_slice = array.slice(0);
array_concat[1][0] = 5;  //改变array_concat中数组元素的值 
console.log(array[1]); //[5,2,3] 
console.log(array_slice[1]); //[5,2,3] 
array_slice[2].name = "array_slice"; //改变array_slice中对象元素的值 
console.log(array[2].name); //array_slice
console.log(array_concat[2].name); //array_slice
```

### 3.3 JSON对象的parse和stringify

JSON对象是ES5中引入的新的类型（支持的浏览器为IE8+），JSON对象parse方法可以将JSON字符串反序列化成JS对象，stringify方法可以将JS对象序列化成JSON字符串，借助这两个方法，也可以实现对象的深拷贝。

```js
//例1
var source = { name:"source", child:{ name:"child" } } 
var target = JSON.parse(JSON.stringify(source));
target.name = "target";  //改变target的name属性
console.log(source.name); //source 
console.log(target.name); //target
target.child.name = "target child"; //改变target的child 
console.log(source.child.name); //child 
console.log(target.child.name); //target child
//例2
var source = { name:function(){console.log(1);}, child:{ name:"child" } } 
var target = JSON.parse(JSON.stringify(source));
console.log(target.name); //undefined
//例3
var source = { name:function(){console.log(1);}, child:new RegExp("e") }
var target = JSON.parse(JSON.stringify(source));
console.log(target.name); //undefined
console.log(target.child); //Object {}
```

这种方法使用较为简单，可以满足基本的深拷贝需求，而且能够处理JSON格式能表示的所有数据类型，**但是对于正则表达式类型、函数类型等无法进行深拷贝\(而且会直接丢失相应的值\)。**还有一点不好的地方是它会抛弃对象的constructor。也就是深拷贝之后，不管这个对象原来的构造函数是什么，在深拷贝之后都会变成Object。**同时如果对象中存在循环引用的情况也无法正确处理。**

### 3.4 利用Object.create\(\)自己实现

直接使用var newObj = Object.create\(oldObj\)，可以达到深拷贝的效果。

```js
function deepClone(initalObj, finalObj) {    
  var obj = finalObj || {};    
  for (var i in initalObj) {        
    var prop = initalObj[i];        // 避免相互引用对象导致死循环，如initalObj.a = initalObj的情况
    if(prop === obj) {            
      continue;
    }        
    if (typeof prop === 'object') {
      obj[i] = (prop.constructor === Array) ? [] : Object.create(prop);
    } else {
      obj[i] = prop;
    }
  }    
  return obj;
}
```

### 3.5 lodash

另外一个很热门的函数库lodash，也有提供\_.cloneDeep用来做 Deep Copy。

```js
var _ = require('lodash');
var obj1 = {
    a: 1,
    b: { f: { g: 1 } },
    c: [1, 2, 3]
};
var obj2 = _.cloneDeep(obj1);
console.log(obj1.b.f === obj2.b.f);
// false
```







---

**参考文章**

* [javaScript中浅拷贝和深拷贝的实现](https://github.com/wengjq/Blog/issues/3)
* [深拷贝与浅拷贝的实现（一）](http://www.alloyteam.com/2017/08/12978/)
* [什么是js深拷贝和浅拷贝及其实现方式](http://www.haorooms.com/post/js_copy_sq)



