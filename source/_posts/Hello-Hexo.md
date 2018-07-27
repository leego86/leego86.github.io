---
title: 'Hexo 安装和使用'
date: 2018-07-13 23:25:36
tags: hexo
categories: hexo
---
## 安装环境
### 安装git
``` bash
$ sudo apt-get install git
```
查看是否安装成功
``` bash
$ git version
```

### 设置git账号
``` bash
$ git config --global user.name "leego86"
$ git config --global user.email "leego8697@gmail.com"
```

### 设置ssh密钥
``` bash
$ ssh-keygen -t rsa -C leego8697@gmail.com
```
回车三次在.ssh下会生成id_rsa和id_rsa.pub两个文件
进入ssh目录
``` bash
$ cd ~/.ssh
```
---
**不确定是不是必需**

添加密钥到ssh-agent
``` bash
$ eval "$(ssh-agent -s)"  
```
添加生成的ssh key到ssh-agent
```bash
$ ssh-add ~/.ssh/id_rsa
```
---
###配置github ssh
登录github 进入settings SSH and GPG keys
点击 New ssh key
title自定义
key 复制id_rsa.pub内的值，然后保存

检测是否配置成功
``` bash
$ ssh -T git@github.com
```
如果看到Hi后面是用户名，说明配置成功


### 安装nodejs
``` bash
$ sudo apt-get install nodejs
```
查看版本

```bash
$ node -v
```
### 安装npm
``` bash
$ sudo apt-get install npm
```
查看是否功
``` bash
$ npm -v
```

### 安装hexo
``` bash
$ npm install -g hexo-cli
```

查看安装情况
``` bash 
$ hexo version
```
### 设置工作目录
``` bash
$ mkdir blog
```

 初始化
``` bash
cd blog
hexo init
npm install
```
或者
``` bash
$ hexo init blog
$ cd blog
$ npm install
```
## 使用
### 配置账号
编辑_config.yml
deploy:
  type: git
  repo: git@github.com:username/username.github.io.git
  branch: master

  注：
  1 冒号后有空格
  2 如果repo直接复制https://github.com/leego86/leego86.github.io.git
  每次发布需要输入账号密码

### 基本命令 
``` bash
$ hexo n test 新建一个文章
$ hexo g    生成文章
$ hexo s    本地部署
$ hexo d    发布到github
```
如果发布提示nothing to commit, working tree clean 需要执行hexo clean 然后再执行hexo d