---
layout: post
title: SQLServer中实现Slope\Avedev
date: 2016-11-25
categories: blog
author: guolf
tags: [SQLServer]
description: SQLServer中实现Slope\Avedev功能

---

## Slope

```
CREATE TABLE [test_slope](x FLOAT, y FLOAT)

DECLARE
@intCount INT
SET @intCount = 1
WHILE @intCount < 11
BEGIN
INSERT INTO [test_slope]
SELECT  @intCount, 5.12 * @intCount + 7 * rand()
SET @intCount = @intCount + 1
END

--– Get the Slope
SELECT (count(x)*sum(x*y) - sum(x)* sum(y))/(count(x)*sum(x*x) - sum(x)* sum(x)) FROM [test_slope]
```


## Avedev

```
CREATE TABLE [test_avedev]([score] [float] NULL)

--insert data ..

begin
declare @v_avg float;
declare @v_count int;
select @v_avg = AVG(score),@v_count =COUNT(0) from test_avedev; 
-- Get the Avedev
select SUM(ABS(score-@v_avg))/@v_count from test_avedev;
end;

```