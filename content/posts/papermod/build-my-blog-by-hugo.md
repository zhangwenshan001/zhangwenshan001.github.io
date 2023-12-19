---
title: Building my own blog by Hugo
summary: This article records the process of building this blog by Hugo & PaperMod theme & github actions
date: 2023-12-17
cover:
  image: images/hugo.png
  caption: "Generated using [OG Image Playground by Vercel](https://og-playground.vercel.app/)"

authors:
  - admin

tags:
  - Hugo
---

Recently, I tried to build a blog and deployed it directly on github. This article will briefly explain how to approach it.

## Github Repository
1. First, we need to have a github account. If you don’t have one yet, please register one.
2. Create a new repository named **[username].github.io** in github.
3. If you want to use Github Pages that github supports by default, please select a Jekyll theme through **project > Settings > Github Pages > Choose a theme**. Then edit the contents of `index.md` and submit the changes. Your own website is ready now!

## Static Website Generator
In addition to Jekyll, there are many other static page generators. Here is a brief comparison and summary of the following three static page generators as well as their characteristics.
 - [Hugo](https://gohugo.io/): 
    - Written in Go
    - Super fast
    - [github](https://github.com/gohugoio/hugo) stared 70k
 - [Jekyll](https://jekyllrb.com/)
    - Written in Ruby
    - Built-in support for Github pages
    - [github](https://github.com/jekyll/jekyll) stared 47k
 - [Hexo](https://hexo.io/)
    - Written in Node.js
    - Supporting Chinese
    - [github](https://github.com/hexojs/hexo) stared 37k

As a backend engineer, I'll just choose `Hugo` for beginning. We can figure out their differences later.

## Hugo & PaperMod

### Quick Start
- First, follow the steps and install [Hugo](https://gohugo.io/getting-started/quick-start/)
```sh
brew install hugo
```
- Create a new project by Hugo
``` sh
hugo new site <name of site> --format yaml
cd <name of site>
```
- Find a concise and good-looking theme you like. This time I used [PaperMod](https://github.com/adityatelange/hugo-PaperMod)
(It could also be added via submodule)
```bash
git clone https://github.com/adityatelange/hugo-PaperMod themes/PaperMod --depth=1
```
For update
```sh
cd themes/PaperMod
git pull
```
- Modify the theme in the configuration file `config.yml`
```yml
theme: "PaperMod"
```

We might need to spend a little bit time to learn how to config for PaperMod. This time I directly downloaded its demo and made some modifications. Let's run it first.

### Add Content
Generate an `md` file by the following command and edit it.
```sh
hugo new content [PATH_TO_NEW_POST].md
```
If you have never written a markdown document, please google for it and I believe you would be familiar with it quickly.

### Run in local environment
Start the server in your local environment by the following command. The default address would be `http://localhost:1313/`
```sh
hugo server
```
If you want confirm your drafts, please use the following commands
```sh
hugo server --buildDrafts
hugo server -D
```

### Publish the site
When you publish your site, Hugo creates the entire static site in the public directory in the root of your project. 
```sh
hugo
```

## Deployment
After confirming the contents, submit the project to your **[username].github.io** repository. The next step is to deploy it. Deploying the `main` branch directly in Github Pages would not work. We need to add a github workflow.
Please refer: [actions-hugo](https://github.com/peaceiris/actions-hugo)

Add the following content to `.github/workflows/gh-pages.yml`
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
After adding the file, Github Actions will automatically execute when the codes are submitted to the `main` branch, and the generated static pages will be submitted to `gh-pages` branch. It should be noted here that by default, Github Actions might have no permissions to modify and create branches. We need to update permissions in **project > Settings > Actions > General > Workflow permissions**.

Finally, select `gh-pages` branch in the configuration of Github Pages and deploy it. Now the whole process is completed. Enjoy your own site!

## License

Copyright 2023-present [Wenshan](https://zhangwenshan001.github.io/).