title: Set up personal blog using hexo on github/gitcafe
date: 2014-10-30 15:00:02
categories: hexo
tags: hexo
---
This blog tells you how to set up your own blog rapidly using [Hexo](http://hexo.io) on github/gitcafe pages.

<!--more-->
### Prerequisite
1. install Node.js 
	- https://nodejs.org/en/
2. install git
	- Ubuntu: `sudo apt-get install git`
	- Windows: https://git-for-windows.github.io/
3. install Hexo:
	- Ubuntu: `sudo npm install -g hexo-cli`
	- Windows: `npm install -g hexo-cli`

Hexo official site: [Hexo](http://hexo.io)

### Set up blog locally
```sh
hexo init my_blog #initialize blog
cd my_blog
npm install #set up blog
npm install hexo-server --save #support blog preview
npm install hexo-deployer-git --save #support deploy using git
npm install hexo-generator-feed --save
npm install hexo-generator-sitemap --save
npm install hexo-renderer-mathjax --save
hexo server #preview blog locally. localhost:4000
```

### Hexo commands
+ Initialize hexo blog: `hexo init`
+ Write a new blog: `hexo new "blog's title"`
+ Generate static files: 	`hexo generate`
+ Blog preview: `hexo server`. preview blog at `localhost:4000`
+ Blog deploy: `hexo depoly`

### Hexo deploy
gitcafe 和 github 很相似，可用于代码托管，同样具有 git-pages的功能，能够托管静态网页。
+ 注册gitcafe账号。在gitcafe注册并创建**公开项目**，**项目名和用户名要一致**。
  在blog\_config.yml添加如下代码：注意把 your_name 替换成你的项目名
```sh
deploy:
  type: git
  repository: git@gitcafe.com:your_name/your_name.git
  branch: gitcafe-pages
```
+ 设置 ssh [参考](https://help.github.com/articles/generating-ssh-keys/)
将公钥拷贝到你的gitcafe主页上，帐号设置，SSH公钥管理即可。
+ 行hexo deploy部署blog到gitcafe， 访问http://your_name.gitcafe.com

### 写文章
在blog 目录下,
```sh
hexo new "my first blog"
hexo generate #生成静态网页
hexo server #在localhost:4000查看博客效果
hexo deploy #将本地的博客部署到 gitcafe 上发布
```

### 后话
博客的一些个性化设置，将在后序的博客中分享
