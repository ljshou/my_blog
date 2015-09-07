title: 使用 Mathjax 在 hexo 中输入数学公式
date: 2014-10-26 23:29:04
categories: hexo
tags: hexo
---
使用Mathjax像latex一样在markdown中输入公式
<!--more-->

### 安装方法
```
$ npm install hexo-renderer-mathjax --save
```
修改 `_config.yml`
```
plugins:
- hexo-renderer-mathjax
```

### 使用方法
```
$$
\frac{\partial u}{\partial t} = h^2 \left( \frac{\partial^2 u}{\partial x^2} + \frac{\partial^2 u}{\partial y^2} + \frac{\partial^2 u}{\partial z^2}\right)
$$
```
效果:
$$
\frac{\partial u}{\partial t} = h^2 \left( \frac{\partial^2 u}{\partial x^2} + \frac{\partial^2 u}{\partial y^2} + \frac{\partial^2 u}{\partial z^2}\right)
$$
