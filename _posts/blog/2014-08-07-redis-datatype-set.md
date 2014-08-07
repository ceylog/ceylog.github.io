---
layout:     post
title:      redis 整理（五）set 类型
category: blog
description: redis 相关整理（五）set 类型

---

## set 类型简介

在Redis中，set是集合，和我们数学中的集合概念相似，对集合的操作有添加删除元素，有对多个几个求交并差等操作，操作中key理解为集合的名字。Redis的set是string类型的无序集合。set元素最大可以包含(2的32次方)个元素。set的十通过hash table实现的，所以添加、删除和查找的复杂度都是0（1）。hash table会随着添加或者删除自动的调整大小。需要注意的是调整hash table大小时候需要同步（获取写锁）会阻塞其他读写操作，可能不久后就会改用跳表来实现。关于set集合类型除了基本的添加删除操作，其他有用的操作还包括集合的取并集（union），交集（intersection），差集（difference）。通过这些操作可以很容易的实现sns中的好友推荐和blog的tag功能。

## set 操作

### 1. sadd


向名称为key的set中添加元素

    redis 127.0.0.1:6379> sadd myset "hello"
    (integer) 1
    redis 127.0.0.1:6379> sadd myset "world"
    (integer) 1
    redis 127.0.0.1:6379> sadd myset "world"
    (integer) 0
    redis 127.0.0.1:6379> smembers myset
    1) "world"
    2) "hello"
    redis 127.0.0.1:6379>


本例中，我们向myset中添加了三个元素，但由于第三个元素跟第二个元素十相同的，所以第三个元素没有添加成功，最后我们用smembers来查看myset中的所有元素。


### 2. srem

删除名称为key的set中的元素member

    redis 127.0.0.1:6379> sadd myset2 "one"
    (integer) 1
    redis 127.0.0.1:6379> sadd myset2 "two"
    (integer) 1
    redis 127.0.0.1:6379> sadd myset2 "three"
    (integer) 1
    redis 127.0.0.1:6379> srem myset2 "one"
    (integer) 1
    redis 127.0.0.1:6379> srem myset2 "four"
    (integer) 0
    redis 127.0.0.1:6379> smembers myset2
    1) "three"
    2) "two"
    redis 127.0.0.1:6379>


本例中，我们向myset2中添加了三个元素后，再调用srem来删除one和four，但由于元素中没有four，所以，词条srem命令执行失败。


### 3. spop


随即返回并删除名称为key的set中一个元素

    redis 127.0.0.1:6379> sadd myset3 "one"
    (integer) 1
    redis 127.0.0.1:6379> sadd myset3 "two"
    (integer) 1
    redis 127.0.0.1:6379> sadd myset3 "three"
    (integer) 1
    redis 127.0.0.1:6379> spop myset3
    "three"
    redis 127.0.0.1:6379> smembers myset3
    1) "two"
    2) "one"
    redis 127.0.0.1:6379>


本例中，我们向myset3中添加三个元素后，在调用spop来随即删除一个元素，可以看到three元素被删除了。


### 4. sdiff


返回所有给定key与第一个key的差集

    redis 127.0.0.1:6379> smembers myset2
    1) "three"
    2) "two"
    redis 127.0.0.1:6379> smembers myset3
    1) "two"
    2) "one"
    redis 127.0.0.1:6379> sdiff myset2 myset3
    1) "three"
    redis 127.0.0.1:6379>


本例中，我们可以看到myset2中的元素与myset3中不同的只是three，所以只有three被查询出来，而不是three和one，因为one是myset3的元素。我们也可以将myset2和myset3换个顺序来看一下结果：

    redis 127.0.0.1:6379> sdiff myset3 myset2
    1) "one"
    redis 127.0.0.1:6379>


这个结果中只显示了，myset3中的元素与myset2中的不同的元素。


### 5. sdiffstore

