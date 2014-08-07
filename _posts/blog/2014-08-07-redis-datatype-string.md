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

### 1. set

设置Key对应的值为string类型的value。

例如设置一个name = zhangsan 的键值对，可以这样做：

    $ redis-cli
    redis 127.0.0.1:6379> set name zhangsan
    OK
    redis 127.0.0.1:6379> get name
    "zhangsan"
    redis 127.0.0.1:6379>

### 2. setnx

设置key 对应的值为string 类型的value。如果key 已经存在，返回0，nx 是not exist 的意思。

例如添加一个name= lisi 的键值对，可以这样做:

    redis 127.0.0.1:6379> setnx name lisi
    (integer) 0
    redis 127.0.0.1:6379> get name
    "lisi"
    redis 127.0.0.1:6379>

由于原来name 有一个对应的值，所以本次的修改不生效，且返回码是0。

### 3. setex

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

### 4. setrange

设置指定键的值的子字符串。

例如：我现在要把 gang 的邮箱 finalsin@foxmail.com 改为 finalsin@sina.com

    redis 127.0.0.1:6379> get gang
    "finalsin@foxmail.com"
    redis 127.0.0.1:6379> setrange gang 9 sina.com
    (integer) 21
    redis 127.0.0.1:6379> get gang
    "finalsin@sina.com"
    redis 127.0.0.1:6379>

### 5. mset

同时设置多个键值对，成功返回ok表示所有键设置成功，失败则返回0表示所有键设置都不成功

例如：同时设置 王刚 的多个爱好：

    redis 127.0.0.1:6379> mset hobby1 music hobby2 sport hobby3 girl
    OK
    redis 127.0.0.1:6379> get hobby1
    "music"
    redis 127.0.0.1:6379> get hobby3
    "girl"
    redis 127.0.0.1:6379>

### 6. msetnx

同时设置多个键值对，同样失败返回0表示所有键都没有设置成功，成功返回OK表示所有键都设置成功，但不同于mset的是本方法不会设置已经存在的键值对！

    redis 127.0.0.1:6379> get hobby1
    "music"
    redis 127.0.0.1:6379> get hobby3
    "girl"
    redis 127.0.0.1:6379> msetnx hobby1 tingge hobby4 hejiu
    (integer) 0
    redis 127.0.0.1:6379> get hobby4
    (nil)
    redis 127.0.0.1:6379> get hobby3
    "girl"

可以看出如果这条命令返回0，那么里面操作都会回滚，都不会被执行。

### 7. get

获取某个键对应的值，如果不存在则返回 `nil`

    redis 127.0.0.1:6379> get hobby4
    (nil)
    redis 127.0.0.1:6379> get hobby3
    "girl"

如上例，hobby3存在，并且值为"girl"，hobby4不存在，则返回 `nil`

### 8. getset

设置某一个键的值，并且返回该键的旧值，如果该键不存在，则返回 `nil`，然后再设置新的值

例如：

    redis 127.0.0.1:6379> getset hobby4 football
    (nil)
    redis 127.0.0.1:6379> getset hobby3 basketball
    "girl"
    redis 127.0.0.1:6379> get hobby4
    "football"
    redis 127.0.0.1:6379> get hobby3
    "basketball"

### 9. getrange

获取指定键的值的子字符串

例如：

    redis 127.0.0.1:6379> getrange gang 0 7
    "finalsin"
    redis 127.0.0.1:6379> getrange gang -8 -1
    "sina.com"
    redis 127.0.0.1:6379> getrange gang 0 100
    "finalsin@sina.com"

上例中，getrange jiege 0 4 表示获取 jiege 这个键的值的下标为 0~4 的所有字符

同样，getrange jiege -15 -1 表示获取 jiege 这个键的值的下标从后数第15个到最后一个的所有字符

而 getrange jiege 0 100 则表示全部输出 jiege 这个键的值的所有字符，因为最后一个字符的下标 小于 100，

当下标超出字符串长度时，将默认为是同方向的最大下标。

### 10. mget

一次性获取多个键的值，如果键不存在，则返回 `nil`

例如：

    redis 127.0.0.1:6379> mget hobby1 hobby2 hobby5
    1) "music"
    2) "sport"
    3) (nil)
    redis 127.0.0.1:6379>

hobby5不存在，所以返回`nil`。

### 11. incr

对一个键的值做加加操作，并返回新的值，如果该键的值类型不是int类型，将会报错，如果该键不存在，则设置该键为1

例如：

    redis 127.0.0.1:6379> set age 20
    OK
    redis 127.0.0.1:6379> incr age
    (integer) 21
    redis 127.0.0.1:6379> get age
    "21"
    redis 127.0.0.1:6379> get age1
    (nil)
    redis 127.0.0.1:6379> incr age1
    (integer) 1
    redis 127.0.0.1:6379> get age1
    "1"

### 12. incrby

类似于incr，但是incrby可以指定增加的值

例如：

    redis 127.0.0.1:6379> incrby age 5
    (integer) 26
    redis 127.0.0.1:6379> get age
    "26"
    redis 127.0.0.1:6379> incrby age -1
    (integer) 25
    redis 127.0.0.1:6379> get age
    "25"

可以看到，5代表给age键增加5，而-1表示给age键减1，即正数为加，负数为减

### 13. decr

对某一个键做减减操作，同incr

例如：

    redis 127.0.0.1:6379> set age 20
    OK
    redis 127.0.0.1:6379> decr age
    (integer) 19
    redis 127.0.0.1:6379> get age
    "19"
    redis 127.0.0.1:6379> get age1
    (nil)
    redis 127.0.0.1:6379> decr age1
    (integer) -1
    redis 127.0.0.1:6379> get age1
    "-1"

### 14. decrby

同incrby，给某一键减去指定的值

    redis 127.0.0.1:6379> decrby age 5
    (integer) 14
    redis 127.0.0.1:6379> get age
    "14"

decrby 完全是为了可读性，我们完全可以通过incrby 一个负值来实现同样效果，反之一样。

### 15. append

给指定key 的字符串值追加value,返回新字符串值的长度。

例如：

    redis 127.0.0.1:6379> set name wanggang
    OK
    redis 127.0.0.1:6379> get name
    "wanggang"
    redis 127.0.0.1:6379> append name @wg.org
    (integer) 15
    redis 127.0.0.1:6379> get name
    "wanggang@wg.org"


### 16. strlen

取指定key 的value 值的长度。

例如：

    redis 127.0.0.1:6379> get name
    "wanggang@wg.org"  
    redis 127.0.0.1:6379> strlen name
    (integer) 15
    redis 127.0.0.1:6379> get age
    "20"
    redis 127.0.0.1:6379> strlen age
    (integer) 2

好了，这就是String类型的所有操作
