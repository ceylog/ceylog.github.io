---
layout:     post
title:      Oracle 树结构递归简单用法
category: blog
description: oracle tree recursive 树 递归
---

Oracle中树形结构的数据在查询时候比较麻烦，前不久看到同事有个用法觉得很不错，Oracle内置的，可以很便利的解决树递归查询问题

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

### `PIOR`位置

运算符 `PRIOR`被放置于等号前后的位置，决定着查询时的检索顺序。

PRIOR 被置于`connect by`子句中等号的前面时，则强制从根节点到叶节点的顺序检索，即由父节点向子节点方向通过树结构，我们称之为自顶向下 的方式。如：

    connect by prio id=parent_id

`PIROR`运算符被置于`connect by`子句中等号的后面时，则强制从叶节点到根节点的顺序检索，即由子节点向父节点方向通过树结构，我们称之为自底向上 的方式。例如：

    connect by id=prio parent_id

在这种方式中也应指定一个开始的节点。
