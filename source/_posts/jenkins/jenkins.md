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

