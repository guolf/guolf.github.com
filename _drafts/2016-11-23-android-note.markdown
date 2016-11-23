---
layout: post
title: Android开发笔记
date: 2016-11-23
categories: blog
author: guolf
tags: [Android]
description: Android开发笔记

---

## gradle

### 多渠道打包
gradle多渠道打包，自定义包名、应用名称，自定义变量。UPDATE_ID可通过`BuildConfig.UPDATE_ID`获取。

```
productFlavors {
	demo {
		applicationId "com.android.kfzs.demo"
		buildConfigField "String", "UPDATE_ID", "\"6f272119-7253-453e-a685-21163da46d94\""
		resValue "string", "app_name", "APPNAME-演示版"
		manifestPlaceholders = [BUGLY_APP_CHANNEL_VALUE: "demo"]
	}
	prod {
		applicationId "com.android.kfzs"
		buildConfigField "String", "UPDATE_ID", "\"11e8e8e1-ff71-4521-ab70-e8b8c8966611\""
		resValue "string", "app_name", "APPNAME"
		manifestPlaceholders = [BUGLY_APP_CHANNEL_VALUE: "prod"]
	}
}
            
```

每个渠道使用不同的icon,在src目录下创建渠道名称文件夹，如图所示
![image](/img/58780917-8FE8-4374-8828-70EAEC2B2E6A.png)


### gradle 排除指定模块

```
compile ('com.github.czy1121:noticeview:1.0.0'){
	exclude group: "com.android.support", module: "appcompat-v7"
}
```

