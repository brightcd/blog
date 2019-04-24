---
title: hexo+netlify构建个人网站
date: 2019-04-24 16:09:38
tags:
---

本站点就是根据以下教程搭建，十分简单，5分钟搞定，希望对想搭建个人网站的同学有用~

## hexo建站

hexo是一款基于Node.js的静态网站生成器，用于管理静态资源，最适合博客类网站搭建，支持使用Markdown解析文章

[静态网站生成器是如何工作的](https://zhuanlan.zhihu.com/p/27682700)

### 安装hexo

安装前提，确保本机上装有下面两个程序：

- Node.js
- Git

>对于Mac用户，建议使用brew安装上面的程序，非常方便
>1. 通过运行下面命令安装Homebrew
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
>2. 安装node.js和git即可，其中npm在安装node的同时就安装了
    brew install nodejs
    brew install git
    也可以通过nvm来安装node.js，运行下面命令：
    brew install nvm
    nvm install stable

上面程序准备完成，使用npm来安装Hexo

    $ npm install -g hexo-cli

### 搭建网站

hexo安装完成后初始化项目：

    $ hexo init <folder>
    $ cd <foldeer>
    $ npm install

新建文章：

    $ hexo new "我的最新文章标题"

生成静态文件（public文件夹下）：

    $ hexo generate (或hexo g)

本地启动站点：

    $ hexo server (或hexo s)

至此就可以在浏览器打开<http://localhost:4000>访问本地站点了，开心写文章，开心使用我上一篇介绍的Markdown编写md文件就ok~

附上：[Hexo官方文档](https://hexo.io/zh-cn/)

## netlify发布站点

netlify发布站点是基于github资源库的，所以在发布之前先将本地项目上传到github资源库

### 提交到github

在[github](https://github.com/)上新建资源库，点击右上角头像，your repositories，新建资源库，新建仓库时Add .gitignore和Add a license处请选择None

初始化仓库并提交：

    # <folder>指hexo新建的工程根目录
    cd <folder>

    # 初始化仓库
    git init

    # 首次提交
    git add .
    git commit -m 'initial commit'

    # 指定远程仓库（可以使用git remote -v验证链接是否正确）
    git remote add origin <远程仓库链接>

    # 提交到远程仓库master分支
    git push origin master

### 发布到netlify

1. 到[netlify官方网站](https://app.netlify.com/)注册netlify账号
{% asset_img register.png Markdown Preview Enhanced %}

2. 关联github账户
登陆后，在 https://app.netlify.com/account/sites 页面下，点击New site from Git进入下一步。
   {% asset_img newsit.png Markdown Preview Enhanced %}
   点击GitHub关联Netlify和你的仓库
   {% asset_img linkgit.png Markdown Preview Enhanced %}

3. 授权验证
首次关联时，你会看到授权窗口，点击Authorize netlify同意授权后，Netlify可以有权访问你在GitHub上的仓库内容了

4. 选择仓库
授权完毕后，页面会跳转回Netlify。通过搜索栏来快速找到你所创建的仓库，选中该仓库。
{% asset_img chooserepo.png Markdown Preview Enhanced %}

5. 配置发布设置
Netlify允许你自定义你的部署设置，我们的demo只需使用默认设置即可。
{% asset_img deploy.png Markdown Preview Enhanced %}

6. 构建并发布网站
点击完Deploy site之后，Netlify会构建并发布你的网站内容。你可以通过Deploy log实时观察部署过程。（但是本人从未看到过【捂脸】）
{% asset_img publish.jpeg Markdown Preview Enhanced %}

7. 发布成功
网站上线。Netlify会在网站发布成功的同时提供给你一个*.netlify.com为后缀以及随机生成的英文名为前缀的子域名。假如你不喜欢Netlify给你的前缀，并且还未来得及购买域名，可以通过Domain settings > Edit site name来快速美化你的网址：
{% asset_img domain.jpeg Markdown Preview Enhanced %}

直接访问netlify上的网址就能访问站点，以上一个静态网站就搭建发布完成啦，普天同庆！

## 搭建过程中遇到的两个坑

### 安装主题时git子模块的处理

题外话：推荐大家使用next主题哈，个人非常喜欢这个简约的主题

问题：

1. 在安装新主题时，在theme目录下直接git clone了主题项目next
2. 导致hexo代码库中出现git子模块，按照网上的方法删除了子模块中.git目录
3. 但是在提交整个工程时子模块目录仍未被提交，远程版本库中该目录下文件为空
4. 强制git add，报错： fatal: Pathspec 'theme/next/' is in submodule 'theme-next'

解决方案：

    git rm --cached next
	git add next

尽量不要在我们的项目中再引入其他的git仓库，就能避免这个问题

### 发布到netlify时git仓库中必须包含node_modules目录

个人理解发布到netlify的站点，实际上只是将git仓库中的代码部署到netlify的服务器上，我们在build项目时使用的命令为hexo generate，生成静态文件到public/下供访问。

为支持hexo命令，git项目中必须要有该node模块，也就是通过npm安装在node_modules中的模块及其依赖

因此我们要么在build命令中加上npm install每次构建安装模块，要么在git仓库中保存该模块，个人更倾向于后者（至少deploy速度更快）

>so，.gitignore文件中不能包含node_modules/，该目录需要push到远程仓库

***

希望以上分享能帮助到大家~

参考：https://juejin.im/post/5c6f68586fb9a049c6447355
