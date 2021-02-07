---
title: Web Components
tags:
  - Web Components
categories:
  - 浏览器
date: 2021-02-07 23:56:49
modified: 2021-02-07 23:57:05
---
## 概念
Web Components 是一套不同的技术，允许您创建可重用的定制元素（它们的功能封装在您的代码之外）并且在您的web应用中使用它们。

**由三项主要技术组成：**

 - **Custom elements（自定义元素）**

   一组JavaScript API，允许您定义custom elements及其行为，在用户界面中按需使用它们

 - **Shadow DOM（影子DOM）**

   一组JavaScript API，将封装的“影子”DOM树附加到元素（与主文档DOM分开呈现）并控制其关联的功能，保持元素的功能私有，可以被脚本化和样式化，而不用担心与文档的其他部分发生冲突。

 - **HTML templates（HTML模板）**

    [<template>](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/template) 和 [<slot>](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/slot) 元素使您可以编写不在呈现页面中显示的标记模板。然后它们可以作为自定义元素结构的基础被多次重用

## 使用

### 基本步骤

1. 创建一个类或函数来指定web组件的功能
2. 使用 [`CustomElementRegistry.define()`](https://developer.mozilla.org/zh-CN/docs/Web/API/CustomElementRegistry/define) 方法注册您的新自定义元素 ，并向其传递要定义的`元素名称`、`指定元素功能的类`、以及可选的其所`继承自的元素`。
3. 如果需要的话，使用[`Element.attachShadow()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/attachShadow) 方法将一个shadow DOM附加到自定义元素上。使用通常的DOM方法向shadow DOM中添加子元素、事件监听器等等。
4. 如果需要的话，使用 [` 和[``](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/slot) 定义一个HTML模板。再次使用常规DOM方法克隆模板并将其附加到您的shadow DOM中。
5. 在页面任何您喜欢的位置使用自定义元素，就像使用常规HTML元素那样



## 源代码 [传送门](https://jsbin.com/kutuceriko/edit?html,js,output)

```javascript
// 创建一个类
class UserCard extends HTMLElement {
  super();
  // 创建一个shadow root 隐藏 <user-card>的内部代码， 隔离 内外部的 DOM
  var shadow = this.attachShadow( {mode: 'closed'} ) // mode 模式 open/closed  close拒绝从js外部访问, open与之相反 
	
	var templateElem = document.getElementById('userCardTemplate');
	// 创建 template  fragment
	var content = templateElem.content.cloneNode(true); 
	// 将模板属性添加到 fragment
	content.querySelector('img').setAttribute('src', this.getAttribute('image'));
	content.querySelector('.container>.name').innerText = this.getAttribute('name');
	content.querySelector('.container>.email').innerText = this.getAttribute('email');

	// button 添加事件
	this.$button = shadow.querySelector('button');
 	this.$button.addEventListener('click', () => {
    	alert('点击了 button')
  });

	// 将content DOM 片段 添加到shadow root上
	shadow.appendChild(content);
}

// CustomElementRegistry.define()方法注册新自定义元素 
window.customElements.define('user-card', UserCard);

```
**模板定义需要注意**

​	1、组件的样式应该与代码封装在一起，只对自定义元素生效，不影响外部的全局样式

​	2、`:host`伪类，指代自定义元素本身。

​	3、通过设置 <user-card> 属性 ，来设定 `template` 内容

```html
<!-- test.html 模板定义 -->
<template id="userCardTemplate"> 
  <img class="image">
  <div class="container">
    <p class="name"></p>
    <p class="email"></p>
    <button class="button">Follow John</button>
  </div>
  
   <style>
   :host {
     display: flex;
     align-items: center;
     width: 450px;
     height: 180px;
     background-color: #d4d4d4;
     border: 1px solid #d5d5d5;
     box-shadow: 1px 1px 5px rgba(0, 0, 0, 0.1);
     border-radius: 3px;
     overflow: hidden;
     padding: 10px;
     box-sizing: border-box;
     font-family: 'Poppins', sans-serif;
   }
   .image {
     flex: 0 0 auto;
     width: 160px;
     height: 160px;
     vertical-align: middle;
     border-radius: 5px;
   }
   .container {
     box-sizing: border-box;
     padding: 20px;
     height: 160px;
   }
   .container > .name {
     font-size: 20px;
     font-weight: 600;
     line-height: 1;
     margin: 0;
     margin-bottom: 5px;
   }
   .container > .email {
     font-size: 12px;
     opacity: 0.75;
     line-height: 1;
     margin: 0;
     margin-bottom: 15px;
   }
   .container > .button {
     padding: 10px 25px;
     font-size: 12px;
     border-radius: 5px;
     text-transform: uppercase;
   }
  </style>
</template>


<!-- 使用 -->
<user-card
  image="https://semantic-ui.com/images/avatar2/large/kristy.png"
  name="User Name"
  email="yourmail@some-email.com"
></user-card>
```

## Web Components 封装

用脚本把`<template>`注入网页，JavaScript 脚本跟`<template>`就能封装成一个 JS 文件，成为独立的组件文件。网页只要加载这个脚本，就能使用`<user-card>`组件。

## 兼容性

Chrome 从 M80 版本开始的 WebComponents v0下线计划，到 M88 终于迎来了尾声。Chrome 不再支持Web Components v0，而是用 Web Components v1 取代它，并且 Safari、Firefox、Edge 都支持了这项改动。

![image](https://provenr.obs.cn-north-4.myhuaweicloud.com/blog/2021-2-8%201-28-57.pic_hd)

## 参考

【1】[阮一峰-Web Components 入门实例教程](http://www.ruanyifeng.com/blog/2019/08/web_components.html)

【2】[MDN](https://developer.mozilla.org/zh-CN/docs/Web/Web_Components)

