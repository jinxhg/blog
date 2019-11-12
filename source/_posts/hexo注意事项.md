---
title: hexo注意事项
date: 2019-11-12 11:46:05
tags:
---
### hexo生成目录
安装hexo-toc
```shell
npm install hexo-toc --save
```
然后,配置_config.yml文件
```yml
toc:
  maxdepth: 3
  class: toc
  slugify: transliteration
  decodeEntities: false
  anchor:
    position: after
    symbol: '#'
    style: header-anchor
```
在Markdown文章中加入TOC的占位符
```md
<!-- toc -->
```