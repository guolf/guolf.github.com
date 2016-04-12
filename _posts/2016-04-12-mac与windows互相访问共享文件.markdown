---
layout: post
title: mac与windows互相访问共享文件
date: 2016-04-12
categories: blog
author: guolf
tags: [mac,windows]
description: mac与windows互相访问共享文件

---

## Windows访问Mac共享文件夹

1、进入系统偏好-共享，开启文件共享

![image](/img/2656595B-4AD4-46E2-A335-B79A8CD98BA6.png)

2、确保windows、mac在同一网段下，互相能够ping通

windows下`win+R`，打开运行界面，输入`\\192.168.23.2`或者输入电脑名称`\\guolf`。输入用户名、密码，即可进行访问。

![image](/img/win+R.png)

## mac访问windows共享文件夹

1、选中需共享文件夹，右键`属性`-`共享`，开启共享。

2、确保windows、mac在同一网段下，互相能够ping通。mac进入Finder界面，`前往`-`连接服务器`，输入`smb://ip`或者`smb://hostname`

![image](/img/D51DBB8E-2EC8-439E-B06A-6B0F78BAFEBD.png)

3、输入windows用户名密码,即可进行访问。

![image](/img/D26076C4-F252-49A7-ABB9-AD28F4ACDECE.png)
