---
title: Lerna 管理 Monorepo 项目
tags:
  - Lerna 
  - Monorepo
categories:
  - 工程化
date: 2020-10-16 11:07:29
modified: 2020-10-16 11:07:34
---

## 什么是Monorepo

Monorepo 的全称是 monolithic repository，即单体式仓库，与之对应的是 Multirepo(multiple repository)，这里的“单”和“多”是指每个仓库中所管理的模块数量。

Multirepo 是比较传统的做法，即每一个 package 都单独用一个仓库来进行管理。

Monorep 是把所有相关的 package 都放在一个仓库里进行管理，**每个 package 独立发布**。如[vue-cli](https://github.com/vuejs/vue-cli)

![image](http://img.provenr.cn/blog/2020-10-16%2011-11-35.png)