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

## 一、什么是Monorepo

Monorepo 的全称是 monolithic repository，即单体式仓库，与之对应的是 Multirepo(multiple repository)，这里的“单”和“多”是指每个仓库中所管理的模块数量。

Multirepo 是比较传统的做法，即每一个 package 都单独用一个仓库来进行管理。

Monorep 是把所有相关的 package 都放在一个仓库里进行管理，**每个 package 独立发布**。如[vue-cli](https://github.com/vuejs/vue-cli)

![image](https://provenr.obs.cn-north-4.myhuaweicloud.com/blog/2020-10-16%2011-11-35.png)

### Multirepo 痛点

1. **不好调试。**因为最终的包是通过文件拷贝的方式组装到一起的，并且都是压缩过的，无法组建一个自上到下的调试流程（实际工作中只能加log，然后重新把包编译组装一遍看效果）
2. **包的依赖关系不清晰。**pkg-a、pkg-b索性没有版本管理，更像是源码级别的，但逻辑又比较独立。pkg-main中的package.json最终会拷贝到 pkg-npm 中，但又依赖pkg-a、pkg-b中的某些包，所以要把pkg-a、pkg-b中的依赖合并到pkg-main中。pkg-main和pkg-npm的package.json耦合在一起，导致一些本来是工程的开发依赖也会发布到 npm 上去，变成pkg-npm 的依赖包。
3. **依赖的包冗余。**可以看到，pkg-a、pkg-b、pkg-main要分别编译，都依赖了babel、webpack等，要分别 cd 到各个目录安装依赖。
4. **发布需要手动修改版本号。** 因为最终只发布了一个包，但实际逻辑要求这个包即要全局安装又要本地安装，业务没有拆开，导致要安装两遍。耦合一起，即便使用 npm link 也会导致调试困难，
5. **发版没有 [CHANGELOG.md](http://CHANGELOG.md)。** 因为pkg-a、pkg-b都没有真正管理版本，所以也没有完善的CHANGELOG来记录自上个版本发布已来的变动。

## 二、什么 是Lerna
> A tool for managing JavaScript projects with multiple packages.

Lerna 是一个管理多个 npm 模块的工具，是 Babel 自己用来维护自己的 Monorepo 并开源出的一个项目。优化维护多包的工作流，解决多个包互相依赖，且发布需要手动维护多个包的问题。Lerna 现在已经被很多著名的项目组织使用，如：Babel, React, Vue, Angular, Ember, Meteor, Jest 。

## 目录结构
- ### package  存放所有的项目和子项目

- ### script  存放项目统一的构建、监控脚本

- ### 子项目结构
    - 各子项目，源码入口统一为 src/index.js；
    - 各子项目，编译出口统一为 build/index.js；
    - 各子项目，IDE 辅助提示统一为 build/index.d.ts；
    - 各子项目，基于 ES6 语法、使用 Babel 编译；
    
- ### package.json & lerna.json 

    ```
    // package.json
    {
      "name": "root",
      "private": true, // 私有的，不会被发布，是管理整个项目，与要发布到npm的解耦
      "devDependencies": {
        "lerna": "^3.15.0"
      }
    }
     
    // lerna.json
    {
      "packages": [
        "packages/*"
      ],
      "version": "0.0.0"
    }
    ```

## command

 1. 全局安装[lerna](https://github.com/lerna/lerna)
    ```
    npm install -g lerna
    ```
 2. 初始化
    ```
    lerna init
    ```
 3. 构建各子项目间的依赖关系
    ```
    lerna bootstrap --hoist
    ```
    > `lerna bootstrap`后,子项目间是过“快捷方式”进行运接,类似于npm i
    >
    > --hoist 来把每个 package 下的依赖包都提升到工程根目录，来降低安装以及管理的成本
 4. 版本发布
    ```
    lerna publish
    ```
    > 将包publish到npm上；注意要先 确认镜像是否为npm (日常开发会切换到淘宝源,提升速度); 再使用`npm login` 登录npm源，否则会失败；
使用--hoist 选项后，所有公共的依赖都只会安装在根目录的node_modules目录中去,而不会在每个包目录下的node_modules中都保留各自的依赖包。
    
    publish 流程:
    - 运行lerna updated来决定哪一个包需要被publish
    - 如果有必要，将会更新`lerna.json`中的`version`
    - 将所有更新过的的包中的`package.json`的`version`字段更新
    - 将所有更新过的包中的依赖更新
    - 为新版本创建一个`git commit`或`tag`
                                                                             
  - 删除所有项目的node_modules目录
    ```
    lerna clean 
    ```
  - 默认为所有的项目运行npm run [script]脚本，可以指定项目；
    ```
     lerna run [script] 
    ```
  - 列出下次发版lerna publish要更新的包。
    ```
    lerna changed 
    ```

  - 可以指定为某一个或所有的包安装依赖
    > 依赖可以是外部(npm i 安装的)也可以是内部依赖(packages/下的包，会创建符号链接)，
    
    example：
    ```
    lerna add <package>[@version] [--dev] [--exact] [--peer] ：
    
    lerna add babel , 该命令会在package-1和package-2下安装babel
    lerna add react --scope=package-1 ,该命令会在package-1下安装react
    lerna add package-2 --scope=package-1，该命令会在package-1下安装package-2
    ```

 - 创建一个lerna管理的包
    ```
     lerna create <name> [loc]
    ```
 - 控制台打印 packages下的包名
    ```
    lerna ls
    ```
- 类似npm link,创建软连接 ,但是实测怎么不起作用？(lerna version:v3.22.0)
    ```
    lerna link
    ```

### [精读《Monorepo 的优势》](https://github.com/dt-fe/weekly/issues/151)

###[基于 Lerna 管理 packages 的 Monorepo 项目最佳实践](https://juejin.im/post/6844903911095025678)

