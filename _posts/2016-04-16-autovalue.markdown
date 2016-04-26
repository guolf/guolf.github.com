---
layout: post
title: [译]AutoValue使用介绍
date: 2016-04-16
categories: blog
author: guolf
tags: [mac,windows]
description: An Introduction to AutoValue

---

## AutoValue使用介绍

> 本文翻译自：http://ryanharter.com/blog/2016/03/22/autovalue/

值类型在Java中是很乏味的，谷歌推出的`AutoValue`库使值类型变得更加容易，期待已久的更新也增加了扩展灵活性。

### Java中的值类型

在我们谈论`AutoValue`的伟大之前，让我们来看看它解决了这个问题：值类型

值类型是一个基于属性简单不可变对象。
