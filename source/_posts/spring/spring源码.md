---
title: spring源码
date: 2020-12-15 15:29:47
categories: spring
---
<!-- toc -->
### 方法论
1. 看源码注解,英文注解看不懂可以安装idea翻译插件
2. 先看脉络,后面有兴趣有时间再深究
3. 大胆猜测
4. 坚持

### 编程式使用IOC容器
``` java
ClassPathResource res = new ClassPathResource("beans.xml");
DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(factory);
reader.loadBeanDefinitions(res);
```