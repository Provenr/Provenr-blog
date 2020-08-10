

## 一、什么是持续集成？

只要有新的代码，就会自动抓取。然后，提供一个运行环境，执行测试，完成构建，还能部署到服务器。

`优点:`，每次代码的小幅变更，就能看到运行结果，从而不断累积小的变更，而不是在开发周期结束时，一下子合并一大块代码。

## 二、Travis CI 
Travis CI 提供的是持续集成服务（Continuous Integration，简称 CI）。

Travis CI 目前有两个网站，一个是 travis-ci.com，另一个是 travis-ci.org. 前者用于企业级和私有仓库，后者用于开源的公有仓库

## 三、构建流程
1. 本地修改代码，提交到指定分支
2. Travis CI 监听仓库改变
3. Travis CI 执行 依赖安装 和 script任务（这里可以做一些安装测试构建任务的依赖和测试构建命令）
4. 任务执行成功后，在 Travis 的 after_success 钩子里面用 ssh 免密登陆服务器
5. [scp](http://note.youdao.com/s/aLZCvNjf) 命令将 文件 部署到个人服务器上

#### 完整的脚本请移步[附录](#附录)

## 四、trvais.yml文件配置基础知识

### 1、trvais 提供的 钩子方法

```
before_install：install 阶段之前执行
before_script：script 阶段之前执行
after_failure：script 阶段失败时执行
after_success：script 阶段成功时执行
before_deploy：deploy 步骤之前执行
after_deploy：deploy 步骤之后执行
after_script：script 阶段之后执行

```
### 2、完整的生命周期：

```
1、before_install
2、install
3、before_script
4、script
5、aftersuccess or afterfailure
6、[OPTIONAL] before_deploy
7、[OPTIONAL] deploy
8、[OPTIONAL] after_deploy
9、after_script
```


Node 项目的install和script阶段都有默认脚本，可以省略。
> install默认值：npm install
>
> script默认值：npm test

### 3、部署
script阶段结束以后，还可以设置通知步骤（notification）和部署步骤（deployment）。



## 如何构建完成后推送第三方服务器
1、 Travis 客户端安装
```

```
2、生成密钥对[详细](http://note.youdao.com/s/Tokq9HKf)
```

```


## 参考稳定
【1】[持续集成服务Travis CI 教程- 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2017/12/travis_ci_tutorial.html)

【2】[集成失败](https://github.com/travis-ci/travis.rb/issues/555#issuecomment-375855660)


## 附录
【1】普通版
```
# Designated language
language: node_js

# Specify the node version.
# stable 会安装最新 14.x.x node版本, 可能会出现构建问题
# 可以指定版本node为12
node_js: stable

# Environment variables
env:
  global:
    - GitHub_REF: git@github.com:Provenr/javascript-Design-Patterns.git

# Cache the node_modules folder and don't need to download and install all npm packages every time.
cache:
  apt: true
  directories:
    - node_modules

# before_install:


# Install dependence
install:
  - npm install -g gitbook-cli
  - gitbook install
# before_script:

# The script to execute.
script:
  - echo 初始化文件...
  # - gitbook init
  - echo 正在生成静态文件...
  - gitbook build

after_success:

```
【2】持续集成成功后 推送到第三方服务器
```yml
language: node_js
node_js:
- 12
cache:
  apt: true
  directories:
  - node_modules
before_install:
- openssl aes-256-cbc -K $encrypted_f31094380f4c_key -iv $encrypted_f31094380f4c_iv
  -in id_rsa.enc -out ~/.ssh/id_rsa -d
- chmod 600 ~/.ssh/id_rsa
- echo -e "Host ${G_HOST}\n\tStrictHostKeyChecking no\n\tIdentityFile ~/.ssh/id_rsa"
  >> ~/.ssh/config

install:
- npm install hexo -g
- echo 安装hexo相关环境
- npm install
before_script:
- echo 正在清空缓存静态文件...
- hexo clean
- echo 正在生成静态文件...
- hexo generate
- cd ./public
- ls -l
script:
- echo 开始部署...

after_script:
- echo 部署完成！
branches:
  only:
  - master
env:
  global:
  - GH_REF=github.com/Provenr/Provenr.github.io.git
  - GH_username=liuyangning
  - GH_useremail=web_lyn@163.com
  - G_HOST=119.3.215.227
after_success:
  - cd ./public/
  - echo 准备移动至指定文件夹到服务器...
  - travis_wait 30 scp -o stricthostkeychecking=no -rC ./* root@${G_HOST}:/root/provenr-local/blog
  - echo 部署完成!

```

