---
title: Pluto环境搭建
tags:  [Pluto]
categories: [ADI]
---

本文旨在帮助刚入手pluto的同志们搭建好pluto的工作环境，便于后续的在matlab下开发测试。

<!-- more -->

# USB驱动安装

安装pluto的USB驱动 ``PlutoSDR-M2k-USB-Drivers.exe`` ，需要的资源在 [github页](https://github.com/analogdevicesinc/plutosdr-m2k-drivers-win/releases) 可以下载。安装过程极其简单，一路默认next就可以了。

安装完成后，可以在设备管理器看到如下图的设备（当然需要连接上pluto），就说明安装成功了。

![](/img/pluto环境搭建.assets/image-20201108132710358.png)

![](/img/pluto环境搭建.assets/image-20201108132742781.png)

同时，我们会看到如下的PlutoSDR存储盘：

![](/img/pluto环境搭建.assets/image-20201108132822198.png)

# OSC上位机安装

安装Pluto官方的上位机管理软件 ``adi-osc-setup.exe`` ，需要的资源在 [官方网站](https://wiki.analog.com/resources/tools-software/linux-software/iio_oscilloscope) 可以下载。安装过程也没啥好说的，选择合适的路径一路默认即可。

# libiio驱动安装

安装所需的libiio驱动 ``libiio-0.21.g565bf68-Windows-setup.exe`` ，需要的资源在 [github页](https://github.com/analogdevicesinc/libiio/releases/tag/0.21) 可以下载。一路默认完事儿了。

# MATLAB安装

这就不用我说了吧，安装要求的版本，我们这次是 ``R2018b`` ，相关安装的教程资料网上一大堆，不过多赘述。

# TDM-GCC编译器安装

安装所需的编译器 ``tdm64-gcc-9.2.0.exe`` ，需要的资源在 [官方网站](https://jmeubank.github.io/tdm-gcc/) 可以下载。

## 编译器的安装

* 点击 ``Create`` 

  ![](/img/pluto环境搭建.assets/image-20201108134436530.png)

* 选择安装的编译器类型，这里选择第二项

  ![](/img/pluto环境搭建.assets/image-20201108134532959.png)

* 选择安装路径，注意**不要用中文，也不要有空格！**默认的路径其实已经可以了，如果需要更改尽量只更改安装盘，不更改文件夹命名

  ![](/img/pluto环境搭建.assets/image-20201108134753307.png)

* 后面一路默认安装即可，可能安装的时间会比较长，耐心等待

## 环境变量配置

* 打开 ``此电脑->属性->高级系统设置->环境变量`` 

  ![](/img/pluto环境搭建.assets/image-20201108135210921.png)

* 新建环境变量，如下配置。注意**变量值为TDM-GCC编译器安装文件夹，一定要一致！**

  ![](/img/pluto环境搭建.assets/image-20201108135344804.png)

## MATLAB配置

* 设置环境变量，输入以下命令

  ```matlab
  #第一个参数是我们的环境变量名，第二个参数是我们的安装路径
  setenv('MW_MINGW64_LOC','C:\TDM-GCC-64');
  mex -setup
  ```

* 在弹出的选项中选择C++语言，点击即可

  ![](/img/pluto环境搭建.assets/image-20201108140126264.png)

# 安装Matlab依赖包

记住要安装Matlab的官方依赖包，我们这次是ADI官方提供的依赖包，官网应该也有提供的下载，这里就不在列出

# 配置完成

至此，整个环境配置完成，愉快的去造作吧！