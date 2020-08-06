# Redis

C 语言编写的，非关系型数据库，KV 数据库。

内存数据库，可以持久化。

用于数据库，缓存，消息中间件。

#### Redis 与 memcached
1) redis 支持 string，list 链表，set，zset 有序集合，hash 存储；memcached 只支持 string
2) redis 支持数据备份 master-slave 模式
3) redis 支持数据持久化，启动时加载再次使用；memcached 数据全放在内存。
4) redis 速度快
5) memcached 是多线程，redis 是单线程的，数据顺序进行，适合小数据，单条数据过大 IO 等待时间长。


#### 优点
1) 速度快
2) 数据类型丰富
3) 支持事务，不支持 rollback


#### 内存回收算法/数据淘汰策略

redis 内存使用达到某个阈值，数据淘汰机制。

noeviction：当内存不足以容纳新写入数据时，新写入操作会报错。默认策略
allkeys-lru：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的 key。
allkeys-random：当内存不足以容纳新写入数据时，在键空间中，随机移除某个 key。
volatile-lru：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，移除最近最少使用的 key。
volatile-random：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个 key。
volatile-ttl：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的 key 优先移除。







