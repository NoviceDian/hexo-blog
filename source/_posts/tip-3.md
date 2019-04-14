---
title: tip-3
date: 2019-04-14 22:40:44
tags:
---


# <center>使用hexo和github pages搭建自己的博客系统</center>

## [hexo](https://github.com/hexojs/hexo)


hexo是一个快速的，简单且高效的博客系统。他是基于node.js的。

1. 安装

因为hexo是基于node.js的，所以需要先安装node.js，以下是安装地址：

[node.js安装地址](http://nodejs.cn/)

在完成node.js之后，使用node提供的npm功能对hexo进行安装：

```
npm install hexo-cli -g
```

以上便将hexo所需要的必要的博客功能搭建完成。

2. 创建博客

- 使用以下代码初始化你的博客文件

```
$ hexo init blog
$ cd blog
```

- 创建文章

```
$ hexo new "Hello Hexo"
```
以上便创建了一篇名为hello hexo的文章。(文章布局默认为post，对于文章布局，本次不做详解)文件地址在初始化博客文件夹目录的\source\_posts下对应的md文件。编辑里面内容就是对应文章内容


- 生成静态页面

```
$ hexo clean
$ hexo generate或者hexo g

```

以上命令会将你的源文件和主题自动生成对应的静态页面。

- 部署

```
$ hexo deploy 或者hexo d
```

将静态页面部署到服务器。


## [GitHub Pages](https://pages.github.com/)


个人博客的搭建肯定少不了服务器，但是在有时候为了一个简单的博客去租用服务器又很麻烦，且作为学习积累使用的话代价太大，这里github为每个使用者提供了GitHub Pages，为每个使用者提供了简单的站点服务。以下为使用步骤：


1. 使用github创建一个仓库，且仓库名必须为username.github.io,username是你注册github时使用的用户名

2. 然后克隆该项目到本地。该文件夹就是github pages使用的静态页面了。


## hexo结合github pages

github pages提供了站点服务，hexo提供了个人博客的框架，且hexo自动生成的静态文件结构完全就是复核github pages规范的，所以很简单的就能结合github pages完成联动。

只要对hexo配置(_config.yml)中的部署配置进行修改，就能很快的适配github pages。

```
deploy:
  type: git
  repository: git@github.com:liuxianan/liuxianan.github.io.git
  branch: master
```

将部署配置改为git的地址，在使用部署指令时，会自动部署到你的github的服务器上，十分方便。


注意若出现以下错误

```
Deployer not found: github 或者 Deployer not found: git
```

只需要安装部署插件即可：

```
npm install hexo-deployer-git --save
```









