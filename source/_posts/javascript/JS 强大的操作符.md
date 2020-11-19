---
title: 强大JavaScript操作符
tags: javaScript
categary: javaScript
date: 2019-07-25 14:57:01
---

## 1. ? 操作符

####  	`三元运算符 < ? >` , 需要三个操作数 ,一个条件为真时要执行的表达式，以及一个条件为假时要执行的表达式。

​	三元操作符 赋值操作

```javascript
var budget =0
var transportion = (budget >0)? Train : Walking
console.log(transportion) // Walking

// 
function nullishAssignment(x, y) {
  return (x == null || x == undefined) ? y : x
} 
```



## 2. ?. 操作符

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

## 3. ?? 操作符

#### `零合并操作符（nullish coalescing）< ?? >` 如果第一个参数不是null/undefined，这个运算符将返回第一个参数，否则，它将返回第二个参数。

```javascript
var x = null;
var y = 5;
var s = x ?? y  // s => 5

// 与 OR 逻辑的区别
s = 0 || y // s => 5
s = null || y // s => 5

```

## 4. ??= 操作符

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

