---
layout:     post
title:      redis 整理（六）ZSet 类型
category: blog
description: redis 相关整理（六）ZSet 类型

---

## zset 类型简介

zset全称为sorted-sets类型,和set数据类型有极为相似,都是字符串的集合,都不允许重复的成员
出现在一个set中.两者的主要区别是zset的每一个成员都会有一个分数(score)与之关联.redis正是通过分数来为集合中的成员进行从小到大的排序.zset的成员是唯一的,但分数(score)却可以重复.
在zset中添加、删除或更新一个成员都是非常快速的操作，其时间复杂度为集合中成员数量的对数.

Sorted-Sets中的成员在集合中的位置是有序的.

## zset 操作

### 1. zadd

命令格式: zadd key score member [[score] [member] …]

描述：将一个或多个 member 元素及其 score 值加入到有序集 key 当中.如果某个 member 已经是有序集的成员，那么更新这个 member 的 score 值，并通过重新插入这个 member 元素，来保证该 member 在正确的位置上。score 值可以是整数值或双精度浮点数。如果 key 不存在，则创建一个空的有序集并执行 ZADD 操作。当 key 存在但不是有序集类型时，返回一个错误。

时间复杂度: O(M*log(N))， N 是有序集的基数， M 为成功添加的新成员的数量

返回值:被成功添加的新成员的数量，不包括那些被更新的、已经存在的成员。


#### 添加一个元素


    127.0.0.1:6379[1]> zadd zset_list 11 test1
    (integer) 1


#### 添加多个元素


    127.0.0.1:6379[1]> zadd zset_list 1 test2 2 test3
    (integer) 2


#### 查看元素


    127.0.0.1:6379[1]> zrange zset_list 0 -1
    1) "test2"
    2) "test3"
    3) "test1"


#### 查看元素带score值


    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test2"
    2) "1"
    3) "test3"
    4) "2"
    5) "test1"
    6) "11"


#### 添加已存在元素，且 score 值不变 操作不成功返回0


    127.0.0.1:6379[1]> zadd zset_list 11 test1
    (integer) 0
    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test2"
    2) "1"
    3) "test3"
    4) "2"
    5) "test1"
    6) "11"


#### 添加已存在元素，但是改变 score 值


    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test2"
    2) "1"
    3) "test3"
    4) "2"
    5) "test1"
    6) "3"


### zrem

命令格式: ZREM key member [member ...]

描述:移除有序集 key 中的一个或多个成员，不存在的成员将被忽略。当 key 存在但不是有序集类型时，返回一个错误。

时间复杂度:O(M*log(N))， N 为有序集的基数， M 为被成功移除的成员的数量。

返回值:被成功移除的成员的数量，不包括被忽略的成员。


移除单个元素

    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test2"
    2) "1"
    3) "test3"
    4) "2"
    5) "test1"
    6) "3"
    127.0.0.1:6379[1]> zrem zset_list test1
    (integer) 1
    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test2"
    2) "1"
    3) "test3"
    4) "2"


移除多个


    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test2"
    2) "1"
    3) "test3"
    4) "2"
    127.0.0.1:6379[1]> zrem zset_list test2 test3
    (integer) 2
    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    (empty list or set)


移除不存在元素


    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    (empty list or set)
    127.0.0.1:6379[1]> zrem zset_list test2
    (integer) 0
    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    (empty list or set)


### zcard

描述：返回zset集合的成员数

时间复杂度：O(1)

返回值：当 key 存在且是有序集(zset)类型时，返回集合内的成员数。不存在返回0。

    127.0.0.1:6379[1]> zcard zset_list
    (integer) 0
    127.0.0.1:6379[1]> zadd zset_list 0 test1
    (integer) 1
    127.0.0.1:6379[1]> zcard zset_list
    (integer) 1
    127.0.0.1:6379[1]> zadd zset_list 1 test2 2 test3
    (integer) 2
    127.0.0.1:6379[1]> zcard zset_list
    (integer) 3

### zcount

命令格式:ZCOUNT key min max

描述：返回有序集 key 中， score 值在 min 和 max 之间(默认包括 score 值等于 min 或 max )的成员的数量。

时间复杂度: O(log(N)+M)， N 为有序集的基数， M 为值在 min 和 max 之间的元素的数量。

返回值：score 值在 min 和 max 之间的成员的数量。

    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test1"
    2) "0"
    3) "test2"
    4) "1"
    5) "test3"
    6) "2"
    127.0.0.1:6379[1]> zcount zset_list 1 2
    (integer) 2
    127.0.0.1:6379[1]> zcount zset_list 0 2
    (integer) 3
    127.0.0.1:6379[1]> zcount zset_list 2 2
    (integer) 1 
    
### zscore

命令格式:ZSCORE key member