返回所有给定key与第一个key的差集，并将结果存为另一个key

    redis 127.0.0.1:6379> smembers myset2
    1) "three"
    2) "two"
    redis 127.0.0.1:6379> smembers myset3
    1) "two"
    2) "one"
    redis 127.0.0.1:6379> sdiffstore myset4 myset2 myset3
    (integer) 1
    redis 127.0.0.1:6379> smembers myset4
    1) "three"
    redis 127.0.0.1:6379>


### 6. sinter


返回所有给定key的交集

    redis 127.0.0.1:6379> smembers myset2
    1) "three"
    2) "two"
    redis 127.0.0.1:6379> smembers myset3
    1) "two"
    2) "one"
    redis 127.0.0.1:6379> sinter myset2 myset3
    1) "two"
    redis 127.0.0.1:6379>


通过本例的结果可以看出，myset2和myset3的交集two被查出来了


### 7. sinterstore


返回所有给定key的交集，并将结果存为另一个key

    redis 127.0.0.1:6379> smembers myset2
    1) "three"
    2) "two"
    redis 127.0.0.1:6379> smembers myset3
    1) "two"
    2) "one"
    redis 127.0.0.1:6379> sinterstore myset5 myset2 myset3
    (integer) 1
    redis 127.0.0.1:6379>smembers myset5
    1) "two"
    redis 127.0.0.1:6379>


通过本例的结果可以看出，myset2和myset3的交集被保存到myset5中了。


### 8. sunion


返回所有给定key的并集

    redis 127.0.0.1:6379> smembers myset2
    1) "three"
    2) "two"
    redis 127.0.0.1:6379> smembers myset3
    1) "two"
    2) "one"
    redis 127.0.0.1:6379> sunion myset2 myset3
    1) "three"
    2) "one"
    3) "two"
    redis 127.0.0.1:6379>


通过本例的结果可以看出，myset2和myset3的并集被查出来了


### 9. sunionstore


返回所有给定key的并集，并将结果存为另一个key

    redis 127.0.0.1:6379> smembers myset2
    1) "three"
    2) "two"
    redis 127.0.0.1:6379> smembers myset3
    1) "two"
    2) "one"
    redis 127.0.0.1:6379> sunionstore myset6 myset2 myset3
    (integer) 3
    redis 127.0.0.1:6379>smembers myset6
    1) "three"
    2) "one"
    3) "two"
    redis 127.0.0.1:6379>


通过本例的结果可以看出，myset2和myset3的并集被保存到myset6中了


### 10. smove


从第一个key对应的set中一处member并添加到第二个对应set中

    redis 127.0.0.1:6379> smembers myset2
    1) "three"
    2) "two"
    redis 127.0.0.1:6379> smembers myset3
    1) "two"
    2) "one"
    redis 127.0.0.1:6379> smove myset2 myset7 three
    (integer) 1
    redis 127.0.0.1:6379>smembers myset7
    1) "three"
    redis 127.0.0.1:6379>


通过本例可以看到，myset2的three被移到myset7中了


### 11. scard


返回名称为key的set的元素个数


    redis 127.0.0.1:6379> scard myset2
    (integer) 1
    redis 127.0.0.1:6379>


通过本例可以看到，myset2的成员数量为1


### 12. sismember


测试member是否是名称为key的set的元素

    redis 127.0.0.1:6379> smembers myset2
    1) "two"
    redis 127.0.0.1:6379> sismembers myset2 two
    (integer) 1
    redis 127.0.0.1:6379>sismember myset2 one
    (integer) 0
    redis 127.0.0.1:6379>


通过本例可以看到，two是myset2的成员，而one不是。


### 13. srandmember


随机返回名称为key的set的一个元素，但是不删除元素

    redis 127.0.0.1:6379> smembers myset3
    1) "two"
    2) "one"
    redis 127.0.0.1:6379> srandmember myset3
    "two"
    redis 127.0.0.1:6379>srandmember myset3
    "one"
    redis 127.0.0.1:6379> 