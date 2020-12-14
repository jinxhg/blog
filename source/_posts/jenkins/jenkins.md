---
title: jenins
date: 2020-12-04 09:56:09
tags:
categories: jenkins
---
<!-- toc -->
### 文档地址
中文文档: https://www.jenkins.io/zh/doc/

### 安装问题
报错
```
sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
...
Caused: sun.security.validator.ValidatorException: PKIX path building failed
...
Caused: javax.net.ssl.SSLHandshakeException
```
jenkins的插件镜像地址为https,缺少证书所有报错,可能是jdk证书有问题,这时候可以重新下载一个jdk试一下

### 离线安装插件
当服务器没有外网的时候,可以先在本地安装插件,然后把本地插件复制到服务器上,
wndows本地插件安装地址C:\Users\当前账号\.jenkins\plugins

### 权限角色管理插件
插件名称: Role-based Authorization Strategy, Role Strategy Plugin插件可以对构建的项目进行授权管理,
让不同的用户管理不同的项目,将不同环境的权限进行区分;该插件可以很灵活的根据需求来进行划分权限,包括正则匹配等