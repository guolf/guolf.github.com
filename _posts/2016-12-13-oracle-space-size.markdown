---
layout: post
title: oracle表空间占用情况查看
date: 2016-12-13
categories: blog
author: guolf
tags: [Oracle]
description: oracle表空间占用情况查看

---

## 查询每个表空间大小

```

select b.file_id "文件ID号",
       b.tablespace_name "表空间名",
       b.bytes / 1024 / 1024 || 'M' "字节数",
       (b.bytes - sum(nvl(a.bytes, 0))) / 1024 / 1024 || 'M' "已使用",
       sum(nvl(a.bytes, 0)) / 1024 / 1024 || 'M' "剩余空间",
       100 - sum(nvl(a.bytes, 0)) / (b.bytes) * 100 "占用百分比"
  from dba_free_space a, dba_data_files b
 where a.file_id = b.file_id
 group by b.tablespace_name, b.file_id, b.bytes
 order by b.file_id;
```

## 查询某个用户下所有表占用空间 
  
```
select OWNER,
        t.segment_name,
        t.segment_type,
        sum(t.bytes / 1024 / 1024) as "size(M)"
   from dba_segments t
  where t.owner = 'YPDZJG'
    and t.segment_type = 'TABLE'
  group by OWNER, t.segment_name, t.segment_type
  order by "size(M)" desc
```