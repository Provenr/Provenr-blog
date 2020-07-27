---
title: node中间件body-parser
date: 2020-07-27 20:30:33
tags: body-parser
category: node
---
## 前言
body-parser是非常常用的一个express中间件，作用是对post请求的请求体进行解析
### 常用配置
```
router.use(bodyParser.json());
router.use(bodyParser.urlencoded({ extended: false })); // 
```

### bodyParser.json([Options])
> ##### 解析JSON类型数据

### bodyParser.urlencoded([Options])
> ##### 解析表单类型
 extended 参数 表示使用哪种库解析，默认值 true
 
 ```
 // -----------------------qs library-------------------------------------
// extended => true（default）, 使用qs 
// qs只会解析5层嵌套  nested object
// 对于数组，qs最大只会解析20个索引，超出的部分将会以键值对的形式解析

// info[name]=henry&info[age]=30&hobby[1]=sport&hobby[2]=coding
// => {info:{"name":"henry","age":"30"},hobby:["sport","coding"]}
// "?a=b"  => { a: 'b' }

// --------------------------querystring library----------------------------------
// extended => false,
// 使用querystring  querystring并不能正确的解析复杂对象（多级嵌套）

// info[name]=henry&info[age]=30&hobby[1]=sport&hobby[2]=coding
//  => {info[name]:"henry",info[age]:"30",hobby[1]:"sport",hobby[2]:"coding"}
// "?a=b"  => { '?a': 'b' }
 
 ```

## 参考

【1】[body-parser](https://github.com/expressjs/body-parser)

【2】[Express中间件body-Parser](https://www.cnblogs.com/chyingp/p/nodejs-learning-express-body-parser.html)