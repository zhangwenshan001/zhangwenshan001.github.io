---
title: Building my own blog my by Hugo
summary: This is the first demo blog.
date: 2023-12-17
# draft: true

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com)'

authors:
  - admin

tags:
  - Hugo
---


This is a demo for creating a blog on github. 

## Static Website Generator
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

This time I tried to use hugo to build a demo blog.


## Steps
1. Create new repository named **[username].github.io** in github
2. Quick start for [hugo](https://gohugo.io/getting-started/quick-start/)
3. Add content (And writing this article now)
```
hugo new content [PATH_TO_NEW_POST].md
```
4. Run in local
```
hugo server
```
or run the following commands to include draft content 
```
hugo server --buildDrafts
hugo server -D
```
5. Change [themes](https://themes.gohugo.io/)

## License

Copyright 2023-present [Wenshan](https://zhangwenshan001.github.io/).
