---
layout: post
title: genymotion unable create virtual device
date: 2016-12-15
categories: blog
author: guolf
tags: [genymotion]
description: genymotion无法下载模拟器解决方法

---

genymotion有时因网络问题，无法下载，提示超时。

![image](/img/A2BC68FB-9DBC-432D-9811-6348924744CE.png)

解决方法：

查看genymotion日志，找到下载URL，手动下载

```
cat ~/.Genymobile/genymotion.log
```

![image](/img/D5C7B50E-EBE3-4F1F-9EF2-1AAE9673EF65.png)

`http://dl.genymotion.com/dists/5.0.0/ova/genymotion_vbox86p_5.0_160825_104023.ova
`即为需要下载的文件，将该文件下载后，拷贝至`/Users/guolf/.Genymobile/Genymotion/ova/genymotion_vbox86p_5.0_160825_104023.ova`。

```
mv genymotion_vbox86p_5.0_160825_104023.ova /Users/guolf/.Genymobile/Genymotion/ova/genymotion_vbox86p_5.0_160825_104023.ova
```

重启genymotion,再次选择该模拟器，点击下载，几秒钟后就会显示下载完成，OK了。



