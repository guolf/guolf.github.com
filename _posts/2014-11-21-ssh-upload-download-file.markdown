---
layout: post
title: ssh文件上传下载
date: 2014-11-21
categories: blog
author: guolf
tags: [linux, ssh]
description: linux中如果要进行文件上传下载，除了用ftp外，ssh也是一个不错的选择。

---

#### 下载文件和目录

```
# 从服务下载文件
scp username@servername:/remote/filename /local/dir/
# 从服务器下载整个目录
scp -r username@servername:/remote/dir/ /local/dir/
# 非默认端口下载文件
scp -P 29308 username@servername:/remote/filename /local/dir/
```

#### 上传文件和目录

```
# 上传指定文件到服务器
scp /local/filename username@servername:/remote/dir
# 上传目录到服务器
scp  -r /local/dir username@servername:/remote/dir
# 非默认端口上传文件
scp -P 13122 /local/filename username@servername:/remote/dir
```

仅作记录.