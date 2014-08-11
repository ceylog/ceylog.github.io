---
layout:     post
title:      redis 整理（三）hash 类型
category: blog
description: redis 相关整理（三）hash 类型

---

## hash 类型简介

redis hash是一个string类型的field和value的映射表。它的添加、删除操作都是0(1)(平均)。hash特别适合用于存储对象。相较于将对象的每个字段存成单个string类型，将一个对象存储在hash类型中会占用更少的内存，并且可以更方便的存取整个对象。省内存的原因是新建一个hash对象时开始是用zipmap来存储的。

## hash 操作

### 1. hset

设置hash field为指定值，如果key不存在，则先创建。

    redis 127.0.0.1:6379> hset myhash field1 Hello
    (integer) 1
    redis 127.0.0.1:6379>

### 2. hsetnx

设置hash field 为指定值，如果key 不存在，则先创建。如果field 已经存在，返回0，nx 是not exist 的意思。

    redis 127.0.0.1:6379> hsetnx myhash field "Hello"
    (integer) 1
    redis 127.0.0.1:6379> hsetnx myhash field "Hello"
    (integer) 0
    redis 127.0.0.1:6379>

第一次执行是成功的，但第二次执行相同的命令失败，原因是field 已经存在了。

### 3. hmset

同时设置hash的多个field。

    redis 127.0.0.1:6379> hmset myhash field1 Hello field2 World
    OK
    redis 127.0.0.1:6379>

### 4. hget

获取指定的hash field。

    redis 127.0.0.1:6379> hget myhash field1
    "Hello"
    redis 127.0.0.1:6379> hget myhash field2
    "World"
    redis 127.0.0.1:6379> hget myhash field3
    (nil)
    redis 127.0.0.1:6379>

由于数据库没有field3，所以取到的是一个空值`nil`。


### 5. hmget

获取全部指定的hash field。

    redis 127.0.0.1:6379> hmget myhash field1 field2 field3
    1) "Hello"
    2) "World"
    3) (nil)
    redis 127.0.0.1:6379>


由于数据库没有field3，所以取到的是一个空值`nil`。

### 6. hincrby

指定的hash filed 加上给定值。

    redis 127.0.0.1:6379> hset myhash field3 20
    (integer) 1
    redis 127.0.0.1:6379> hget myhash field3
    "20"
    redis 127.0.0.1:6379> hincrby myhash field3 -8
    (integer) 12
    redis 127.0.0.1:6379> hget myhash field3
    "12"
    redis 127.0.0.1:6379>


例中将field3 的值从20 降到了12，即做了一个减8 的操作。

### 7. hexists

测试指定field 是否存在。

    redis 127.0.0.1:6379> hexists myhash field1
    (integer) 1
    redis 127.0.0.1:6379> hexists myhash field9
    (integer) 0
    redis 127.0.0.1:6379>


通过上例可以说明field1 存在，但field9 是不存在的。

### 8. hlen

返回指定hash 的field 数量。

    redis 127.0.0.1:6379> hlen myhash
    (integer) 4
    redis 127.0.0.1:6379>


通过上例可以看到myhash 中有4 个field。

### 9. hdel

返回指定hash 的field 数量。

    redis 127.0.0.1:6379> hlen myhash
    (integer) 4
    redis 127.0.0.1:6379> hdel myhash field1
    (integer) 1
    redis 127.0.0.1:6379> hlen myhash
    (integer) 3
    redis 127.0.0.1:6379>


### 10. hkeys

返回hash 的所有field。

    redis 127.0.0.1:6379> hkeys myhash
    1) "field2"
    2) "field"
    3) "field3"
    redis 127.0.0.1:6379>


说明这个hash 中有3 个field

### 11. hvals

返回hash 的所有value。

    redis 127.0.0.1:6379> hvals myhash
    1) "World"
    2) "Hello"
    3) "12"
    redis 127.0.0.1:6379>


说明这个hash 中有3 个field

### 12. hgetall

获取某个hash 中全部的filed 及value

    redis 127.0.0.1:6379> hgetall myhash
    1) "field2"
    2) "World"
    3) "field"
    4) "Hello"
    5) "field3"
    6) "12"
    redis 127.0.0.1:6379>

可见，一下子将myhash 中所有的field 及对应的value 都取出来了。


[1]:    http://redis.io "redis"
[2]:    http://redis.io/download "redis download"