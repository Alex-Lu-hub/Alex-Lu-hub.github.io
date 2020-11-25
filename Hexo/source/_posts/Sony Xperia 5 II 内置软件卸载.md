---
title: Sony Xperia 5 II 内置软件卸载
tags:  [Android]
categories: [Android]
---

入手港版Sony后有一些我用不到的软件，又不想root，利用adb卸载。

<!-- more -->

# 下载adb工具包

这玩意儿网上百度一大堆，下一个就好了。应该是个压缩包，解压到你知道的地方即可

# 手机准备

首先，手机要打开 ``开发者选项`` ，通常应该是点击版本号5下即可，具体的请自行百度

随后，进入 ``开发者选下`` ，打开 ``USB调试``

请下载一个包名查看软件，后面删除包需要用这类软件来查看包名

# 删除流程

* 手机连接电脑

* 在解压adb的文件夹打开 ``cmd`` 

* 输入如下命令

  ```powershell
  adb devices
  ```

  能够看到自己的设备，说明连接成功。若未看到，请检查手机准备步骤是否完成

* 连接设备

  ```powershell
  adb shell
  ```

* 删除命令如下

  ```powershell
  pm uninstall -k --user 0 应用包名
  ```

  这个包名不是应用的名字，具体的格式可以用如下命令查看

  ```powershell
  pm list packages
  ```

  所以具体要删除啥，就用下载的包名查看软件看即可

# 我删除的软件

```powershell
# Call of Duty
com.activision.callofduty.shooter
# LinkedIN
com.linkedin.android
# TIDAL 3 Months Free
com.android.tidal.campaigninstaller
# Netflix
com.netflix.mediaclient
# Google地图
com.google.android.apps.maps
# 数字健康
com.google.android.apps.wellbeing
# 智能镜头
com.google.ar.lens
# Google Play Service for AR
com.google.ar.core
# Google
com.google.android.googlequicksearchbox
# Chrome
com.android.chrome
# 相册
com.google.android.apps.photos
# 文件
com.google.android.documentsui
```