描述:返回有序集 key 中，成员 member 的 score 值。如果 member 元素不是有序集 key 的成员，或 key 不存在，返回 nil 。

时间复杂度:O(1)

    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test1"
    2) "0"
    3) "test2"
    4) "1"
    5) "test3"
    6) "2"
    127.0.0.1:6379[1]> zscore zset_list test2
    "1"
    127.0.0.1:6379[1]> zscore zset_list test5
    (nil)

### zincrby

命令格式：ZINCRBY key increment member

描述：为有序集 key 的成员 member 的 score 值加上增量 increment 。

时间复杂度：O(log(N))

返回值： 返回member 成员的新 score 值，以字符串形式表示。

    127.0.0.1:6379[1]> zscore zset_list test2
    "1"
    127.0.0.1:6379[1]> zincrby zset_list 2 test2
    "3"
    127.0.0.1:6379[1]> zincrby zset_list -2 test2
    "1"

### zrange

命令格式: ZRANGE key start stop [WITHSCORES]

描述：返回指定区间的成员。其中成员位置按 score 值递增(从小到大)来排序。 WITHSCORES选项是用来让成员和它的score值一并返回.(在前面我们已经用到过)

时间复杂度:O(log(N)+M)， N 为有序集的基数，而 M 为结果集的基数。

返回值：返回指定区间的成员列表.

    127.0.0.1:6379[1]> zrange zset_list 1 2
    1) "test2"
    2) "test3"
    127.0.0.1:6379[1]> zrange zset_list 0 2
    1) "test1"
    2) "test2"
    3) "test3"
    127.0.0.1:6379[1]> zrange zset_list 0 -1
    1) "test1"
    2) "test2"
    3) "test3"
    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test1"
    2) "0"
    3) "test2"
    4) "1"
    5) "test3"
    6) "2"


当给定区间不存在于有序集时的情况


    127.0.0.1:6379[1]> zrange zset_list 10 20
    (empty list or set)


### zrevrange

命令格式：ZREVRANGE key start stop [WITHSCORES]

描述：和zrange一样使用，唯一不同是其成员位置按 score 值递减(从大到小)来排列。

    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test1"
    2) "0"
    3) "test2"
    4) "1"
    5) "test3"
    6) "2"
    127.0.0.1:6379[1]> zrevrange zset_list 0 -1 withscores
    1) "test3"
    2) "2"
    3) "test2"
    4) "1"
    5) "test1"
    6) "0"

### zrangebyscore

命令格式：ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]

描述：返回有序集key中所有score值介于min与max之间(包括等于)的成员.成员按score值递增(从小到大)排列 。min 和 max 可以是 -inf 和 +inf可选limit参数指定返回结果的数量及区间。

时间复杂度：O(log(N)+M)， N 为有序集的基数， M 为被结果集的基数。

返回值：指定区间内，带有 score 值(可选)的有序集成员的列表。

    127.0.0.1:6379[1]> zrangebyscore zset_list -inf +inf
    1) "test1"
    2) "test2"
    3) "test3"
    127.0.0.1:6379[1]> zrangebyscore zset_list -inf +inf withscores
    1) "test1"
    2) "0"
    3) "test2"
    4) "1"
    5) "test3"
    6) "2"
    127.0.0.1:6379[1]> zrangebyscore zset_list 1 +inf withscores
    1) "test2"
    2) "1"
    3) "test3"
    4) "2"

显示大于2 小于等于10的成员

    127.0.0.1:6379[1]> zrangebyscore zset_list -inf +inf withscores
    1) "test1"
    2) "0"
    3) "test2"
    4) "1"
    5) "test3"
    6) "2"
    127.0.0.1:6379[1]> zrangebyscore zset_list (1 10
    1) "test3

显示条件 1 < score < 10 的成员

    127.0.0.1:6379[1]> zrangebyscore zset_list (1 (10
    1) "test3"

### zrevrangebyscore

命令格式: zrevrangebyscore key max min [WITHSCORES] [LIMIT offset count]

描述：和zrangebyscore一样，唯一不同的是成员按 score 值递减(从大到小)的次序排列。

    127.0.0.1:6379[1]> zrangebyscore zset_list -inf +inf withscores
    1) "test1"
    2) "0"
    3) "test2"
    4) "1"
    5) "test3"
    6) "2"
    127.0.0.1:6379[1]> zrevrangebyscore zset_list +inf -inf withscores
    1) "test3"
    2) "2"
    3) "test2"
    4) "1"
    5) "test1"
    6) "0"

### zrank
命令格式： zrank key member

描述：返回有序集key中成员member的排名。成员按 score 值递增(从小到大)顺序排列。排名以0开始，也就是说score 值最小的为0.

时间复杂度:O(log(N))

