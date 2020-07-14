---
title: css实用代码片段
p: css/css实用代码片段
date: 2020-07-14 16:13:03
tags: fragment
category: css
---

#### ios 滑动卡顿

```
*{
  -webkit-overflow-scrolling: touch;
}
```
#### 进行抗锯齿渲染
```

-webkit-font-smoothing: antialiased; /*chrome、safari*/

-moz-osx-font-smoothing: grayscale;/*firefox*/”

```

#### 禁止长按选择复制

```

* {
	user-select:none;
	-webkit-touch-callout:none;
	-webkit-user-select:none;
	-khtml-user-select:none;
	-moz-user-select:none;
	-ms-user-select:none;

}
```
#### 1px 线 

```
.weui-cell:before {
    content: " ";
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    height: 1px;
    border-top: 1px solid #D9D9D9;
    color: #D9D9D9;
    -webkit-transform-origin: 0 0;
    transform-origin: 0 0;
    -webkit-transform: scaleY(0.5); // 0.5缩放
    transform: scaleY(0.5);
    left: 15px;
}
```

#### 获取url 参数

```
function getQuery(pa) {
    var url = window.location.href.replace(/#+.*$/, ' ');
    url = decodeURI(url)
    var params = url.substring(url.indexOf("?") + 1, url.length).split("&"),
    	param = {};
    for (var i = 0; i < params.length; i++) {
    	var pos = params[i].indexOf('='), //查找name=value
    		key = params[i].substring(0, pos),
    		val = params[i].substring(pos + 1); //提取value
    	param[key] = val;
    }
    			return (typeof(param[pa]) == "undefined") ? "" : param[pa];
    // return param;
}
```

#### axios 请求

```
1. 利用node qs 转化

import qs from 'qs';
transformRequest:[(data) => {
  return qs.stringify(data)
}]

2. 函数将data对象拼接为字符串 
axios.post('https://apiv1', 
{
    token: localStorage.getItem('token'),
    id: orderid,
}, 
{
transformRequest: [function (data) {
    // 对 data 进行任意转换处理
    let ret = '';
    for (let it in data) {
        ret += encodeURIComponent(it) + '=' + encodeURIComponent(data[
            it]) + '&'
    }
    return ret
}],
headers: {
    "Content-Type": 'application/x-www-form-urlencoded;charset=UTF-8'
}
}).then(function (response) {
    
})
.catch(function (error) {
    
});
```

#### mete标签使用

```
// 禁止缩放
<meta name="viewport" content="width=device-width,initial-scale=1,user-scalable=no"/>

// 忽略将数字变为电话号码
<meta content="telephone=no" name="format-detection"> 

// 忽略识别email
<meta content="email=no" name="format-detection"> 

```

#### 移动端 忽略滚动条

```
element::-webkit-scrollbar{

    display: none;

}

```

#### placeholder元素样式的修改

```
input::-webkit-input-placeholder{color:red;}

input:focus::-webkit-input-placeholder{color:green;}
```


#### 使用图片时，触发baseline 会发现图片下的空白 

```
img{display:block}；

img{vertical-align:top}
```


#### 设置宽度为100% 设置padding  margin 超出屏幕

```
box-sizing:border-box
```

#### 解决移动端 click 300ms 延迟
```
 fastclick.js
 
 import FastClick from 'FastClick'
 /**
 * 点击延迟
 */
FastClick.attach(document.body)
 
 
 zepto.js里的 tap事件。
 
```

#### 浏览器判断 [NPM安装](https://www.npmjs.com/package/device)
```
可使用 Device.js 库
var ua = navigator.userAgent.toLowerCase();
```

#### 移动端布局
1. 长列表    
    页面的地址栏会随着 body 的滚动隐藏起来

2. 伪元素使用
```
    .elem{
        color: #333;
        font-size: .26rem;
        text-align: center;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    .elem:after, .elem:before {
    content: "";
    height: 1px;
    display: block;
    background: #888;
    width: .5rem;
    }
    .elem:after{
      margin-left: .3rem;    
    }
    .elem:before{
      margin-right: .3rem;    
    }
    
```
#### 文本截取

