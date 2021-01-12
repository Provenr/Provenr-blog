---
title: js 对象类型判断
tags: javaScript
categary: javaScript
date: 2020-12-17 10:56:28
---

## 一、 对象判断

###  	1、普通对象判断

1. 	#### typeof

   ```
   console.log(typeof '123') // string
   console.log(typeof 123) // number
   console.log(typeof false) // boolean
   console.log(typeof undefined) // undefined
   console.log(typeof null) //object
   console.log(typeof Symbol()) //symbol
   console.log(typeof new Array) // object
   console.log(typeof {}) // object
   console.log(typeof (()=>'')) // function
   ```

   ⚠️null 、函数 、对象实例（array）返回都是 object

2. #### toString

   ```javascript
   Object.prototype.toString.call({}) ;   // [object Object]
   ```

###  	2、 空对象判断
####  Ⅰ. 通过JSON自带的stringify()方法来判断:

```javascript
if (JSON.stringify({a: 1}) === '{}') {
    return false // 如果为空,返回false
}
```
⚠️**注意**：JSON.stringify() 序列化 [传送门](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)

- 布尔值、数字、字符串的包装对象在序列化过程中会自动转换成对应的原始值。

  ```javascript
  JSON.stringify({});                        // '{}'
    JSON.stringify(true);                      // 'true'
    JSON.stringify("foo");                     // '"foo"'
    JSON.stringify([1, "false", false]);       // '[1,"false",false]'
    JSON.stringify({ x: 5 });                  // '{"x":5}'
  ```

- undefined、任意的函数以及 symbol 值，在序列化过程中会被忽略（出现在非数组对象的属性值中时）或者被转换成 null（出现在数组中时）。函数、undefined 被单独转换时，会返回 undefined，如JSON.stringify(function(){}) or JSON.stringify(undefined).

  ```javascript
  JSON.stringify({x: undefined, y: function(){}, z: Symbol("")});
  // '{}'
  ```

- 对包含循环引用的对象（对象之间相互引用，形成无限循环）执行此方法，会抛出错误。

- NaN 和 Infinity 格式的数值及 null 都会被当做 null。

  ```javascript
  JSON.stringify({x: null, y: NaN, z: Infinity});
  ```

  👉 特殊条件下，使用 JSON.stringify()  去判断 空对象 不成立

  ```javascript
  JSON.stringify({a: undefined}) === '{}' // true
  JSON.stringify({a: function(){}}) === '{}' // true
  ```

####  Ⅱ. for...in...遍历属性，为真则为“非空数组”；否则为“空数组”

```
function isEmptyObj(obj){
  for (var i in obj) { // 如果不为空，则会执行到这一步，返回true
      return false
  }
  return true
}
```

#### Ⅲ. ES6新增的方法Object.keys():

```javascript
Object.keys({}) // 返回对象自身可枚举属性组成的数组
if (Object.keys(object).length === 0) {
    return false // 如果为空,返回false
}

// 使用 Reflect.OwnKeys()
const isEmpty = obj => Reflect.ownKeys(obj).length === 0 && obj.constructor === Object;

```



## 二、 数组判断

### 1. instanceof 检测原型

```javascript
[] instanceof Array
```

### 2. toString()

toString是 Object 的原型方法，调用该方法，默认返回当前对象的 [[Class]] 。这是一个内部属性，其格式为 [object Xxx] ，其中 Xxx 就是对象的类型。

```javascript
Object.prototype.toString.call([]) === '[object Array]'
```

🌈  JS类型判断 补充

```javascript
Object.prototype.toString.call('') ;   // [object String]
Object.prototype.toString.call(1) ;    // [object Number]
Object.prototype.toString.call(true) ; // [object Boolean]
Object.prototype.toString.call(Symbol()); //[object Symbol]
Object.prototype.toString.call(undefined) ; // [object Undefined]
Object.prototype.toString.call(null) ; // [object Null]
Object.prototype.toString.call(newFunction()) ; // [object Function]
Object.prototype.toString.call(newDate()) ; // [object Date]
Object.prototype.toString.call([]) ; // [object Array]
Object.prototype.toString.call(newRegExp()) ; // [object RegExp]
Object.prototype.toString.call(newError()) ; // [object Error]
Object.prototype.toString.call(document) ; // [object HTMLDocument]
Object.prototype.toString.call(window) ; //[object Window] 

// 正则匹配 类型的字符串
function type(obj) {
  return Object.prototype.toString.call(obj).match(/\[object (.*)\]/)[1]
}
type([]) // 'Array'
```

###3.ES6新增 isArray方法

```
Array.isArray([]) // true
```

## 三、总结

1. 如果只是想要判断某一个对象是不是某一个构造函数的实例，使用instanceof

2. 如果希望区分内置对象和自定义对象，可以使用Object.prototype.toString.call

3. 如果希望判断是不是基础类型的或者对象，或者是函数，可以使用typeof

4. 如果希望得到自定义对象的具体的类型的字符串，可以使用constructor

