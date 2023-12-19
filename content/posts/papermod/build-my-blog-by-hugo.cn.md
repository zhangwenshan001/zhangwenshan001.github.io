---
title: 使用Hugo构建个人blog
summary: 本文记录了该blog构建的过程，主要采用Hugo+PaperMod主题+github部署
date: 2023-12-19
cover:
  image: images/hugo.png
  caption: "Generated using [OG Image Playground by Vercel](https://og-playground.vercel.app/)"

authors:
  - admin

CNtags:
  - Hugo
---

一直以来都有搭建一个私人blog的想法，奈何迟迟没有行动。趁着最近一些变化以及行动力还未消退，周末尝试着直接利用github部署了一个简单的blog。本文将会对整个过程进行简单地记录。

## github仓库
1. 首先，我们需要有一个github账号。还没有的话先注册一个。
2. 新建repository，格式是 **[username].github.io**
3. 如果想使用github默认支持的Github Pages，则可通过 **project > Settings > Github Pages > Choose a theme** 直接选择一个Jekyll主题，简单地编辑一下index.md的内容，然后commit changes，网站主页就大功告成了。

## 静态页面生成器
除了Jekyll，还有许多静态页面生成器，这里简单地对以下三个静态页生成器极其特点进行了比较总结。
 - [Hugo](https://gohugo.io/): 
    - Go语言开发
    - 生成静态页面速度非常快
    - [github](https://github.com/gohugoio/hugo) stared 70k
 - [Jekyll](https://jekyllrb.com/)
    - Ruby语言开发
    - Github Pages默认支持的
    - [github](https://github.com/jekyll/jekyll) stared 47k
 - [Hexo](https://hexo.io/)
    - Node.js开发
    - 中文社区支持较好
    - [github](https://github.com/hexojs/hexo) stared 37k

老实说具体的差别目前还没有深入研究，作为后端工程师总之就先选hugo吧，以后碰到问题再仔细了解。

## Hugo & PaperMod

### 快速开始
- 根据步骤首先安装[Hugo](https://gohugo.io/getting-started/quick-start/)
```sh
brew install hugo
```
- 用hugo创建一个新项目
``` sh
hugo new site <name of site> --format yaml
cd <name of site>
```
- 找一个简单好看的主题，本次采用[PaperMod](https://github.com/adityatelange/hugo-PaperMod)
（也可以通过submodule的方式添加）
```bash
git clone https://github.com/adityatelange/hugo-PaperMod themes/PaperMod --depth=1
```
更新时
```sh
cd themes/PaperMod
git pull
```
- 修改配置文件`config.yml`中的主题
```yml
theme: "PaperMod"
```

PaperMod的页面配置需要自己简单学习下各个配置项。为了方便起见，本次我就直接把它的demo下载下来修改了一下，总之先把项目跑起来。

### 添加文章
用以下命令生成一个`md`文件并进行编辑（也就是现在正在编辑的这篇文章）
```sh
hugo new content [PATH_TO_NEW_POST].md
```
没有写过markdown文档的话，也需要简单熟悉一下，这里就不赘述了，自行谷歌一下。

### 本地预览
使用以下命令启动本地环境，默认可以在`http://localhost:1313/`进行访问
```sh
hugo server
```
如果想要确认草稿，也可以带上参数启动本地服务
```sh
hugo server --buildDrafts
hugo server -D
```
### 发布
以下命令将会发布静态页面
```sh
hugo
```

## 部署
内容确认完毕后，将整个项目提交到 **[username].github.io**仓库。
接下来的一个问题是部署。直接在Github Pages里选择main分支部署是不行的。还需要添加一个github的workflow。
参考这个项目：[actions-hugo](https://github.com/peaceiris/actions-hugo)。

添加以下内容到 `.github/workflows/gh-pages.yml`
``` yml
name: github pages

on:
  push:
    branches:
      - main # Set a branch to deploy

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: true # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0 # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: "0.121.1"
          # extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```
添加上述文件后，当代码提交到`main`分支时github actions会自动执行，并将生成的静态页面提交到`gh-pages`分支，这里需要注意下，默认情况下Workflow permissions可能没有修改和创建分支的权限，需要在 **project > Settings > Actions > General > Workflow permissions** 里设置一下权限。


最后在Github Pages的配置中选择`gh-pages`分支进行部署，整个过程就完成了。



## License
Copyright 2023-present [Wenshan](https://zhangwenshan001.github.io/).