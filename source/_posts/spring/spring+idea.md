---
title: spring+idea
date: 2020-12-14 16:13:01
categories: spring
---
<!-- toc -->
### idea2018.3+spring5.0.x
1.配置maven库国内镜像
```
buildscript {
	repositories {
		mavenLocal()
		mavenCentral()
		maven { url 'https://maven.aliyun.com/nexus/content/groups/public/' }
		maven { url 'https://maven.aliyun.com/nexus/content/repositories/jcenter'}
		maven { url "https://repo.spring.io/plugins-release" }
	}
	dependencies {
		classpath("io.spring.gradle:propdeps-plugin:0.0.8")
		classpath("io.spring.gradle:docbook-reference-plugin:0.3.1")
		classpath("org.asciidoctor:asciidoctorj-pdf:1.5.0-alpha.16")
		classpath("org.asciidoctor:asciidoctorj-epub3:1.5.0-alpha.7")
	}
}
```

2.spring5.0x与idea的gradle插件版本差异
当idea版本过高,会报如下错误,可以使用idea2018.3版本
```
The getTestClassesDir() method has been deprecated and is scheduled to be removed in Gradle 5.0. Please use the getTestClassesDirs() method instead.
```
