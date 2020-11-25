---
title: gradle随记
date: 2020-11-25 23:04:15
tags:
categories: gradle
---
### GradleW
简化Gradle的安装和部署;不同版本的项目可能需要不同版本的Gradle,手工部署比较麻烦,而且可能产生冲突

### Gradle包损坏
报错message
```
Cause: zip END header not found
```
当出现该错误提示的时候,表示Gradle包损坏,需要删除Gradle包重新下载;同样的,使用Gradlew提示该错误时,表示Gradlew包损坏,需要找到使用对应版本的Gradlew包删除重新下载,如果找不到,可以用everything搜索Gradle-版本号-bin.zip然后进行删除