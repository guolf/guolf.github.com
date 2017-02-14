---
layout: post
title: solr6.4 +tomcat8+zookeeper3.4.9 Windows搭建教程
date: 2017-2-14
categories: blog
author: guolf
tags: [solr,zookeeper]
description: solr6.4 +tomcat8+zookeeper3.4.9 Windows搭建教程

---


	软件环境配置
	环境：Windows Server 2008 R2
	tomcat版本：8.5.11
	solr版本：6.4.1
	zookeeper版本：3.4.9
	jdk版本：1.8

## 部署

### 下载安装包
下载tomcat\solr\zookeeper，创建solr-cloud目录，复制三份tomcat\ zookeeper至solr-cloud目录下，创建三个home文件夹

### 将solr部署至tomcat
将`solr-6.4.1\server\solr-webapp\webapp`文件拷贝至三个tomcat目录webapp下，并重命名为solr，将`solr-6.4.1\server\lib`与`solr-6.4.1\server\lib\ext`目录下所有jar拷贝至`webapp\solr\WEB-IN\lib`下。

将三个tomcat端口分别改为7071,7072,7073。

将`solr-6.4.1\server\resources\log4j.properties`拷贝至`webapp\solr\WEB-IN\classes\`

修改`webapps\solr\WEB-INF\web.xml`文件，将env-entry代码块取消注释，并相应修改home目录位置,将security-constraint块注释。

```
<env-entry>
       <env-entry-name>solr/home</env-entry-name>
       <env-entry-value>E:\glf\solr-cloud\home2-7072</env-entry-value>
       <env-entry-type>java.lang.String</env-entry-type>
</env-entry>

<!-- Get rid of error message -->
<!--
  <security-constraint>
    <web-resource-collection>
      <web-resource-name>Disable TRACE</web-resource-name>
      <url-pattern>/</url-pattern>
      <http-method>TRACE</http-method>
    </web-resource-collection>
    <auth-constraint/>
  </security-constraint>
  <security-constraint>
    <web-resource-collection>
      <web-resource-name>Enable everything but TRACE</web-resource-name>
      <url-pattern>/</url-pattern>
      <http-method-omission>TRACE</http-method-omission>
    </web-resource-collection>
</security-constraint>
-->
```

### 拷贝solr配置文件

将solr-6.4.1\server\solr下文件拷贝至三个home目录下，将solr.xml的 hostPort分别改为7071,7072,7073，必须与上一步骤的tomcat端口一致

### 启动tomcat 
分别启动各tomcat，验证是否能正常访问

http://localhost:7071/solr/index.html

http://localhost:7072/solr/index.html 

http://localhost:7073/solr/index.html

如果都能正常访问到admin页面，则配置成功。否则就需要检查哪里错了或遗漏了。接下来配置zookeeper使其关联起来。

### 配置zookeeper
在三个zookeeper目录下创建data目录，创建一个myid文件，内容分别为1、2、3；conf目录下创建zoo.cfg，添加以下内容

```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=E:/glf/solr-cloud/zookeeper1/data
clientPort=2181 # 端口号分别修改为2181,2182,2183
server.1=127.0.0.1:2881:3881
server.2=127.0.0.1:2882:3882
server.3=127.0.0.1:2883:3883
```

### 启动zookeeper

进入bin目录，运行zkServer

### 修改tomcat的catalina.bat文件，添加以下代码

```
set JAVA_OPTS=-Dbootstrap_confdir=E:/glf/solr-cloud/cloud-config -Dcollection.configName=myconf -DzkHost=127.0.0.1:2181,127.0.0.1:212,127.0.0.1:2183 -DnumShards=3
```

### 重启tomcat
如果能在管理界面看到cloud，则说明配置成功



