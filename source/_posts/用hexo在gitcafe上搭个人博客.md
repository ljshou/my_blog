title: 用hexo在gitcafe上搭个人博客
date: 2014-10-30 15:00:02
categories: hexo
tags: hexo
---
本文介绍如何用 hexo 快速地在 gitcafe 上搭自己的个人博客。

<!--more-->
###环境准备
1. 安装 Node.js
2. 安装 git
3. 安装 Hexo:
```sh
$ sudo npm install -g hexo
```
Hexo 官网: [Hexo](http://hexo.io)

### 本地建博客
```sh
hexo init blog #博客目录是 blog
cd blog
npm install #博客建成功
hexo server #本地预览，执行后，可在浏览器中输入 localhost:4000查看博客
```

### hexo 命令
+ 初始化新的Hexo项目: hexo init
+ 创建一篇新文章: hexo new “blog's title”
+ 构建静态文件: hexo generate，简写为hexo g
+ 开启本地server进行预览:hexo server， 简写为hexo s。 执行后可以访问localhost:4000进行效果预览。
+ 部署到远程服务器: hexo depoly，简写为hexo d

### 把博客部署到 gitcafe
gitcafe 和 github 很相似，可用于代码托管，同样具有 git-pages的功能，能够托管静态网页。
+ 注册gitcafe账号。在gitcafe注册并创建**公开项目**，**项目名和用户名要一致**。
  在blog\_config.yml添加如下代码：注意把 your_name 替换成你的项目名
  ```sh
  deploy:
  type: github
  repository: git@gitcafe.com:your_name/your_name.git
  branch: gitcafe-pages
  ```
+ 设置 ssh [参考](https://help.github.com/articles/generating-ssh-keys/)
将公钥拷贝到你的gitcafe主页上，帐号设置，SSH公钥管理即可。
+ 行hexo deploy部署blog到gitcafe， 访问http://your_name.gitcafe.com

###写文章
在blog 目录下,
```sh
hexo n "my first blog"
hexo g #生成静态网页
hexo s #在localhost:4000查看博客效果
hexo d #将本地的博客部署到 gitcafe 上发布
```

###后话
博客的一些个性化设置，将在后序的博客中分享
