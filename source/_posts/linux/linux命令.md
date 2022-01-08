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

### df磁盘空间查看
df可以查看一级文件夹大小、使用比例、档案系统及其挂入点。
```
$ df -Th
```
-T:可以用来查看分区的文件系统。
-h:表示使用「Human-readable」的输出，也就是在档案系统大小使用 GB、MB 等易读的格式。

### du详细磁盘使用分析
```
$ du -h --max-depth=1 /var/log
```