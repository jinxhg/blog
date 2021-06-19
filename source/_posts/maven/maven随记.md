---
title: maven
date: 2020-11-25 23:03:43
tags: ['maven', 'maven wrapper']
categories: maven
---
<!-- toc -->
### 安装maven wrapper
mvn -N io.takari:maven:wrapper -Dmaven={maven版本}
``` shell
mvn -N io.takari:maven:wrapper -Dmaven=3.6.3
``` 
### idea项目导入依赖报错可能问题解决
删除项目的.idea文件夹和所有模块的.iml文件,然后重新导入项目