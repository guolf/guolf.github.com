---
layout: post
title: Oracle正则表达式
date: 2016-11-23
categories: blog
author: guolf
tags: [Oracle]
description: Oracle中使用正则表达式查询、替换

---

## Oracle正则表达式

正则查询患者妊娠、怀孕时间

```
select regexp_substr('妊娠10周' ,'(孕|妊娠)([^"]*)周',1, 1) from dual;  
select regexp_substr('孕10周' ,'(孕|妊娠)([^"]*)周',1, 1) from dual;  
```


正则替换患者姓名

```
select regexp_replace('出院记录[王老五]' ,'\[[^"]*(])','[某患者]',1, 0) from dual;
```