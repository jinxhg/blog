---
title: linux命令
date: 2020-07-12 00:17:33
tags: ['linux', 'linux命令']
categories: linux
---
<!-- toc -->
### 查找所有文件中某个字符串
查找目录下的所有文件中是否含有某个字符串
```ssh
find .|xargs grep -ri "IBM"
```
查找目录下的所有文件中是否含有某个字符串,并且只打印出文件名
```ssh
find .|xargs grep -ri "IBM" -l
```

### 压缩和解压
解压tar.gz文件
```
tar -zxvf xxx.tar.gz
```