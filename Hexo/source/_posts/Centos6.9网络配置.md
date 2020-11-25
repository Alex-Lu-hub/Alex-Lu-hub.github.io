---
title: Centos6.9网络配置
tags:  [Linux]
categories: [Linux]
date: 2020-10-8 12:00:00
updated: 2020-10-25 21:00:00
excerpt: 新的Centos6.9 minimal虚拟机无法连接网络，虚拟机网络配置的记录
---

Linux初步课程学习中需要搭建一个新的虚拟机，选择了用Centos6.9的minimal版本作为虚拟机版本。不知道是我VMware配置问题还是镜像问题，配置好的虚拟机无法连接网络。

``ifconfig``可以看见只有一个``lo``网卡，使用``ifconfig -a``可以看见有一个``eth0``网卡未被启用。

回想起当时新人培训的时候做的小型局域网，记一篇笔记来记录虚拟机的网络配置备忘。

# 配置网卡

* 进入网卡配置文件目录``/etc/sysconfig/network-scripts``
* 找到对应的网卡文件，我的是``ifcfg-eth0``，进行配置

```bash
#别问我为啥不用vim，centos默认没有vim
vi ifcfg-eth0
```

* 重点配置以下内容

```shell
IPADDR=192.168.229.6   ip地址
NETMASK=255.255.255.0  子网掩码
GATEWAY=192.168.229.2    网关
ONBOOT=yes             随系统自动启动
STATIC=true            静态地址
BOOTPROTO=static       静态地址（推荐使用这个指定静态地址）
```

* 为何使用静态地址？

  我对此的理解还不够，但是从最开始的培训和这次的配置我选择的都是静态地址的选项，使用静态的更为稳定，不需要过多的配置

* 其中，ip地址和网关需要查看VMware的虚拟网络配置器，如下图

  ![](/img/Centos6.9网络配置/1.png)


  ![](/img/Centos6.9网络配置/2.png)

* 配置到这里，基本上的网卡配置就完成了

# DNS配置

* 完成上述配置后，直接ping百度肯定是ping不同的，因为DNS配置还没有进行
* 打开DNS配置文件

```bash
vi /etc/resolv.conf
```

* 文件里面应该是没有任何内容的，有也没有关系，加入以下内容

```shell
nameserver 114.114.114.114
namesetver 8.8.8.8
```
**经学长指正，上述方法在重启后DNS会失效，在``/etc/sysconfig/network-script/ifcfg-eth0``中添加``DNS1=X.X.X.X``，``DNS2=Y.Y.Y.Y``等更为合适**

* 重启网络

```bash
service network restart
```

* 现在你就可以在这台虚拟机里愉快的使用网络啦！

# 碎碎念

* 为啥我要配网络呢？

  因为虚拟机里装好后却不少东西（连vim都没有，差评），配置新的yum源后发现无法更新，进一步发现了没有网络的问题。使用ssh连接不把网卡配好肯定也是无法连接到的。于是就折腾了一下网卡的配置。

* 为啥非要用ssh连接？

  VMware的命令行界面，连复制粘贴的不行，不用ssh在远程终端连接能行吗？！