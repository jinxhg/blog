---
title: hexo要点
date: 2019-11-12 11:46:05
tags:
categories: hexo
---
<!-- toc -->
### 文档地址
中文官网：https://hexo.io/zh-cn/index.html
中文文档: https://hexo.io/zh-cn/docs/commands

### hexo中使用toc标签
安装hexo-toc
```shell
$ npm install hexo-toc --save
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
```markdown
<!-- toc -->
```

### hexo自定义新文章路径
example
```shell
$ hexo new page --path about/me "About me"
```
以上命令会创建一个 source/about/me.md 文件，同时 Front Matter 中的 title 为 "About me"
注意！title 是必须指定的！如果你这么做并不能达到你的目的：
```shell
$ hexo new page --path about/me
```
此时 Hexo 会创建 source/_posts/about/me.md，同时 me.md 的 Front Matter 中的 title 为 "page"。这是因为在上述命令中，hexo-cli 将 page 视为指定文章的标题、并采用默认的 layout。

### hexo草稿
1.生成草稿
```shell
$ hexo new draft "test-draft"
```
会在source/_drafts目录下面，多一个test-draft.md的文件
2.强行预览草稿
草稿文件不会被显示在页面上，链接也访问不到。如果你希望强行预览草稿，更改配置文件：
```yml
render_drafts: true
```
或者，如下方式启动server：
```shell
$ hexo server --drafts
```
3.把草稿变成文章
```shell
$ hexo publish [layout] <filename>
```

### hexo命令
1.generate

2.server

3.deploy

### hexo文章资源文件夹

### hexo部署
