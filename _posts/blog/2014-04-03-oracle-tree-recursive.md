---
layout:     post
title:      Oracle 树结构递归用法
category: blog
description: oracle tree recursive 树 递归
---

Oracle中树形结构的数据在查询时候比较麻烦，前不久看到同事有个用法觉得很不错，oracle内置的，可以很便利的解决树递归查询问题

###示例表结构
    id       name   parent_id
    1         aa     
    2         bb    1
    3         cc    2
    4         dd    3

###从根节点到页节点

    select * from table_
    start with id = '1'
    connect by prior id=parent_id

###从叶节点到根节点

    select * from table_ 
    start with id = '4'
    connect by prior parent_id=id
