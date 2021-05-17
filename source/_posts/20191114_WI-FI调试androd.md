---
title: WI-FI调试android手机
date: 2019-11-14 11:16:08
---
方法一：
1. 使用数据线连接手机和电脑，打开USB调试

2. 输入`adb devices` ，查看是否连接

3. 获取手机ip地址，`adb shell netcfg`，或在手机`系统-状态信息`查看

4. ping一下手机电脑网络是否联通`ping 192.168.1.1`，然后输入`adb tcpip 8888` （设置端口号为8888，默认端口是5555）

5. 输入`adb connect 192.168.1.1:8888`，即可连接

6. 最后断开手机和电脑的数据线连接，即可wifi调试android手机

>若断开调试，输入`adb disconnect`

方法二：
- 在AndroidStudio下载plugins插件`Android wifi adb`
- ping一下网络是否联通

>连接异常：
开启adb服务 `adb start-server`
关闭adb服务 `adb kill-server`