#### note-redis
redis是非关系型内存键值数据库,可存储键和五种不同类型的值之间的映射。

###### 五种数据类型:
String : 字符串
List : 列表
Set : 无序集合
Zset : 有序集合
Hash : 无序散列表

#### String
```
> set hello world
OK
> get hello
"world"
> del hello
(integer) 1
> get hello
(nil)
```

#### List
```
//往列表中添加元素

> rpush list-key item
(integer) 1
> rpush list-key item2
(integer) 2
> rpush list-key item
(integer) 3

//遍历列表 

> lrange list-key 0 -1
1) "item"
2) "item2"
3) "item"

//通过下标查询列表中的元素

> lindex list-key 1
"item2"

//删除列表中一个元素（从末尾删除）

> lpop list-key
"item"
> lrange list-key 0 -1
1) "item2"
2) "item"
```

#### Set
```
//往无序集合set中添加元素

> sadd set-key item
(integer) 1
> sadd set-key item2
(integer) 1
> sadd set-key item3
(integer) 1

//添加失败，无序集合中不能出现相同的值
> sadd set-key item
(integer) 0


//遍历无序集合set

> smembers set-key
1) "item"
2) "item2"
3) "item3"

//验证某个元素是否存在无序集合set-key中

> sismember set-key item4
(integer) 0
> sismember set-key item
(integer) 1

//删除无序集合中某个元素

> srem set-key item2
(integer) 1
> srem set-key item2
(integer) 0

> smembers set-key
1) "item"
2) "item3"
```

#### Zset
```
//往有序集合zset中添加元素

> zadd zset-key 728 member1
(integer) 1
> zadd zset-key 982 member0
(integer) 1
//添加失败，无法添加值相同的元素
> zadd zset-key 982 member0
(integer) 0

//遍历有序集合的键值对

> zrange zset-key 0 -1 withscores
1) "member1"
2) "728"
3) "member0"
4) "982"

//遍历有序集合的键
> zrange zset-key 0 -1
1) member1
2) member0

//通过序号(分数)查找元素
> zrangebyscore zset-key 0 800 withscores
1) "member1"
2) "728"

//删除某个元素
> zrem zset-key member1
(integer) 1
> zrem zset-key member1
(integer) 0
> zrange zset-key 0 -1 withscores
1) "member0"
2) "982"
```

#### Hash
```
//往散列表中添加元素

> hset hash-key sub-key1 value1
(integer) 1
> hset hash-key sub-key2 value2
(integer) 1
//虽然显示失败，但sub-key1 的值已被改变(若把value1改成value3)
> hset hash-key sub-key1 value1
(integer) 0

//遍历散列表

> hgetall hash-key
1) "sub-key1"
2) "value1"
3) "sub-key2"
4) "value2"

//根据键删除某个元素
> hdel hash-key sub-key2
(integer) 1
> hdel hash-key sub-key2
(integer) 0

//根据键获取对应的值 
> hget hash-key sub-key1
"value1"


> hgetall hash-key
1) "sub-key1"
2) "value1"
```

##### 键的过期时间
Redis 可以为每个键设置过期时间，当键过期时，会自动删除该键。

对于散列表这种容器，只能为整个键设置过期时间（整个散列表），而不能为键里面的单个元素设置过期时间。

过期时间对于清理缓存数据非常有用。


##### 发布与订阅
发布与订阅实际上是观察者模式，订阅者订阅了频道之后，发布者向频道发送字符串消息会被所有订阅者接收到。

发布与订阅有一些问题，很少使用它，而是使用替代的解决方案。问题如下：

1、如果订阅者读取消息的速度很慢，会使得消息不断积压在发布者的输出缓存区中，造成内存占用过多；
2、如果订阅者在执行订阅的过程中网络出现问题，那么就会丢失断线期间发送的所有消息。
