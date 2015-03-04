---
layout:     post
title:      redis 整理（七）key（键）的相关操作
category: blog
description: redis 相关整理（七）key（键）的相关操作

---


### del

时间复杂度: String类型其时间复杂度为O(1)。 其余O(N) N表删除key数量.

描述：删除

返回值: 实际被删除的KEY数量

操作命令如下:

    redis 127.0.0.1:6379> del my_set_diff my_set
    (integer) 2

### keys

命令格式: keys pattern

描述: 获取所有匹配PATTERN参数的keys.

时间复杂度: O(N) 表KEY的数量.

返回值:匹配模式的键列表。

操作命令如下:

    redis 127.0.0.1:6379> keys my*
    1) “my_set_2″
    2) “myset2″
    3) “myset5″
    4) “myset”

### exists

描述: 判断指定键是否存在。1存在,0不存在.

时间复杂度:O(1)

操作命令如下:

    redis 127.0.0.1:6379> exists myset
    (integer) 1
    redis 127.0.0.1:6379> exists myset9
    (integer) 0

### move

命令格式: move key db

时间复杂度:O(1)

描述: 将当前数据库中指定的键Key移动到参数中指定的数据库中。如果该Key在目标数据库中已经存在，或者在当前数据库中并不存在，该命令将不做任何操作并返回0。

返回值:成功1 否则0

### rename

命令格式: rename key newkey

时间复杂度:O(1)

描述: 重命名key,key不存在将报错,如果newKey存在则直接覆盖

### renamenx

命令格式: renamenx key newkey

时间复杂度:O(1)

描述:当newkey不存在时才重命名.

返回值: 成功返回1,如里newkey已经存在,返回0.

### persist

命令格式:persist key

时间复杂度: O(1)

描述:移除给定key的生存时间。

返回值:1表示Key的过期时间被移出，0表示该Key不存在或没有过期时间。

### expire

命令格式:EXPIRE key seconds

时间复杂度: O(1)

描述:为给定key设置生存时间。当key过期时，它会被自动删除

返回值:设置成功返回1。当key不存在或者不能为key设置生存时间，返回0。

### expireat

命令格式:expireat key timetamp

时间复杂度: O(1)

描述:EXPIREAT的作用和EXPIRE一样，都用于为key设置生存时间。不同在于EXPIREAT命令接受的时间参数是UNIX时间戳(unix timestamp)。

返回值:设置成功返回1。当key不存在或者不能为key设置生存时间，返回0。

### ttl

命令格式:ttl key

时间复杂度: O(1)

描述:返回给定key的剩余生存时间(time to live)(以秒为单位)。

返回值:key的剩余生存时间(以秒为单位)。当key不存在或没有设置生存时间时，返回-1 。


### randomkey

时间复杂度: O(1)

描述:随机的返回一个Key。

返回值:返回的随机键，如果该数据库是空的则返回nil。

### type

命令格式:type key

时间复杂度: O(1)

描述:获取key的类型

返回值: 返回的字符串为string、list、set、hash和zset，如果key不存在返回none。


### dump

命令格式:dump key

可用版本:reidis>=2.6.0

时间复杂度: O(1)

描述:序列化给定key,并返回被序列化的值,使用 RESTORE 命令可以将这个值反序列化为 Redis 键。

返回值: 如key不存在返回nli,否则返回被序列化的值.
