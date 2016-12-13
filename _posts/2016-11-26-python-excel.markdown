---
layout: post
title: python3批量处理excel文件生成sql语句
date: 2016-11-26
categories: blog
author: guolf
tags: [python]
description: python3批量处理excel文件生成sql语句

---

##  安装xlrd

```
pip intall xlrd
```

## 遍历获取Excel文件

```
def get_all_excel(rootDir): 
	list_dirs = os.walk(rootDir) 
	for root, dirs, files in list_dirs: 
		for d in dirs: 
			get_all_excel(rootDir = os.path.join(root, d))
		for f in files:
			filename = os.path.join(root,f)
			if 'xls' in filename:
				#excel_by_name(filename)
				print(os.path.join(root, f))
```

## 读取excel生成SQL语句

```
def excel_by_name(file,colnameindex=0,by_index=0):
    list =[]
    data = xlrd.open_workbook(file)
    table = data.sheets()[0]
    nrows = table.nrows #行数
    ncols = table.ncols #列数
    colnames =  table.row_values(colnameindex)
    sql = ""
    for rownum in range(1,nrows):
        row = table.row_values(rownum)
        if row:
            sInset = "insert into temp (oname,oaddress,username,superclass,telnum,telname)"
            sValues = "values ("
            for i in range(len(colnames)):
                if isinstance(row[i],int):
                    sValues += "'" + str(row[i]) + "',"
                if isinstance(row[i],float):
                    sValues += "'" + str(int(row[i])) + "',"
                else:
                    sValues += "'" + row[i] + "',"
            sql += sInset[:-1]+")" + sValues[:-1]+");" + "\r\n"
	pos = file.rfind(".") #lastIndexof
    f1 = open(file[:pos] '.sql','w')
    f1.write(sql)
    f1.close()

```

完整代码

```

import os,sys,xlrd

def get_all_excel(rootDir): 
	list_dirs = os.walk(rootDir) 
	for root, dirs, files in list_dirs: 
		for d in dirs: 
			get_all_excel(rootDir = os.path.join(root, d))
		for f in files:
			filename = os.path.join(root,f)
			if 'xls' in filename:
				excel_by_name(filename)
				print(os.path.join(root, f))

def excel_by_name(file,colnameindex=0,by_index=0):
    list =[]
    data = xlrd.open_workbook(file)
    table = data.sheets()[0]
    nrows = table.nrows #行数
    ncols = table.ncols #列数
    colnames =  table.row_values(colnameindex)
    sql = ""
    for rownum in range(1,nrows):
        row = table.row_values(rownum)
        if row:
            sInset = "insert into temp (oname,oaddress,username,superclass,telnum,telname)"
            sValues = "values ("
            for i in range(len(colnames)):
                if isinstance(row[i],int):
                    sValues += "'" + str(row[i]) + "',"
                if isinstance(row[i],float):
                    sValues += "'" + str(int(row[i])) + "',"
                else:
                    sValues += "'" + row[i] + "',"
            sql += sInset[:-1]+")" + sValues[:-1]+");" + "\r\n"
	pos = file.rfind(".") #lastIndexof
    f1 = open(file[:pos] '.sql','w')
    f1.write(sql)
    f1.close()

def main():
	get_all_excel(rootDir="/Users/guolf/Documents/temp")

if __name__=="__main__":
    main()

```