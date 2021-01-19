---
title: ES6 Reflect
tags: javaScript
categary: ES6
date: 2021-01-19 10:37:54
Modified: 2021-01-19 10:40:13
---

## 一、Reflect 介绍

`Reflect`对象设计目的 ：

 1、将`Object`对象明显属于语言内部的方法（Object.defineProperty）剥离出来， 放到 Reflect 对象上（现阶段，某些方法同时在`Object`和`Reflect`对象上部署，未来的新方法将只部署在`Reflect`对象上）

2、让某些`Object`方法的返回结果合理，比如 `Object.defineProperty(obj, name, desc)`，无法定义属性时，会抛出错误，而`Reflect.defineProperty(obj, name, decs)` 返回 false

```javascript
// 老写法 
try{
	Object.defineProperty(obj, name, desc)
	// success
} catch(e) {
	// failure
}

// 新写法
if(Reflect.defineProperty(obj, name, desc)){
	// success
} else {
	// failure
}
```

3、将`Object`命令式式操作 改为 Reflect 函数行为

```javascript
let foo = {bar: 1}

// 老写法
'bar' in foo // true
delete foo.bar

// 新写法
Reflect.has(foo, 'bar') // true

```

4、`Reflect`对象的方法与`Proxy`对象的方法一一对应，只要是`Proxy`对象的方法，就能在`Reflect`对象上找到对应的方法。

```javascript
var obj = new Proxy({}, {
	get: function(target, key, receiver){
		return Reflect.get(target, key, receiver)
	},
	set: function(target, key, value, receiver){
		return Reflect.set(target, key, value, receiver)
	}
})
```

## 二、`Reflect`对象的 13 个静态方法。

- Reflect.apply(target, thisArg, args)

  > 等同于`Function.prototype.apply.call(func, thisArg, args)`，用于绑定`this`对象后执行给定函数。

  ```javascript
  const ages = [11, 33, 12, 54, 18, 96];
  
  // 旧写法
  const youngest = Math.min.apply(Math, ages);
  const oldest = Math.max.apply(Math, ages);
  const type = Object.prototype.toString.call(youngest);
  
  // 新写法
  const youngest = Reflect.apply(Math.min, Math, ages);
  const oldest = Reflect.apply(Math.max, Math, ages);
  const type = Reflect.apply(Object.prototype.toString, youngest, []);
  ```

  

- Reflect.construct(target, args)  

  > 等同于`new target(...args)`, 是一种不使用`new`，来调用构造函数的方法。

  ```javascript
  function Greeting(name) {
    this.name = name;
  }
  
  // new 的写法
  const instance = new Greeting('张三');
  
  // Reflect.construct 的写法
  const instance = Reflect.construct(Greeting, ['张三']);
  ```

  

- Reflect.get(target, name, receiver) 

  > 没有该属性，则返回`undefined`。

- Reflect.set(target, name, value, receiver)

  > 设置了赋值函数，则赋值函数的`this`绑定`receiver`。

  ```javascript
  var myObject = {
    foo: 4,
    set bar(value) {
      return this.foo = value;
    },
  };
  
  var myReceiverObject = {
    foo: 0,
  };
  
  Reflect.set(myObject, 'bar', 1, myReceiverObject); // set 中 this 指向myReceiverObject，改变的是 myReceiverObject 的 foo
  myObject.foo // 4
  myReceiverObject.foo // 1
  ```

- Reflect.defineProperty(target, name, desc)

- Reflect.deleteProperty(target, name) // 对应 `delete` 操作符

- Reflect.has(target, name)  // 对应 `in` 操作符

- Reflect.ownKeys(target)

  > 返回对象的所有属性, 包含Symbol 

- Reflect.isExtensible(target)

  > 表示当前对象是否可扩展。返回一个布尔值，

- Reflect.preventExtensions(target)

  > 让一个对象变为不可扩展。返回一个布尔值，表示是否操作成功。

- Reflect.getOwnPropertyDescriptor(target, name)

- Reflect.getPrototypeOf(target) 

  > 读取对象的`__proto__`属性

- Reflect.setPrototypeOf(target, prototype)

  > 用于设置目标对象的原型（prototype）,返回 布尔值 ，表示是否设置成功。
  >
  > 第一个参数是`undefined`或`null`。`Object.setPrototypeOf`和`Reflect.setPrototypeOf`都会报错。

  

**大部分与`Object`对象的同名方法的作用都是相同的，而且它与`Proxy`对象的方法是一一对应的**


## 三、总结

1、Reflect 静态方发的第一个参数是对象，否则会报错。

```javascript
Reflect.set(1, 'foo', {}) // 报错
Reflect.set(false, 'foo', {}) // 报错
```

2、`Proxy.set`的`receiver`参数总是指向当前的 `Proxy`实例，`Reflect.set`一旦传入`receiver`，就会将属性赋值到`receiver`上面（即`obj`）

````javascript
let p = {
  a: 'a'
};

let handler = {
  set(target, key, value, receiver) {
    console.log('set');
    // receiver => Proxy:{a: "a"}
    Reflect.set(target, key, value, receiver)
  },
  defineProperty(target, key, attribute) {
    console.log('defineProperty');
    Reflect.defineProperty(target, key, attribute);
  }
};

let obj = new Proxy(p, handler);
obj.a = 'A';
// set
// defineProperty
````

