---
title: JavaScript数组扁平化
tags:
  - flat
  - JavaScript
date: 2019-12-04 20:59:53
---
## JavaScript 数组扁平化

###一、ES6 语法 flat 函数

```
var newArray = arr.flat([depth])
return: 一个包含将数组与子数组中所有元素的新数组。
```
#### options 
depth 可选
指定要提取嵌套数组的结构深度，默认值为 1。

### reduce + concat + isArray 实现

```
var arr2 = [{a:1}, [{id:2}, [6]]];

function flat(array, deep = 0) {
    return deep > 0 ?  array.reduce((acc, item) => {
        if (Array.isArray(item)){
            return acc.concat(flat(item, deep - 1))
        } else {
            return acc.concat(item)
        }
    }, []) : array.slice()
}

let s = flat(arr2, 2);
console.log(s)
console.log(arr2)

```