---
layout:     post
title:      redis 整理（三）List 类型
category: blog
description: redis 相关整理（三）List 类型

---

## list 类型简介

List是一个链表结构，主要功能是push、pop、获取一个范围的所有值等等，操作中key理解为链表的名字。Redis的list类型其实就是一个每个子元素都是string类型的双向链表。我们可以通过push、pop操作从链表的头部或者尾部添加删除元素，这样list既可以作为栈，又可以作为队列。

## list 操作

### 1. lpush

在key对应list的头部添加字符串元素

    redis 127.0.0.1:6379> lpush mylist "world"
    (integer) 1
    redis 127.0.0.1:6379> lpush mylist "hello"
    (integer) 2
    redis 127.0.0.1:6379> lrange mylist 0 -1
    1) "hello"
    2) "world"

### 2. rpush

在key对应list的尾部添加字符串元素

    redis 127.0.0.1:6379> rpush mylist2 "world"
    (integer) 1
    redis 127.0.0.1:6379> rpush mylist2 "hello"
    (integer) 2
    redis 127.0.0.1:6379> lrange mylist2 0 -1
    1) "hello"
    2) "world"

### 3. linsert

在key对应list的特定位置前或后添加字符串

    redis 127.0.0.1:6379> rpush mylist3 "world"
    (integer) 1
    redis 127.0.0.1:6379> linsert mylist3 before "world" "hello"
    (integer) 2
    redis 127.0.0.1:6379> lrange mylist3 0 -1
    1) "hello"
    2) "world"

### 4. lset

设置list中指定下标的元素值

    redis 127.0.0.1:6379> rpush mylist4 "hello"
    (integer) 1
    redis 127.0.0.1:6379> lset mylist4 0 "world"
    OK
    redis 127.0.0.1:6379> lrange mylist4 0 -1
    1) "world"

### 5. lrem

从key对应list中删除n个和value相同的元素。（n<0从尾删除，n=0全部删除）

    redis 127.0.0.1:6379> rpush mylist5 "hello"
    (integer) 1
    redis 127.0.0.1:6379> rpush mylist5 "hello"
    (integer) 1
    redis 127.0.0.1:6379> lrem mylist5 1 "hello"
    (integer) 1

### 6. ltrim

保留指定key的值范围内的数据

    redis 127.0.0.1:6379> rpush mylist8 "one"
    (integer) 1
    redis 127.0.0.1:6379> rpush mylist8 "two"
    (integer) 2
    redis 127.0.0.1:6379> ltrim mylist8 1 -1
    (integer) 1
    redis 127.0.0.1:6379> lrange mylist5 1 "hello"
    (integer) 1

### 6. lpop

从list的头部删除元素，并返回删除元素

    redis 127.0.0.1:6379> lrange mylist 0 -1
    1) "hello"
    2) "world"
    redis 127.0.0.1:6379> lpop mylist
    "hello"
    redis 127.0.0.1:6379> lrange mylist 0 -1
    "world"
    redis 127.0.0.1:6379>


### 7. rpop

从list的尾部删除元素，并返回删除元素

    redis 127.0.0.1:6379> lrange mylist2 0 -1
    1) "hello"
    2) "world"
    redis 127.0.0.1:6379> rpop mylist2
    "world"
    redis 127.0.0.1:6379> lrange mylist2 0 -1
    1) "hello"
    redis 127.0.0.1:6379>

### 8. rpoplpush

从第一个list的尾部移除元素并添加到第二个list的头部

    redis 127.0.0.1:6379> lrange mylist2 0 -1
    1) "hello"
    2) "world"
    redis 127.0.0.1:6379> rpop mylist2
    "world"
    redis 127.0.0.1:6379> lrange mylist2 0 -1
    1) "hello"
    redis 127.0.0.1:6379>

### 9. lindex

返回名称为key的list中index位置的元素

    redis 127.0.0.1:6379> lrange mylist5 0 -1
    1) "three"
    2) "foo"
    redis 127.0.0.1:6379> lindex mylist5 0
    "three"
    redis 127.0.0.1:6379> lindex mylist5 1
    "foo"
    redis 127.0.0.1:6379>

### 10. llen


返回key对应list的长度

    redis 127.0.0.1:6379> llen mylist5
    (integer) 2
    redis 127.0.0.1:6379>
