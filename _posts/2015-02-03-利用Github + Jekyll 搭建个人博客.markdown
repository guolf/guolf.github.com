---
layout: post
title: 利用Github + Jekyll 搭建个人博客
date: 2015-02-03
categories: blog
author: guolf
tags: [github, jekyll]
description: 我写这篇文章的目的是记录本博客的搭建过程，自己从零开始逐步搭建起来了GitHub Pages，其中借鉴了很多的博客和模版，稍后会在后面列出，也为没有用过gihub和jekyll的童鞋提供一点帮助。

---
## 利用Github + Jekyll 搭建个人博客

我写这篇文章的目的是记录本博客的搭建过程，自己从零开始逐步搭建起来了GitHub Pages，其中借鉴了很多的博客和模版，稍后会在后面列出，也为没有用过gihub和jekyll的童鞋提供一点帮助。

学习使用github网页的最好办法就是clone别人的代码，看懂他们的代码，并修改成自己喜欢的样子。这篇文章介绍了windows下从最初安装软件到使用的过程。

下面开始一步步讲解Github Pages的使用流程：

## 在windows下安装ruby环境

### 1). 下载并安装 [RubyInstaller for Windows](http://rubyinstaller.org/downloads/)。
版本可以选择2.0或者1.9.3都可以。

双击安装，安装时选中“Add Ruby executables to your PATH”前的框将ruby添加到环境变量中。

安装完成后，在 Windows 命令行窗口中执行以下命令，检查ruby是否已经加到PATH中： 

```
ruby --version
```

### 2). 安装 DevKit 。

请根据主页上的描述下载对应的DevKit版本，下载后直接解压到没有空格的路径（例如 E:\DevKit)，然后在Windows的命令行窗口中执行以下命令：

```
ruby dk.rb init 
ruby dk.rb install
```

### 3). 安装Jekyll和相关的包。

由于国内网络原因（你懂的），导致 rubygems.org 存放在 Amazon S3 上面的资源文件间歇性连接失败。解决方案是使用淘宝的 RubyGems 镜像，它是一个完整 rubygems.org 镜像，你可以用此代替官方版本，同步频率目前为15分钟一次以保证尽量与官方服务同步。

```
gem sources --remove https://rubygems.org/
gem sources -a https://ruby.taobao.org/
gem sources -l
```

安装完成Ruby和DevKit 后继续安装jekyll，执行如下命令安装：

```
gem install jekyll
```

等待安装完成即可。

## 创建git版本库

登录到自己的Github账户，选择New repository，Project Name命名为：”你的用户名”.github.com，例如我的就叫“guolf.github.com”。

完成后在本地克隆jekyll-bootstrap模版，运行命令：

```
git clone https://github.com/plusjade/jekyll-bootstrap.git USERNAME.github.com
```

将jekyll-bootstrap模版克隆到本地USERNAME.github.com文件夹下。

然后cd到文件夹内运行命令：

```ruby
jekyll serve
```

成功后打开浏览器输入地址：

`http://localhost:4000` 即可浏览本地生成的页面。

## 绑定域名

创建CNAME文件，内容是你的域名。比如： guolf.com
登录域名DNS解析页面，如果是绑定顶级域名，则增加一条A记录，指向[username].github.io所对应的IP。可通过ping命令获得。

```
C:\Users\User>ping guolf.github.io
正在 Ping github.map.fastly.net [103.245.222.133] 具有 32 字节的数据:
来自 103.245.222.133 的回复: 字节=32 时间=64ms TTL=49
来自 103.245.222.133 的回复: 字节=32 时间=65ms TTL=49
来自 103.245.222.133 的回复: 字节=32 时间=63ms TTL=49
来自 103.245.222.133 的回复: 字节=32 时间=63ms TTL=49
103.245.222.133 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 63ms，最长 = 65ms，平均 = 63ms
```
通过上述可得知，guolf.github.io所对应的Ip为103.245.222.133。

增加两条A记录，@、WWW  均指向103.245.222.133

如果是二级域名，通过增加解析CNAME记录.
CNAME文件内容为blog.52dev.net
增加一条CNAME记录，主机记录填写blog,记录值填写`guolf.github.io`即可。