返回值：返回成员排名，member不存在返回nil.

    127.0.0.1:6379[1]> zrank zset_list test1
    (integer) 0

### zrevrank

命令格式： zrevrank key member

描述：返回有序集key中成员member的排名。成员按 score 值递增(从大到小)顺序排列。排名以0开始，也就是说score 值最大的为0.

时间复杂度:O(log(N))

返回值：返回成员排名，member不存在返回nil.

    127.0.0.1:6379[1]> zrevrank zset_list test3
    (integer) 0

### zremrangebyrank

命令格式: ZREMRANGEBYRANK key start stop

描述：移除有序集 key 中，指定排名(rank)区间内的所有成员。区间分别以下标参数 start 和 stop 指出，包含 start 和 stop 在内。下标参数 start 和 stop 都以 0 为底，也就是说，以 0 表示有序集第一个成员，以 1 表示有序集第二个成员，以此类推。你也可以使用负数下标，以 -1 表示最后一个成员， -2 表示倒数第二个成员，以此类推。

时间复杂度:O(log(N)+M)， N 为有序集的基数，而 M 为被移除成员的数量。

返回值:被移除成员的数量。

    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test1"
    2) "0"
    3) "test2"
    4) "1"
    5) "test3"
    6) "2"
    127.0.0.1:6379[1]> zremrangebyrank zset_list 1 2
    (integer) 2
    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test1"
    2) "0"

### zremrangebyscore

命令格式：zremrangebyscore key min max

描述：移除score值介于min和max之间（等于）的成员

时间复杂度:O(log(N)+M)， N 为有序集的基数，而 M 为被移除成员的数量。

返回值:被移除成员的数量。

移除所有score在 100 到 110 内的数据

    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test1"
    2) "0"
    3) "test11"
    4) "100"
    5) "test22"
    6) "101"
    127.0.0.1:6379[1]> zremrangebyscore zset_list 100 110
    (integer) 2
    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test1"
    2) "0"


### zunionstore

命令格式：ZUNIONSTORE destination numkeys key [key ...] [WEIGHTS weight [weight ...]] [AGGREGATE SUM|MIN|MAX]

描述：计算给定的一个或多个有序集的并集，其中给定 key 的数量必须以 numkeys 参数指定，并将该并集(结果集)储存到 destination 。默认情况下，结果集中某个成员的 score 值是所有给定集下该成员 score 值之 和 。

    127.0.0.1:6379[1]> zadd zset1 1 test1 2 test2
    (integer) 2
    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test1"
    2) "0"
    127.0.0.1:6379[1]> zadd zset_list 1 test2
    (integer) 1
    127.0.0.1:6379[1]> zunionstore dis_set 2 zset_list zset1
    (integer) 2
    127.0.0.1:6379[1]> zrange dis_set 0 -1 withscores
    1) "test1"
    2) "1"
    3) "test2"
    4) "3"
    127.0.0.1:6379[1]> zrange zset1 0 -1 withscores
    1) "test1"
    2) "1"
    3) "test2"
    4) "2"
    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test1"
    2) "0"
    3) "test2"
    4) "1"
    127.0.0.1:6379[1]> zadd zset_list 3 test3
    (integer) 1
    127.0.0.1:6379[1]> zunionstore dis_set1 2 zset_list zset1
    (integer) 3
    127.0.0.1:6379[1]> zrange dis_set1 0 -1 withscores
    1) "test1"
    2) "1"
    3) "test2"
    4) "3"
    5) "test3"
    6) "3"

### zinterstore

命令格式：ZINTERSTORE destination numkeys key [key ...] [WEIGHTS weight [weight ...]] [AGGREGATE SUM|MIN|MAX]

描述：计算给定的一个或多个有序集的交集。其中给定 key 的数量必须以 numkeys 参数指定，并将该交集(结果集)储存到 destination 。默认情况下，结果集中某个成员的 score 值是所有给定集下该成员 score 值之 和 。

时间复杂度：O(N*K)+O(M*log(M))， N 为给定 key 中基数最小的有序集， K 为给定有序集的数量， M 为结果集的基数。

返回值：保存到 destination 的结果集成员数。

    127.0.0.1:6379[1]> zrange zset1 0 -1 withscores
    1) "test1"
    2) "1"
    3) "test2"
    4) "2"
    127.0.0.1:6379[1]> zrange zset_list 0 -1 withscores
    1) "test1"
    2) "0"
    3) "test2"
    4) "1"
    5) "test3"
    6) "3"
    127.0.0.1:6379[1]> zinterstore dis_set2 2 zset1 zset_list
    (integer) 2
    127.0.0.1:6379[1]> zrange dis_set2 0 -1 withscores
    1) "test1"
    2) "1"
    3) "test2"
    4) "3"