---
title: ES6 对象扩展
tags: javaScript
categary: ES6
date: 2021-01-17 10:37:54
Modified: 2021-01-19 10:40:13
---

# ES6  对象的扩展

## 一、属性的简写
### 1. 属性、方法的简写
```javascript
let foo = 'foo';
const baz = {
		foo,
		method(){return 'hello'}
}

等价于
const baz = {
			foo: foo,
			method: function() {
				console.log(this.foo)
			}
}
```
### 2. 函数的返回值
> 适用模块输出一组变量
```javascript
function getPoint() {
  const x = 1;
  const y = 10;
  return {x, y};
}

// 模块输出
let ms = {};

function getItem (key) {
  return key in ms ? ms[key] : null;
}
function setItem (key, value) {
  ms[key] = value;
}

module.exports = { getItem, setItem};

等价于
module.exports = {
  getItem: getItem,
  setItem: setItem
}
```
### 3. 属性的赋值器（setter）和取值器（getter）
```javascript
const cart = {
  _wheels: 4,

  get wheels () {
    return this._wheels;
  },

  set wheels (value) {
    if (value < this._wheels) {
      throw new Error('数值太小了！');
    }
    this._wheels = value;
  }
}
```
### 4. 简写的对象方法不能用作构造函数，会报错。
```javascript
const obj = {
  f() {
    this.foo = 'bar';
  }
};
new obj.f() // 报错 obj.f is not a constructor

```
## 二、属性名表达式
### 1、定义对象的属性
```javascript
// 方法一 是直接用标识符作为属性名
obj.foo = 'foo';

// 方法二 把表达式放在方括号内。作为对象的属性名
obj['a' + 'bc'] = 123;
```
### 2、表达式还可以用于定义方法名
```javascript
let obj = {
  ['h' + 'ello']() {
    return 'hi';
  }
};
obj.hello() // hi
```
> 注意，属性名表达式与简洁表示法，不能同时使用，会报错。
```javascript
const baz = {bar: 'abc'}
// 报错
const foo = 'bar';
const bar = 'abc';
const baz = { [foo] };

// 正确
const baz = { [foo]: bar};
```
### 3、属性名表达式如果是一个对象，自动将对象转为字符串[object Object]，
```javascript
const keyA = {a: 1};

const myObject = {
  [keyA]: 'valueA',
};

myObject // Object {[object Object]: "valueA"}
```
## 三、对象方法的 name 属性
> 函数的name属性，返回函数名, 对象方法也是函数，name属性返回函数名
```javascript
const person = {
  sayName() {
    console.log('hello!');
  },
};

person.sayName.name  // 'sayName'

```
### 1、取值函数（getter）和存值函数（setter）
```javascript
// 对象的方法使用了取值函数（getter）和存值函数（setter）
const obj = {
  get foo() {},
  set foo(x) {}
};

// name属性 在 该方法的属性的描述对象的get和set属性上面
const descriptor = Object.getOwnPropertyDescriptor(obj, 'foo');
descriptor.get.name // "get foo"
descriptor.set.name // "set foo"
```
### 2、bind方法创造的函数，Function构造函数创造的函数
```javascript
// 构造函数
(new Function()).name // "anonymous"

// bind 绑定
var doSomething = function() {};
doSomething.bind().name // "bound doSomething"
```
### 3、 含有 Symbol 值，name属性返回的是这个 Symbol 值的描述
```javascript
const key1 = Symbol('description');
let obj = {
  [key1]() {},
};
obj[key1].name // "[description]"
```
## 四、super 关键字
this关键字总是指向函数所在的当前对象，super关键字，指向当前`对象的原型对象`
```javascript
const proto = {
  foo: 'foo
};
const obj = {
  foo: 'foo1
  find() {
    return super.foo;
  }
};

Object.setPrototypeOf(obj, proto); // 为obj设置原型对象
obj.find() // "foo"

obj.__proto__ = {foo: 'foo2'};
obj.find() // "foo2"
```
## 五、扩展运算符
### 1、解构赋值
```javascript
let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
x // 1
y // 2
z // { a: 3, b: 4 }
```
### 2、解构赋值浅拷贝
```javascript
let obj = { a: { b: 1 } };
let { ...x } = obj;

// 修改 x 的属性值
x.a.b = 2;
// 也会影响 obj 的值
obj.a.b // 2

// 等同 Object.assgin(x, obj)
```
## 六、属性的遍历
### 1、对象属性的描述对象（Descripter）
> Object.getOwnPropertyDescriptor(obj, prop)  // 获取
> Object.defineProperty(obj, prop, descriptor)  // 设置
```javascript
let obj = { foo: 123 };
Object.getOwnPropertyDescriptor(obj, 'foo')
//  {
//    value: 123, 
//    writable: true, 
//    enumerable: true,
//    configurable: true 
//  }
```
descriptor 是一个对象，对象里的属性描述符有两种类型：`数据描述符`和`存取描述符`
**数据描述符**是一个具有值的属性，该值可能是可写的，也可能不是可写的。
**存取描述符**是由`getter-setter`函数对描述的属性。描述符必须是这两种形式之一

描述符同时有(value或writable) 和 (get或set)关键字，将会产生一个异常

