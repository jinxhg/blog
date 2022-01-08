---
title: VirtualBox网络配置
date: 2021-10-19 11:26:52
tags: ['VirtualBox网络配置', 'VirtualBox']
categories: VirtualBox
---
<!-- toc -->
## VirtualBox连接的5种方式
1. NAT：虚拟机可以通过宿主机访问主机能够访问的一切网络，宿主机不能访问虚拟机，虚拟机之间不能访问
2. NAT网络：在NAT的基础上，虚拟机之间搭建了局域网，可以实现虚拟机之间的相互访问
3. 桥接：相当于虚拟机与宿主机连接在同一局域网内，相对于宿主机可见，可以看成是一台连接的宿主机
4. 内部：虚拟机不能连外网
5. 仅主机(host-only)：虚拟机不能连外网，并且不互通

## 桥接模式
1. 相当于虚拟机与宿主机连接在同一局域网内，相对于宿主机可见，可以看成是一台连接的宿主机。
2. 桥接模式每一台虚拟机都会占用一个真实的ip地址，会造成ip地址极大的浪费。
3. 如何设置固定ip，使用桥接模式经常会造成局域网内ip冲突。

### 桥接模式配置
{% asset_img 桥接模式配置.jpg [桥接模式配置] %}
1. 连接方式：桥接网卡
2. 界面名称：正在使用的网卡
3. 启动虚拟机，配置linux网络：vi /etc/sysconfig/network-scripts/ifcfg-<系统网络名称>，配置动态ip：BOOTPROTO=dhcp， 开机启动网卡：ONBOOT=yes
```
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s3
UUID=6a7d29f8-3521-4764-bf44-eab3f8b3fe42
DEVICE=enp0s3
ONBOOT=yes
```
4. 然后启动该网络设置
```
$ systemctl restart network
或
$ service network restart
```

## NAT网络
1. 可以固定虚拟机ip而不导致ip冲突
2. 虚拟机可以访问外网和内网虚拟机，宿主机可以访问虚拟机
3. 方便搭建用于学习的集群

### 1. 添加VirtualBox网卡
管理==》全局设定==》网络，添加网卡
{% asset_img VirtualBox添加网卡-1.png [VirtualBox添加网卡] %}
{% asset_img VirtualBox添加网卡-2.png [VirtualBox添加网卡] %}

### 2. 设置虚拟机网络
选择需配置虚拟机，设置==》网络==网卡1，连接方式选择“NAT网络”，界面名称选择正在使用的网卡
{% asset_img 配置虚拟机网络.png [配置虚拟机网络] %}

### 3. 启动虚拟机，配置网络
vi /etc/sysconfig/network-scripts/ifcfg-<系统网络名称>，修改BOOTPROTO=static，并添加以下配置
```
BOOTPROTO=static
ONBOOT=yes
IPADDR=10.0.2.14
NETMASK=255.255.255.0
GATEWAY=10.0.2.1
DNS1=114.114.114.114
DNS2=8.8.8.8
```
然后启动该网络设置
```
$ systemctl restart network
或
$ service network restart
```
此时虚拟机已经可以访问外网

### 4. 宿主机访问虚拟机，配置VirtualBox网卡端口映射
虚拟机采用NAT网络配置，虚拟机的ip相对于宿主机是隐藏的。想要使得宿主机对虚拟机进行访问，需要配置端口映射，即通过将宿主机的一个端口(localhost:port)与虚拟机的ssh端口进行连接，宿主机通过访问宿主机端口(localhost:port)实现对虚拟机ssh端口(ip:22)的访问。
注意，这个端口是一一对应的。因为整个虚拟机子网10.0.2.0/24网段对主机是隐藏的，主机要想访问虚拟机子网中的任意一台虚拟机，就必须建立一个独立的宿主机端口与虚拟机的ssh端口进行连接。
配置NAT端口映射，如下：
{% asset_img 配置VirtualBox网卡端口映射.jpg [配置VirtualBox网卡端口映射] %}

宿主机使用ssh命令连接虚拟机
```
$ ssh -p 23330 root@localhost
```

### 5. 虚拟机之间连接
虚拟机之间使用ip地址就可以了，因为虚拟机之间由虚拟局域网连接。