```
单行文本

width:300px;     
overflow: hidden;     
text-overflow:ellipsis;     
white-space: nowrap;   


多行文本

display:-webkit-box;  
overflow:hidden;  
text-overflow:ellipsis;  
-webkit-line-clamp:2;  
-webkit-box-orient:vertical;  

```
####  修改chrome记住密码后自动填充表单的黄色背景 ？
```
  input:-webkit-autofill, textarea:-webkit-autofill, select:-webkit-autofill {
    background-color: rgb(250, 255, 189); /* #FAFFBD; */
    background-image: none;
    color: rgb(0, 0, 0);
  }
```
#### **响应式布局**

1、通过媒体查询方式

```

/*默认为20px*/
html {    
    font-size : 20px;
}
/*判断宽度设置不同的html font-size值去覆盖默认值*/
@media only screen and (min-width: 320px){    
    html {        
        font-size: 10px;
    }
}
@media only screen and (min-width: 375){    
    html {        
        font-size: 16; 
    }
}
@media only screen and (min-width: 414px){    
    html {        
        font-size: 20px; 
    }
}
```
#### loadmore

```
 <div class="loadmore">
    <i class="loading"></i>
    <span class="loadmore__tips">正在加载</span>
  </div>
  
  
  .loadmore {
  width: 65%;
  margin: 1.5em auto;
  line-height: 1.6em;
  font-size: 14px;
  text-align: center;
  .loading{
    width:20px;
    height:20px;
    display:inline-block;
    vertical-align:middle;
    -webkit-animation:weuiLoading 1s steps(12, end) infinite;
            animation:weuiLoading 1s steps(12, end) infinite;
    background:transparent url("data:image/svg+xml;charset=utf8, %3Csvg xmlns='http://www.w3.org/2000/svg' width='120' height='120' viewBox='0 0 100 100'%3E%3Cpath fill='none' d='M0 0h100v100H0z'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23E9E9E9' rx='5' ry='5' transform='translate(0 -30)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23989697' rx='5' ry='5' transform='rotate(30 105.98 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%239B999A' rx='5' ry='5' transform='rotate(60 75.98 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23A3A1A2' rx='5' ry='5' transform='rotate(90 65 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23ABA9AA' rx='5' ry='5' transform='rotate(120 58.66 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23B2B2B2' rx='5' ry='5' transform='rotate(150 54.02 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23BAB8B9' rx='5' ry='5' transform='rotate(180 50 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23C2C0C1' rx='5' ry='5' transform='rotate(-150 45.98 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23CBCBCB' rx='5' ry='5' transform='rotate(-120 41.34 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23D2D2D2' rx='5' ry='5' transform='rotate(-90 35 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23DADADA' rx='5' ry='5' transform='rotate(-60 24.02 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23E2E2E2' rx='5' ry='5' transform='rotate(-30 -5.98 65)'/%3E%3C/svg%3E") no-repeat;
    background-size:100%;
  }
  .loadmore__tips {
    display: inline-block;
    vertical-align: middle;
    color: rgba(0, 0, 0, 0.9);
  }
}

```

#### divider 分割线

```
 <div class="divider divider--hairline divider--content-center">
    <span>暂无数据</span>
 </div>

<style lang="less" scoped>
.divider{
    display: flex;
    align-items: center;
    margin: 16px 0;
    color: #969799;
    font-size: 14px;
    line-height: 24px;
    border-color: #999;
    border-style: solid;
    border-width: 0;
    &::before,
    &::after {
        display: block;
        flex: 1;
        box-sizing: border-box;
        height: 1px;
        border-color: inherit;
        border-style: inherit;
        border-width: 1px 0 0;
    }

    &::before {
        content: '';
    }

    &--hairline {
        &::before,
        &::after {
            transform: scaleY(.5);
        }
    }

    &--dashed {
        border-style: dashed;
    }

    &--content-center,
    &--content-left,
    &--content-right {
        &::before {
            margin-right: 16px;
        }

        &::after {
            margin-left: 16px;
            content: '';
        }
    }

    &--content-left {
        &::before {
            max-width:20%;
        }
    }

    &--content-right {
        &::after {
            max-width: 20%;
        }
    }
}
</style>
 


```