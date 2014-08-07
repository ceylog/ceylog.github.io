---
layout:     post
title:      redis 整理（二）String类型
category: blog
description: redis 相关整理（二）String类型

---

上文详细介绍了Redis数据库以及它的安装过程，及适用场景，已经了解，Redis数据库是基于内存的数据库，速度极快，本篇将介绍redis数据库的操作使用方法，Redis有七种数据类型：字符串，哈希表，链表，集合和有序集合



## String 类型简介

String是最简单的类型，可以理解成与Memcached是一模一样的类型，一个Key对应一个Value，可以完全实现Memcached的功能，而且效率要比Memcached高很多，同时可以设置Redis的定时数据持久化，操作日志的记录以及主从复制等功能！

## 操作

### set

设置Key对应的值为string类型的value。

例如设置一个name = zhangsan 的键值对，可以这样做：
    $ redis-cli
    redis 127.0.0.1:6379> set name zhangsan
    OK
    redis 127.0.0.1:6379> get name
    "zhangsan"
    redis 127.0.0.1:6379>

### setnx

设置key 对应的值为string 类型的value。如果key 已经存在，返回0，nx 是not exist 的意思。

例如添加一个name= lisi 的键值对，可以这样做:
    redis 127.0.0.1:6379> setnx name lisi
    (integer) 0
    redis 127.0.0.1:6379> get name
    "lisi"
    redis 127.0.0.1:6379>

由于原来name 有一个对应的值，所以本次的修改不生效，且返回码是0。

### setex

设置一个键对应的值，并对此键值对设置一个有效期。

例如：指定一个键值对 result = success，并且设置一个有效期为10秒，我们来这样做：

    redis 127.0.0.1:6379> set result 10 success
    OK
    redis 127.0.0.1:6379> get result
    "success"
    redis 127.0.0.1:6379> get result
    (nil)
    redis 127.0.0.1:6379>

由于第二次调用已经超过10秒，所以无法取到result的值了！！！


[1]:    http://redis.io "redis"
[2]:    http://redis.io/download "redis download"