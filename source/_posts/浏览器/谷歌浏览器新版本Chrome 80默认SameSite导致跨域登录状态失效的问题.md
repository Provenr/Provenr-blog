---
title: 谷歌浏览器新版本Chrome 80默认SameSite导致跨域登录状态失效的问题
tags:
  - cookie 跨域
  - SameSite
categories:
  - 浏览器
abbrlink: 8843278e
date: 2020-10-13 14:33:11
modified: 2020-10-13 14:33:17
---

## 一、问题描述

1. 浏览器`cookie`设置正常，域名下`cookie`也有值，**但就是带不过去后台**，出现跨域问题。
2. 遂检查`Nignx`配置，`CORS`配置正常

3. 定位Chrome 80版本的 [SameSite](https://www.chromestatus.com/feature/5088147346030592) 的问题

##  二、什么是SameSite

`SameSite ` 是针对某些类型的跨站点请求伪造（CSRF）攻击的相当强大的防御

> 在Chrome 80版本中，Chrome会将没有声明`SameSite`值的`cookie`默认设置为`SameSite=Lax`。只有采用`SameSite=None; Secure`设置的`cookie`可以从外部访问，前提是通过安全连接（即`HTTPS`）访问。

`SameSite`是`Cookie`中的一个属性，它用来标明这个 `cookie` 是个“同站 `cookie`”，“同站 `cookie`” 只能作为`第一方cookie`，不能作为`第三方cookie`，因此可以限制第三方`Cookie`，解决`CSRF`的问题。不知道`CSRF`的看着[这个](https://blog.csdn.net/sinat_36521655/article/details/84189698)。早在Chrome 51中就引入了这一属性，但是不会默认设置，所以相安无事。

`SameSite`总共有三个值：`Strict`、`Lax`、`None`。以下内容引自[阮一峰博客](http://www.ruanyifeng.com/blog/2019/09/cookie-samesite.html)

#### 1. Strict

`Strict`最为严格，完全禁止第三方 `Cookie`，跨站点时，任何情况下都不会发送 `Cookie`。换言之，只有当前网页的 `URL` 与请求目标一致，才会带上 `Cookie`。

```javascript
Set-Cookie: CookieName=CookieValue; SameSite=Strict;
```

这个规则过于严格，可能造成非常不好的用户体验。比如像本人当前遇到的现象，`cookie`带不过，等于一直没有登录状态，就会回到登录页。

#### 2. Lax(默认值)

`Lax`规则稍稍放宽，大多数情况也是不发送第三方 `Cookie`，但是导航到目标网址的 `Get` 请求除外。Chrome 80之后默认设置为该值。

```javascript
Set-Cookie: CookieName=CookieValue; SameSite=Lax;
```

导航到目标网址的 `GET` 请求，只包括三种情况：链接，预加载请求，`GET` 表单。详见下表

| 请求类型    | 示例                             | 正常情况    | Lax         |
| ----------- | -------------------------------- | ----------- | ----------- |
| 链接        | <a href="…"></a>                 | 发送 Cookie | 发送 Cookie |
| 预加载      | <link rel=“prerender” href="…"/> | 发送 Cookie | 发送 Cookie |
| `GET` 表单  | <form method=“GET” action="…">   | 发送 Cookie | 发送 Cookie |
| `POST` 表单 | <form method=“POST” action="…">  | 发送 Cookie | 不发送      |
| `iframe`    | <iframe src="…"></iframe>        | 发送 Cookie | 不发送      |
| `AJAX`      | $.get("…")                       | 发送 Cookie | 不发送      |
| `Image`     | <img src="…">                    | 发送 Cookie | 不发送      |

设置了`Strict`或`Lax`以后，基本就杜绝了`CSRF`攻击。

#### 3. None

浏览器会在同站请求、跨站请求下继续发送cookies，不区分大小写。网站可以选择显式关闭 `SameSite` 属性，将其设为 `None` ，**同时必须**设置 `Secure` 属性（表示`Cookie` 只能通过 `HTTPS` 协议发送，`HTTP`协议不会发送），否则无效。

下面为无效响应头：

```javascript
Set-Cookie: widget_session=abc123; SameSite=None
```

下面为有效响应头：

```javascript
Set-Cookie: widget_session=abc123; SameSite=None; Secure
```

## 三、解决办法

### 1、服务端

显示关闭`SameSite`属性，按照上述有效响应头设置登录接口的响应头即可

```javascript
response.setHeader(name: "Set-Cookie", value: "_u=xxxx; Path=/Login; SameSite=None; Secure")
```

### 2、 浏览器禁用

- 地址栏输入：`chrome://flags/`
- 找到`SameSite by default cookies`和`Cookies without SameSite must be secure`
- 将上面两项设置为 `Disable`

## 四、补充

### 1. 什么是第三方cookie 

第三方Cookie：由当前`a.com`页面发起的请求的 `URL` 不一定也是 `a.com` 上的，可能有 `b.com` 的，也可能有 `c.com` 的。我们把发送给 `a.com` 上的请求叫做第一方请求（`first-party request`），发送给 `b.com` 和 `c.com` 等的请求叫做第三方请求（`third-party request`），第三方请求和第一方请求一样，都会带上**各自域名下**的 `cookie`，所以就有了`第一方cookie`（`first-party cookie`）和`第三方cookie`（`third-party cookie`）的区别。上面提到的 `CSRF` 攻击，就是利用了第三方 `cookie`可以携带发送的特点 。

### 2. 同站`cookie`

“同站`cookie`”不是根据同源策略判断，而是PSL（公共后缀列表），子域名可以访问父域名`cookie`，但父域名无法访问子域名`cookie`。