```javascript

Object.defineProperty(obj, "newDataProperty", {
    value: 101, // 设置值
    writable: true, // 值可以被修改
    enumerable: true, // 可以被枚举
    configurable: true // 属性可以被删除、特性可以修改
})

var obj = {newDataProperty: 0};
Object.defineProperty(obj, "newDataProperty", {
    get: function(){
    		return this.newDataProperty
    }, // 设置值
    set: function(value){
    	this.newDataProperty = value
    }, // 值可以被修改
    enumerable: true, // 可以被枚举
    configurable: true // 属性可以被删除、特性可以修改
})
```

数据描述符和存取描述符均具有一下可选键值（特性）：

- **configurable**: 默认值为 false。如果为 false，则任何尝试删除目标属性或修改属性以下特性（writable, configurable, enumerable）的行为将被无效化，。
- **enumerable**:默认值为 false。 是否能枚举。也就是是否能被（for...in 或 Object.keys ）方法遍历。
- **writable**: 默认为 false。是否能修改值。
- **value**: 默认为 undefined。该属性的具体值是多少。

存取描述符：

- **get**: 目标属性被访问就会调回此方法，并将此方法的运算结果返回用户。默认为 undefined
- **set**: 目标属性被赋值，就会调回此方法。默认为 undefined

### 2、5 种方法可以遍历对象的属性。

- for...in：循环遍历对象自身的和继承的可枚举属性（不含 Symbol 属性）
- Object.keys(obj)：返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含 Symbol 属性）的键名
- Object.getOwnPropertyNames(obj)：回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名
- Object.getOwnPropertySymbols(obj)：返回一个数组，包含对象自身的所有 Symbol 属性的键名
- Reflect.ownKeys(obj)：返回一个数组，包含对象自身的（不含继承的）所有键名，不管键名是 Symbol 或字符串，也不管是否可枚举

以上的 5 种方法遍历对象的键名，都遵守同样的属性遍历的次序规则。

- 首先遍历所有`数值键`，按照数值`升序排列`。
- 其次遍历所有`字符串键`，按照加入时间`升序排列`。
- 最后遍历所有`Symbol 键`，按照加入时间`升序排列`。

## 七、对象新增的方法

- Object.is()

  严格判断两个值是否相等，与严格比较运算符（===）的行为基本一致，

  区别在于 `+0` 不等于 `-0` ，`NaN`等与自身

  ```javascript
  +0 === -0 //true
  NaN === NaN // false
  
  Object.is(+0, -0) // false
  Object.is(NaN, NaN) // true
  ```

- Object.assign() // 忽略`enumerable`为`false`的属性，只拷贝对象自身的可枚举的属性。`浅拷贝`，遇到`同名属性会进行替换`

  ```javascript
  const target = { a: 1, b: 1 };
  
  const source1 = { b: 2, c: 2 };
  const source2 = { c: 3 };
  
  Object.assgin(target, source1, source2)
  ```

- Object.getOwnPropertyDescriptors()

- Object.setPrototypeOf()，Object.getPrototypeOf()

- Object.keys()，Object.values()，Object.entries()

  ```javascript
  const obj = { foo: 'bar', baz: 42 };
  
  Object.keys(obj)
  // ["foo", "baz"]
  
  Object.values(obj)
  // ["bar", 42]
  
  Object.entries(obj)
  // [ ["foo", "bar"], ["baz", 42] ]
  ```

  

- Object.fromEntries() // 用于将一个键值对数组转为对象

  ```javascript
  Object.fromEntries([
    ['foo', 'bar'],
    ['baz', 42]
  ])
  // { foo: "bar", baz: 42 }
  ```

## 八、对象操作符

### 1. ? 操作符

####  	`三元运算符 < ? >` , 需要三个操作数 ,一个条件为真时要执行的表达式，以及一个条件为假时要执行的表达式。

	三元操作符 赋值操作

```javascriptjavascript
var budget =0
var transportion = (budget >0)? Train : Walking
console.log(transportion) // Walking

// 
function nullishAssignment(x, y) {
  return (x == null || x == undefined) ? y : x
} 
```



### 2. ?. 操作符

#### 	`可选的链式操作符 < ?.>` 读取深嵌在对象链中的属性值，而不必显式验证每个引用。当一个引用为空时，表达式停止计算并返回一个未定义的值。	

```javascript
var travelPlans = {
	destination: 'DO',
  monday: {
		Location: 'National Mall',
    budget: 200
  }
}
const tuesdayPlans = travelPlans.tuesday?.location
console.log(tuesdayPlans)

```

### 3. ?? 操作符

#### `零合并操作符（nullish coalescing）< ?? >` 如果第一个参数不是null/undefined，这个运算符将返回第一个参数，否则，它将返回第二个参数。

```javascript
var x = null;
var y = 5;
var s = x ?? y  // s => 5

// 与 OR 逻辑的区别
s = 0 || y // s => 5
s = null || y // s => 5

```

### 4. ??= 操作符

#### 逻辑空值赋值运算符< ??= > 只有在当前值为空或未定义的情况下才会赋一个新的值

```javascript
var x = null;
var y = 5;
x ??= y  // x => 5

等价于 x ?? (x = y)

// 与函数参数默认值的区别
function gamesettingswithnullish(options){
  options.gamespeed ??=1;
  options.gamediff ??= 'easy';
  return options
}
  
function gamesettingswithdefaultparams(gamespeed=l, gamediff="easy"){
  return {gamespeed, gamediff}
}
gamesettingswithnullish(null) // {gamespeed: 1, gamediff: 'easy'}
gamesettingswithdefaultparams(null) // {gamespeed: null, gamediff: null}
```