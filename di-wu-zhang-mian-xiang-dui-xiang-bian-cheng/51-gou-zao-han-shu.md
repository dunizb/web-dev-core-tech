# 第1节 构造函数

构造函数就是初始化一个实例对象，对象的prototype属性是继承一个实例对象。

两个点：

1. 初始化数据；
2. 在JS中给对象添加属性用的，初始化属性值用的

构造函数默认函数首字母大写。

构造函数并没有显示返回任何东西。new 操作符会自动创建给定的类型并返回他们，当调用构造函数时，new会自动创建this对象，且类型就是构造函数类型。

也可以在构造函数中显示调用return.如果返回的值是一个对象，它会代替新创建的对象实例返回。如果返回的值是一个原始类型，它会被忽略，新创建的实例会被返回。    

```js
function Person( name){
   this.name =name;
}
var p1=new Person('John');
```

等同于：

```js
function person(name ){
    Object obj =new Object();
    obj.name =name;
    return obj;
}
var p1= person("John");
```









