---
title: 博客搭建过程
date: 2018-08-09 10:57:20
tags:
    - 博客
categories:
  	- 博客
description: 记录一下博客网站的搭建过程，使用 Github 提供的 GitHub Pages + Hexo 实现。GitHub Pages 是 GitHub 提供的静态网页服务，文件存储在 GitHub 仓库中，可以结合 Jekyll、Hexo 等博客框架，快速的部署博客网站，同时还可以绑定个人域名，成为你的专属网站。Hexo 是一个快速、简洁且高效的博客框架，文档清晰，插件、主题丰富，使用 Makedown 解析文章，生成静态网页。使用 GitHub Pages + Hexo 即可快速部署漂亮的博客，让您专注博客内容。
---
记录一下博客网站的搭建过程，使用 Github 提供的 GitHub Pages + Hexo 实现。GitHub Pages 是 GitHub 提供的静态网页服务，文件存储在 GitHub 仓库中，可以结合 Jekyll、Hexo 等博客框架，快速的部署博客网站，同时还可以绑定个人域名，成为你的专属网站。Hexo 是一个快速、简洁且高效的博客框架，文档清晰，插件、主题丰富，使用 Makedown 解析文章，生成静态网页。使用 GitHub Pages + Hexo 即可快速部署漂亮的博客，让您专注博客内容。

博客的搭建过程参考了官方教程和一些网上的教程，“不重复造轮子”，我不再详细描写具体的配置过程，而是直接贴出教程的链接，方便查阅。

搭建过程主要以下几个步骤：
  - 建立 GitHub Pages 仓库
  - 安装、部署 Hexo
  - 配置主题
  - 安装插件
  - 编写博客

在贴出的教程链接中，会有交叉的地方，请注意辨别。

## 建立GitHub Pages仓库

建立 [GitHub Pages](https://pages.github.com/) 仓库，首先要有 GitHub 账号，然后在 GitHub 上建立一个命名为 [你GitHub用户名].github.io的私有仓库。需要注意的是，仓库的命名必须为你GitHub的用户名，否则将不生效，就不能通过 `https://[你GitHub用户名].github.io` 来访问。

官方教程点击[这里](https://pages.github.com/)。

建好仓库之后，在仓库里面上传一个 `index.html` 文件，然后通过 `https://[你GitHub用户名].github.io` 来访问，可以看到`index.html`的内容，就说明已经建立好。上传后直接访问可能会出现404，等几分钟就好了。确认仓库已经建好，用来测试的 `index.html` 文件就可以删除了。

在搭建博客、编写博客的过程中，基本都用不上 Git 命令，但博客是基于 GitHub 的仓库实现的，还是推荐了解一下 Git 命令，方便解决各种问题。这里推荐一个简单清晰的git教程：[廖雪峰Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)。

## 安装、部署Hexo
Hexo 是基于 node.js 实现的，所以需要在自己电脑上安装 node.js，Hexo 没有可视化界面，需要使用命令行来完成各种功能，为了便于使用命令行，所以还要安装 Fit Bash。有了 node.js 和 Git Bash 之后，就可以愉快的在上面敲命令行安装、部署 Hexo 了。

安装的方法官方教程里都有，点击[这里](https://hexo.io/zh-cn/docs/)，官方教程比较全面，也会比较繁琐。

还有一个比较简单的教程，点击[这里](http://visugar.com/2017/05/04/20170504SetUpHexoBlog/)，里面还会教你怎么新建 GitHub 仓库，可以直接跳过，使用上面建好的就可以了。

到这里，部署 Hexo，并发布到 GitHub 仓库之后，博客就已经搭建完了。

不过，你可能还不太满意，界面不漂亮、没有站内搜索、没有RSS...

界面不漂亮？换个主题吧。

## 配置主题

好看的主题都在[这里](https://hexo.io/themes/)，点击进去预览，然后根据主题的配置方法配置即可。

我使用的主题是 [maupassant](https://www.haomwei.com/technology/maupassant-hexo.html)，大道至简，里面有详细的配置方法，按需配置即可。

本地配置好之后，使用 Hexo 发布命令 `hexo deploy`，就可以在 `https://[你GitHub用户名].github.io` 上看到效果。

## 安装插件

想要站内搜索，推荐这个插件：[hexo-generator-search](https://github.com/wzpan/hexo-generator-search)

想要RSS，推荐这个插件：[hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed)

还想安装其他的？都在[这里](https://hexo.io/plugins/)。

漂亮的博客网站已经建好了，开始书写内容吧。

## 编写博客
Hexo 是将 Markdown 文件解析为静态网页，所以博客也需要使用 Markdown 格式书写。Markdown 的目标是实现「易读易写」，所以使用起来很简单，这里是[官方教程](http://www.markdown.cn/)。

Markdown 的编辑器有很多，我使用的是 Atom，加上下面这些插件，即可方便的书写博客。
  - markdown-writer：高亮显 示Markdown
  - markdown-scroll-sync：编写的时候按了 `Ctrl + Shift + M`，之后能同步滚动预览
  - markdown-image-assistant：截图之后直接按 `Ctrl + V` 粘贴

在 Atom 的 `File` -> `settings` -> `install`中搜索相应插件然后安装即可，使用方法在搜索结果界面点进去都有介绍。

好了，专注内容吧。
