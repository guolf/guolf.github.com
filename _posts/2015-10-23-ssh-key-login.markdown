---
layout: post
title: linux利用ssh key 免密码登陆
date: 2015-08-26
categories: blog
author: guolf
tags: [linux, ssh]
description: Dexposed是基于久负盛名的开源Xposed框架实现的一个Android平台上功能强大的无侵入式运行时AOP框架，项目地址：https://github.com/alibaba/dexposed

---
## 利用ssh key验证登陆Linux服务器
### 生成SSH Key

#### 创建RSA Key Pair

```
ssh-keygen -t rsa
```

#### 输入key保存位置
```
Enter file in which to save the key (/home/demo/.ssh/id_rsa):
```

```
Enter passphrase (empty for no passphrase):
```


```
ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/demo/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/demo/.ssh/id_rsa.
Your public key has been saved in /home/demo/.ssh/id_rsa.pub.
The key fingerprint is:
4a:dd:0a:c6:35:4e:3f:ed:27:38:8c:74:44:4d:93:67 demo@a
The key's randomart image is:
+--[ RSA 2048]----+
|          .oo.   |
|         .  o.E  |
|        + .  o   |
|     . = = .     |
|      = S = .    |
|     o + = +     |
|      . o + o .  |
|           . o   |
|                 |
+-----------------+
```
#### 将生成的id_rsa.pub拷贝到服务器

```
scp -p 1234 /home/demo/.ssh/id_rsa.pub root@server:/root/.ssh/
```

将id_rsa.pub重命名成authorized_keys

```
cd .ssh
mv id_rsa.pub authorized_keys
```
#### 修改本机的ssh config
config文件位于`~/.ssh/config`

```
Host myvps
Hostname 104.XX.XXX.XX
Port 1234
User root
IdentityFile ~/.ssh/id_rsa
```
然后直接 `ssh myvps` 即可连接上远程服务器。
