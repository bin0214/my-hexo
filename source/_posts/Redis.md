---
title: Redis
date: 2022-01-010 00:00:00
categories:
- Redis
tags:
- Redis
---



## Redis概述

- Redis是一个开源的**key-value**存储系统
- 和Memcached类似，它支持存储value类型相对更多，包括string（字符串）、**list**（链表）、**set**（集合）、**zset**（sorted set --有序集合）和**hash**（哈希类型）。
- 这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是**原子性**的。
- 再此基础上，Redis支持各种不同方式的排序
- 与memcached一样，为了保证效率，数据都是**缓存在内存**中
- 区别的是Redis会**周期性**的把更新的**数据写入磁盘**或者把修改操作写入追加的记录文件
- 并且在此基础上实现**master-slave（主从）**同步

### Redis介绍

- 默认16个数据库，类型数组下标从0开始，**初始默认使用0号库**

- 使用命令select  &lt;dbid&gt; 来切换数据库，如select 8

- 统一密码管理，所以库同样密码

- **dbsiez** 查看当前数据库的key的数量

- **flushdb 清空当前库**

- **fluahall 通杀全部库**



Redis是单线程+多路IO复用

多路复用是指使用一个线程来检查多个文件描述符（Socket）的就绪状态，比如调用select和poll函数，传入多个文件的描述符，如果有一个文件描述符就绪，则返回，否则阻塞直到超时。得到就绪状态后进行真正的操作可以在同一个线程里执行，也可以启动线程执行（比如使用线程池）

## 常用五大数据类型

### Redis键（key）

- keys * ——查看当前库所有key（匹配：keys *1） 

- exists key ——判断某个key是否存在

- type key ——查看key是什么类型

- del key ——删除指定的key数据

- unlink key ——根据value选择非阻塞删除
  - 仅将keys从keyspace元数据中删除，真正的删除会在后续异步操作
- expire key 10 ——10秒钟：为给定的key设置过期时间
- ttl kek ——查看还有多少秒过期，-1表示永不过期，-2表示以过期
- select 命令切换数据库

- dbsiez ——查看当前数据库的key的数量

- flushdb ——清空当前库

- fluahall ——通杀全部库

### Redis字符串（String）

#### 简介

String是Redis最基本的类型，一个key对应一个value

String类型是二进制安全的。意味着Redis的string可以包含任何数据。比如jpg图片或者序列化的对象

String是Redis最基本的类型，一个Redis字符串value最多可以是**512m**

#### 常用命令

- set &lt;key&gt;&lt;value&gt;——添加键值对
- get &lt;key&gt; ——查询键值对
- append &lt;key&gt;&lt;value&gt; ——将给定的&lt;value&gt;追加到原址的末尾
- strlen  &lt;key&gt;——获取值的长度
- setnx &lt;key&gt;&lt;value&gt;——只有在key不存在时，设置key的值
- incr &lt;key&gt; ——将key中存储的数字值增1，只能对数字值操作，如果为空，新增值为1
- decr &lt;key&gt; ——将key中存储的数字值减1，只能对数字值操作，如果为空，新增值为-1
- incrby/decrby &lt;key&gt;&lt;步长&gt;——将key中存储的数字值增减。自定义步长
- mset &lt;key1&gt;&lt;value1&gt;&lt;key2&gt;&lt;value2&gt;......——同时设置一个或多个key-value对
- mget &lt;key1&gt;&lt;key2&gt;&lt;key3&gt;......——同时获取一个或多个value
- msetnx &lt;key1&gt;&lt;value1&gt;&lt;key2&gt;&lt;value2&gt;......——同时设置一个或多个key-value对，当且仅当所有给定key都不存在。（**原子性，有一个失败则都失败**）
- getrange &lt;key&gt;&lt;起始位置&gt;&lt;结束位置&gt;——获取知道范围，类似Java中ubstring，前包，后包
- setrange &lt;key&gt;&lt;起始位置&gt;&lt;value&gt;——用&lt;value&gt;覆写所存储的字符串值，从&lt;起始位置&gt;开始（索引从0开始）
- setex&lt;key&gt;&lt;过期时间&gt;&lt;value&gt;——设置键值的同时，设置过期时间，单位秒
- getset&lt;key&gt;&lt;value&gt;以旧换新，设置新值的同时获取旧值

### Redis列表（list）

#### 简介

单键多值

Redis列表是简单的字符串列表，按照插入顺序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。

他的底层实际是个双向链表，对两端的操作性能很高，通过索引下标的操作中间的节点性能会比较差。

#### 常用命令

- lpush/rpush &lt;key&gt;&lt;value1&gt;&lt;value2&gt;&lt;value3&gt;……——从左边/右边插入一个或多个值。

- lpop/rpop &lt;key&gt;——从左边/右边吐出一个值。**值在键在，值光键亡。**

- rpoplpush &lt;key1&gt;&lt;key2&gt;——从&lt;key1&gt;列表右边吐出一个值，插到&lt;key2&gt;列表右边

- lrange &lt;key&gt;&lt;start&gt;&lt;stop&gt;——按照索引下标获得元素（从左到右）

  例子：lrange mylist 0 -1——0左边第一个，-1右边第一个，（0-1表示获取所有）

- lindex &lt;key&gt;&lt;index&gt;——按照索引下标获得元素（从左到右）

- llen &lt;key&gt; ——获得列表长度

- linsert &lt;key&gt; before &lt;value&gt;&lt;newvalue&gt;——在&lt;value&gt;的后面插入&lt;newvalue&gt;插入值 

- lrem &lt;key&gt;&lt;n&gt;&lt;value&gt; ——从左边删除n个value（从左到右）

- lset&lt;key&gt;&lt;index&gt;&lt;value&gt;——将列表key下标为index的值替换成value

### Redis集合（set）

#### 简介

Redis set对外提供的功能与list类似是一个列表的功能，特色之处在于set是可以自动排重的，当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供。

Redis的Set是string类型的**无序集合，他底层其实是一个value为null的hash表**，所以添加，删除，查找的**复杂度都是O(1)**

一个算法，随着数据的增加，执行时间的铲断，如果是O(1)，数据增加，查找数据的时间不变

#### 常用命令

- sadd &lt;key&gt;&lt;value1&gt;&lt;value2&gt;……——将一个或多个member元素加入到集合key中，已经存在的memeber元素将被忽悠

- smembers &lt;key&gt; ——去取出该集合的所有值

- sismember &lt;key&gt;&lt;value&gt; ——判断集合&lt;key&gt;是否含有&lt;value&gt;值，有1，没有0

- scard &lt;key&gt;——返回该集合的元素个数

- srem &lt;key&gt;&lt;value1&gt;&lt;value2&gt;——删除集合中的某个元素

- spop &lt;key&gt;——**随机从该集合中吐出一个值**

- srandmember &lt;key&gt; &lt;n&gt;——随机从集合中取出n和值，不会从集合中删除

- smove &lt;source&gt;&lt;estination&gt;value——把集合中一个值从一个集合移动到另外一个集合

- sinter &lt;key1&gt;&lt;key2&gt;——返回两个集合的**交集**元素

- sumion &lt;key1&gt;&lt;key2&gt;——返回两个集合的**并集**元素

- sdiff &lt;key1&gt;&lt;key2&gt;——返回两个集合的**差集**元素(key1中的，不包含key2中的)

### Redis哈希（hash）

#### 简介

Redis hash是一个键值对集合

Redis hash是一个string类型的field和value的映射表，hash特别合适用于储存对象。类似Java里面的Map&lt;String,Object&gt;

#### 常用命令

- hset &lt;key&gt;&lt;field&gt;&lt;value&gt;——给&lt;key&gt;集合中的&lt;field&gt;键赋值&lt;value&gt;

- hget  &lt;key1&gt;&lt;field&gt;——从&lt;key1&gt;集合&lt;field&gt;取出value

- hmset  &lt;key1&gt;&lt;field1&gt;&lt;value1&gt;&lt;field2&gt;&lt;value2&gt;……——批量设置hash的值

- hexists &lt;key1&gt;&lt;field&gt;——查看哈希表key中，给定域field是否存在.

- hkeys &lt;key&gt;——列出该hash集合的所有field

- hvals &lt;key&gt;——列出该hash集合的所有value

- hincrby &lt;key&gt;&lt;field&gt;&lt;increment&gt;——为哈希表key的域field的值添加1 -1

- hsetnx &lt;key&gt;&lt;field&gt;&lt;value&gt;——将哈希表key中的域设置为value，当且仅当域field不存在

### Redis有序集合（zset）

#### 简介

Redis有序集合zset与普通集合set非常相似，是一个没有重复元素的字符串集合。

不同之处是有序集合的每一个成员都关联一个**评分（score）**,这个评分（score）被用来按照从最低分到最高分的方式排序集合中的成员。集合的成员是唯一的，但是评分可以是重复的。

因为元素是有序的，所以你也可以很快的根据评分（score）或者次序（position）来获取一个范围的元素。

访问有序集合的中间元素也是非常快的，因此你能够使用有序集合作为一个没有重复成员的智能列表

#### 常用命令

- zadd <key><score1><value1><score2><value2>……——将一个或多个member元素及其score值加入

- zrange <key><satrt><stop> [withscores]——返回有序集合key中，下标在<satrt><stop>之间的元素，带withscores可以让分数一起和值返回到结果集

- zrangebyscore <key><min><max> [withscores]——返回有序集key，所以score值介与min和max之间（包括等于min或max）的成员。有序集合成员按score值递增（从小到大）次序排序

- zrevrangebyscore <key><min><max>[withscores]——同上，改变从大到小排序

- zincrby <key><incrment><value>——为元素的score加上增量

- zrem <key><value>——删除该集合下，指定值的元素

- zcount <key><min><max>——统计该集合，分数区间内的元素个数

- zrank <key><value>——返回该值在集合中的排名，从0开始

## 发布与订阅

### 什么是发布和订阅

Redis发布订阅（pub/sub）是一种消息通信模式：发送者（pub）发送消息，订阅者（sub）接收消息

Redis客户端可以订阅任意数量的频道

### Redis的发布和订阅

1. 客户端可以订阅频道如下图

<img src="/img/Redis-05-01.png">

2. 当给这个频道发送消息后，消息就会发送给订阅的客户端

<img src="/img/Redis-05-02.png">

### 发布订阅命令行实现

1. 打开一个客户端订阅channel1

   SUBSCRIBE channel1

<img src="/img/Redis-05-03.png">

2. 打开另一个客户端，给channel1发布消息hello

   publish channel1 hello

<img src="/img/Redis-05-04.png">

3. 打开第一个客户端可以看到发送的消息

<img src="/img/Redis-05-05.png">

注：发布的消息没有持久化，如果在订阅的客户端收不到hello，只能收到订阅后发布的消息

## Redis新数据类型

### Bitmaps

#### 简介

现代计算机用二进制(位)作为信息的基础单位，1个字节等于8位，比如“abc”字符串是由3个字节组成，但时实际在计算机存储时将其用二进制表示，“abc”分别对应ASCII码分别是97、 98、 99，对应的二进制分别是01100001、 01100010和01100011，如下图

<img src="/img/Redis-06-01.png">

合理地使用操作位能够有效地提高内存使用率和开发效率。

Redis提供了Bitmaps这个“数据类型”可以实现对位的操作：

1. Bitmaps本身不是一种数据类型，实际上它就是字符串（key-value） ， 但是它可以对字符串的位进行操作
2. Bitmaps单独提供了一套命令，所以Redis中使用Bitmaps和使用字符串的方法不太相同。可以把bitmaps想象成一个以位为单位的数组，数组的每个单元只能存储0和1，数组下标在Bitmaps中叫做偏移量

> offset:偏移量从0开始

<img src="/img/Redis-06-02.png">

#### 命令

- setbit <key><offset><value> ——设置Bitmaps中某个偏移量的值（0和1）

- getbit <key><offset>——获取Bitmaps中某个偏移量的值
- bitcount <key>[start end]——统计字符串从start字节到end字节字符比特值为1的数量
- bitop and(or/not/xor) <destkey> [key…] ——bitop是一个复合操作， 它可以做多个Bitmaps的and（交集） 、 or（并集） 、 not（非） 、 xor（异或） 操作并将结果保存在destkey中。

### HyperLogLog

#### 简介

在工作当中，我们经常会遇到与统计相关的功能需求，比如统计网站PV（PageView页面访问量）,可以使用Redis的incr、incrby轻松实现。

但像UV（UniqueVisitor，独立访客）、独立IP数、搜索记录数等需要去重和计数的问题如何解决？这种求集合中不重复元素个数的问题称为基数问题。
解决基数问题有很多种方案：

1. 数据存储在MySQL表中，使用distinct count计算不重复个数
2. 使用Redis提供的hash、set、bitmaps等数据结构来处理

以上的方案结果精确，但随着数据不断增加，导致占用空间越来越大，对于非常大的数据集是不切实际的。

能否能够降低一定的精度来平衡存储空间？Redis推出了HyperLogLog

Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的。

在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

什么是基数?

比如数据集 {1, 3, 5, 7, 5, 7, 8}， 那么这个数据集的基数集为 {1, 3, 5 ,7, 8}, 基数(不重复元素)为5。 基数估计就是在误差可接受的范围内，快速计算基数。

#### 命令

- pfadd <key>< element> [element ...] —— 添加指定元素到 HyperLogLog 中
- pfcount<key> [key ...] ——计算HLL的近似基数，可以计算多个HLL，比如用HLL存储每天的UV，计算一周的UV可以使用7天的UV合并计算即可
- pfmerge<destkey><sourcekey> [sourcekey ...]  ——将一个或多个HLL合并后的结果存储在另一个HLL中，比如每月活跃用户可以使用每天的活跃用户来合并计算可得

### Geospatial

#### 简介

Redis 3.2 中增加了对GEO类型的支持。GEO，Geographic，地理信息的缩写。该类型，就是元素的2维坐标，在地图上就是经纬度。redis基于该类型，提供了经纬度设置，查询，范围查询，距离查询，经纬度Hash等常见操作。

#### 命令

- geoadd<key>< longitude><latitude><member> [longitude latitude member...] ——添加地理位置（经度，纬度，名称）
- geopos  <key><member> [member...]  ——获得指定地区的坐标值
- geodist<key><member1><member2>  [m|km|ft|mi ] —— 获取两个位置之间的直线距离

> m 表示单位为米[默认值]。
>
> km 表示单位为千米。
>
> mi 表示单位为英里。
>
> ft 表示单位为英尺。

- georadius<key>< longitude><latitude>radius m|km|ft|mi  ——以给定的经纬度为中心，找出某一半径内的元素

## Redis Jedis 测试

### Jedis所需要的jar包

```java
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.2.0</version>
</dependency>
```

>  连接Redis注意事项
>
> redis.conf中注释掉bind 127.0.0.1 ,然后 protected-mode no

### 测试案例

#### 连接Redis

```java
    public static void main(String[] args) {
        //创建Jedis对象
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        //测试连接
        String ping = jedis.ping();
        System.out.println("连接成功"+ping);
        //关闭连接
        jedis.close();
    }
```

#### 测试key和String

```java
    @Test
    public void testKeyAndString(){
        //创建Jedis对象
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        //添加
        jedis.set("name0","lucy");
        jedis.set("name1","lucky");
        jedis.set("name2","Tom");
        String name = jedis.get("name0");
        System.out.println(name);


        Set<String> keys = jedis.keys("*");
        for (String key:keys){
            System.out.println(key);
        }
    }
```

#### 测试List

```java
    @Test
    public void testList(){
        //创建Jedis对象
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        jedis.lpush("names","lucy","tom","lucky","simple");
        List<String> names = jedis.lrange("names", 0, -1);
        System.out.println(names);
    }
```

#### 测试Set

```java
	@Test
    public void testSet(){
        //创建Jedis对象
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        jedis.sadd("orders","orders1","orders2");
        Set<String> smembers = jedis.smembers("orders");
        for (String order : smembers) {
            System.out.println(order);
        }
    }
```

#### 测试hash

```java
	@Test
 	public void testHash(){
        //创建Jedis对象
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        jedis.hset("hash1","userName","lisi");
        System.out.println(jedis.hget("hash1","userName"));
        Map<String,String> map = new HashMap<String,String>();
        map.put("telphone","13810169999");
        map.put("address","atguigu");
        map.put("email","abc@163.com");
        jedis.hmset("hash2",map);
        List<String> result = jedis.hmget("hash2", "telphone","email");
        for (String element : result) {
            System.out.println(element);
        }
    }
```

#### 测试zset

```java
    @Test
    public void testZset(){
        //创建Jedis对象
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        jedis.zadd("zset01", 100d, "z3");
        jedis.zadd("zset01", 90d, "l4");
        jedis.zadd("zset01", 80d, "w5");
        jedis.zadd("zset01", 70d, "z6");
        Set<String> zrange = jedis.zrange("zset01", 0, -1);
        for (String e : zrange) {
            System.out.println(e);
        }
    }
```

## Redis Jedis实例

### 完成一个手机验证功能

要求：

1、输入手机号，点击发送后随机生成6位数字码，2分钟有效

2、输入验证码，点击验证，返回成功或失败

3、每个手机号每天只能输入3次

代码

```java
public class demo2 {
    public static void main(String[] args) {
        verifyCode("13331333");
        getRedisCode("13331333","494755");
    }

    //验证码校验
    public static void getRedisCode(String phone,String code){
        //连接Redis
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        jedis.select(2);
        //拼接key
        //验证码key
        String codeKey = "VerifyCode"+phone+":code";
        String redisCode = jedis.get(codeKey);
        //判断
        if (redisCode.equals(code)){
            System.out.println("成功");
        }else{
            System.out.println("失败");
        }
        jedis.close();

    }

    //每个手机每天只能发送3次，验证码放到redis中，设置过期时间
    public static void verifyCode(String phone){
        //连接Redis
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        jedis.select(2);
        //拼接key
        //手机发送次数key
        String countKey = "VerifyCode"+phone+":count";
        //验证码key
        String codeKey = "VerifyCode"+phone+":code";
        //每个手机每天只能发送三次
        String count = jedis.get(countKey);
        if (count == null){
            //没有发送次数，第一次发送
            //设置发送次数是1
            jedis.setex(countKey,24*60*60,"1");
        }else if(Integer.valueOf(count) <= 2){
            jedis.incr(countKey);
        }else if (Integer.valueOf(count) > 2){
            System.out.println("今天已经发送了三次");
            jedis.close();
            return;
        }

        String code = getCode();
        System.out.println(code);
        jedis.setex(codeKey,120,code);
        jedis.close();
    }

    //生成6位数数字验证码
    public static String getCode(){
        String code= "";
        Random random = new Random();
        for (int i = 0;i<6;i++){
            int rend = random.nextInt(10);
            code += rend;
        }
        return code;
    }
}
```

## Redis 事务 锁机制 秒杀

### Redis的事务定义

Redis事务是一个单独的隔离操作：事务中的所有命令队列中，但不会执行，直到输入Exec后，Redis会将之前的命令队列中的命令依次执行。

组队的过程可以通过discard来放弃组队

### Multi、Exec、discard

从输入Multi命令开始，输入的命令都会依次进入命令队列中，但不会执行，知道输入Exec后，Redis会将之前的命令队列中的命令依次执行

<img src="/img/Redis-10-01.png">

#### 案例

<img src="/img/Redis-10-07.png">

组队成功，提交成功

<img src="/img/Redis-10-08.png">

组队阶段报错，提交失败

<img src="/img/Redis-10-09.png">

组成成功，提交有成功有失败情况

### 事务的错误处理

组队中某个命令出现了报告错误，执行时整个的所有队列都会被取消

<img src="/img/Redis-10-02.png">

如果执行阶段某个命令报出了错误，则只有报错的命令不会被执行，而其他的命令都会执行，不会回滚。

<img src="/img/Redis-10-03.png">

### 事务冲突的问题

#### 例子

一个请求想给金额减8000

一个请求想给金额减5000

一个请求想给金额减1000

<img src="/img/Redis-10-04.png">

#### 悲观锁

<img src="/img/Redis-10-05.png">

**悲观锁**，顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次拿数据的时候都会上锁，这样别人想拿这个数据就会block直到他拿到锁。**传统的关系型数据库里边就用到了很多这种锁机制**，比如**行锁**，**表锁**等，**读锁**，**写锁**等，都是在做操作之前线上锁

#### 乐观锁

<img src="/img/Redis-10-06.png">

乐观锁，顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所有不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以利用版本号机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量。Redis就是利用这种check-and-set机制实现事务的。

### WATCH key [key ...]

在执行multi之前，先执行watch ley1 [key2]，可以监视一个或多个key，**如果在事务执行之前这个或这些key被其他命令所改动，那么事务将被打断。**

### unwatch

取消watch命令对所有key的监视

如果在执行watch命令之后，exec命令或discard命令被执行了的话，那么就不需要再执行unwatch

### Redis事务三特性

- 单独的隔离操作
  - 事务中的所有命令都会序列化、按顺序地执行。十五在执行过程中，不会被其他客户端发送来的命令请求所打断。
- 没有隔离级别的概念
  - 队列中的命令没有提及之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行
- 不保证原子性
  - 事务中如果有一条命令执行失败，其他的命令仍然会被执行，没有回滚。

## Redis持久化

Redis有两种方式RDB和AOF

### Redis持久化之RDB

在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的Snapshot快照，他恢复时是将快照文件直接读到内存里

#### 备份是如何执行的

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个**临时文件**中，待持久化过程都结束了，再用这个**临时文件替换上次持久化好的文件**。整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能，如歌需要进行大规模数据的恢复，且对于数据恢复的完整行不是非常敏感，那么RDB方式要比AOF方式更加的高效。**RDB的缺点是最后一次持久化后的数据可能丢失**

#### Fork

- Fork的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等）数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程
- 在Linux程序中，fork（）会产生一个和父进程完全相同的子进程，但子进程在此后多会exec系统调用，出于效率考虑，Linux中引入“写时复制技术”
- 一般情况父进程和子进程会公用同一段物理内存，只有进程空间的各段的内容要发送变化时，才会将父进程的内容复制一份给子进程

#### RDB持久化流程

<img src="/img/Redis-12-01.png">

#### 优势

- 适合大规模的数据恢复
- 对数据完整性和一致性要求不高更适合使用
- 节省磁盘空间
- 恢复速度快

<img src="/img/Redis-12-02.png">

#### 劣势

- Fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑、
- 虽然Redis在fork时使用写时拷贝技术，但是如果数据庞大时还是比较消耗性能。
- 在备份周期在一定间隔时间做一次备份，所有如果Redis意外down掉的话，就会丢失最后一次快照后的所有修改

#### 小总结

<img src="/img/Redis-12-03.png">

### Redis持久化之AOF

以日志的形式来记录每个操作（增量保存），将Redis执行过的所有写指令记录下来（读操作不记录），只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换样之，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。

#### AOF持久化流程

1. 客户端的请求写指令被append追加到AOF缓冲区内。
2. AOF缓冲区根据AOF持久化策略[always,everysec,no]将操作sync同步到磁盘AOF文件中
3. AOF文件大小超过重新策略或手动重写时，会对AOF文件rewrite重写，压缩AOF文件容量
4. Redis服务重启时，会重新load加载AOF文件中的写操作达到数据恢复目的

<img src="/img/Redis-13-01.png">

### 



#### AOF是默认不开启的

可以在redis.conf中配置文件名称，默认为 appendonly.aof

AOF文件的保存路径，同RDB的路径一致。

#### AOF和RDB同时开启，redis听谁的？

AOF和RDB同时开启，系统默认取AOF的数据（数据不会存在丢失）

#### AOF同步频率设置

appendfsync **always**
始终同步，每次Redis的写入都会立刻记入日志；性能较差但数据完整性比较好
appendfsync **everysec**
每秒同步，每秒记入日志一次，如果宕机，本秒的数据可能丢失。
appendfsync **no**
redis不主动进行同步，把**同步时机交给操作系统。**

#### 优势

<img src="/img/Redis-13-02.png">

- 备份机制更加稳健，丢失数据概率更低。
- 可读的日志文本，通过操作AOF稳健，可以处理误处理。

#### 劣势

- 比起RDB占用更多的磁盘空间
- 恢复备份速度要慢
- 每次读写都同步的话，有一定性能压力
- 存在个别BUG，造成恢复不能

#### 小总结

<img src="/img/Redis-13-03.png">

### 总结

- RDB持久化方式能够在指定的时间间隔能对你的数据进行快照存储
- AOF持久化方式记录每次对服务器写的操作，但服务器重启的时候会重新执行这些命令来恢复原始的数据，AOF命令以Redis协议追加保存每次写的操作到文件末尾
- Redis还能对AOF文件进行后台重写，使得AOF文件的体积不至于过大
- 只做缓存：如果你只希望你的数据在服务器运行的时候存在，你也可以不使用任何持久化方式
- 同时开启两种持久化方式（系统默认取AOF的数据）
- 在这种情况下,当redis重启的时候会优先载入AOF文件来恢复原始的数据, 因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整.
- RDB的数据不实时，同时使用两者时服务器重启也只会找AOF文件。那要不要只使用AOF呢？ 
- 建议不要，因为RDB更适合用于备份数据库(AOF在不断变化不好备份)， 快速重启，而且不会有AOF可能潜在的bug，留着作为一个万一的手段。

#### 用哪个比较好

官方推荐两个都启用。

如果对数据不敏感，可以选单独用RDB。

不建议单独用 AOF，因为可能会出现Bug。

如果只是做纯内存缓存，可以都不用。

## Redis主从复制

### 定义

主机数据更新后根据配置和策略，自动同步到备机的master/slaver机制，Master以写为主，Slave以读为主

### 作用

- 读写分离，性能扩展
- 容灾快速恢复

<img src="/img/Redis-14-01.png">

### 开启主从复制（略）

## 常用3种方式

#### 一主二仆

<img src="/img/Redis-14-02.png">

#### 薪火相传

上一个Slave可以是下一个slave的Master，Slave同样可以接收其他 slaves的连接和同步请求，那么该slave作为了链条中下一个的master, 可以有效减轻master的写压力,去中心化降低风险。

<img src="/img/Redis-14-03.png">

#### 反客为主

当一个master宕机后，后面的slave可以立刻升为master，其后面不用做任何修改。

用slaveof no one 将从机变为主机

<img src="/img/Redis-14-04.png">

### 复制原理

- Slave启动成功连接到master后发送一个sync命令。
- Master接收到命令启动后台的存盘进程，同时收集所有接收的用于修改数据集命令，在后台进程执行完毕之后，master将传送整个数据文件到slave，以完成一次完全同步。
- 全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。
- 增量复制：Master继续将新的所有收集到的修改命令依次传给slave完成同步。
- 但是只有是重新连接master，一次完全同步（全量复制）将被自动执行。

<img src="/img/Redis-14-07.png">

### 哨兵模式

#### 定义

反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库

<img src="/img/Redis-14-06.png">

#### 开启哨兵模式（略）

### 故障恢复

<img src="/img/Redis-14-07.png">

优先级在redis.conf中默认：slave-priority 100，值越小优先级越高

偏移量是指获得原主机数据最全的

每个redis实例启动后都会随机生成一个40位的runid

## Redis集群

### 什么是集群

Redis集群实现了对Redis的水平扩容，及启动N个redis节点，将整个数据库分布存储在这N个节点中，每个节点存储总数据的1/N。

Redis集群通过分区来提供一定程度的可用性；即使集群中有一部分节点失效或者无法进行通信，集群也可以继续处理命令请求。

### 启动Redis集群（略）

### 什么是插槽

一个 Redis 集群包含 16384 个插槽（hash slot）， 数据库中的每个键都属于这 16384 个插槽的其中一个， 

集群使用公式 CRC16(key) % 16384 来计算键 key 属于哪个槽， 其中 CRC16(key) 语句用于计算键 key 的 CRC16 校验和 。

集群中的每个节点负责处理一部分插槽。 举个例子， 如果一个集群可以有主节点， 其中：

节点 A 负责处理 0 号至 5460 号插槽。

节点 B 负责处理 5461 号至 10922 号插槽。

节点 C 负责处理 10923 号至 16383 号插槽。

### 在集群中录入值

在redis-cli每次录入、查询键值，redis都会计算出该key应该送往的插槽，如果不是该客户端对应服务器的插槽，redis会报错，并告知应前往的redis实例地址和端口。

redis-cli客户端提供了 –c 参数实现自动重定向。

如 redis-cli  -c –p 6379 登入后，再录入、查询键值对可以自动重定向。

不在一个slot下的键值，是不能使用mget,mset等多键操作。

<img src="/img/Redis-15-02.png">

可以通过{}来定义组的概念，从而使key中{}内相同内容的键值对放到一个slot中去。

<img src="/img/Redis-15-03.png">

### 查询集群中的值

CLUSTER GETKEYSINSLOT &lt;slot&gt;&lt;count&gt; 返回 count 个 slot 槽中的键。

<img src="/img/Redis-15-01.png">

### 故障恢复

如果主节点下线？从节点能否自动升为主节点？注意：**15秒超时**

主节点恢复后，主从关系会如何？主节点回来变成从机。

如果所有某一段插槽的主从节点都宕掉，redis服务是否还能继续?

如果某一段插槽的主从都挂掉，而cluster-require-full-coverage 为yes ，那么 ，整个集群都挂掉

如果某一段插槽的主从都挂掉，而cluster-require-full-coverage 为no ，那么，该插槽数据全都不能使用，也无法存储。

redis.conf中的参数  cluster-require-full-coverage

### Redis集群提供以下的好处

- 实现扩容
- 分摊压力
- 无中心配置相对简单

### Redis集群的不足

- 多键操作是不被支持的
- 多键的Redis事务是不被支持的。lua脚本不被支持
- 由于集群方案出现较晚，很多公司已经采用了其他的集群方案，而代理或者客户端分片的方案想要迁移至redis cluster，需要整体迁移而不是逐步过渡，复杂度较大。

## Reids应用问题解决

### 缓存穿透

#### 问题描述

key对应的数据源并不存在，每次针对次key的请求从缓存获取不到，请求都会压到数据源，从而可能压垮数据源，比如用一个不存在的用户id获取用户信息，不论缓存还是数据库都没有，若黑客利用此漏洞进行攻击可能压垮数据库。

<img src="/img/Redis-16-02.png">

#### 解决方案

一个一定不存在缓存及查询不到的数据，由于缓存是不命中时被动写的，并且出于容错考虑，如果从存储层查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义。

解决方案：

（1） **对空值缓存：**如果一个查询返回的数据为空（不管是数据是否不存在），我们仍然把这个空结果（null）进行缓存，设置空结果的过期时间会很短，最长不超过五分钟

（2） **设置可访问的名单（白名单）：**

使用bitmaps类型定义一个可以访问的名单，名单id作为bitmaps的偏移量，每次访问和bitmap里面的id进行比较，如果访问id不在bitmaps里面，进行拦截，不允许访问。

（3） **采用布隆过滤器**：(布隆过滤器（Bloom Filter）是1970年由布隆提出的。它实际上是一个很长的二进制向量(位图)和一系列随机映射函数（哈希函数）。

布隆过滤器可以用于检索一个元素是否在一个集合中。它的优点是空间效率和查询时间都远远超过一般的算法，缺点是有一定的误识别率和删除困难。)

将所有可能存在的数据哈希到一个足够大的bitmaps中，一个一定不存在的数据会被 这个bitmaps拦截掉，从而避免了对底层存储系统的查询压力。

（4） **进行实时监控：**当发现Redis的命中率开始急速降低，需要排查访问对象和访问的数据，和运维人员配合，可以设置黑名单限制服务

### 缓存击穿

#### 问题描述

key对应数据存在，但是redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这时候大并发的请求可能会瞬间把后端BD压垮。

![image-20220110164537596](/img/Redis-16-03.png)

#### 解决方案

key可能会在某些时间点被超高并发地访问，是一种非常“热点”的数据。这个时候，需要考虑一个问题：缓存被“击穿”的问题。

解决问题：

**（1）预先设置热门数据：**在redis高峰访问之前，把一些热门数据提前存入到redis里面，加大这些热门数据key的时长

**（2）实时调整：**现场监控哪些数据热门，实时调整key的过期时长

**（3）使用锁：**

（1） 就是在缓存失效的时候（判断拿出来的值为空），不是立即去load db。

（2） 先使用缓存工具的某些带成功操作返回值的操作（比如Redis的SETNX）去set一个mutex key

（3） 当操作返回成功时，再进行load db的操作，并回设缓存,最后删除mutex key；

（4） 当操作返回失败，证明有线程在load db，当前线程睡眠一段时间再重试整个get缓存的方法。

<img src="/img/Redis-16-04.png">

### 缓存雪崩

#### 问题描述

key对应的数据存在，但是redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这时候大并发的请求可能会瞬间把后端DB压垮。

缓存雪崩与缓存击穿的区别在于这里针对很多key缓存，前者则是某一个key

正常访问

<img src="/img/Redis-16-05.png">

缓存失效瞬间

<img src="/img/Redis-16-06.png">

#### 解决方案

缓存失效时的雪崩效应对底层系统的冲击非常可怕！

解决方案：

（1） **构建多级缓存架构：**nginx缓存 + redis缓存 +其他缓存（ehcache等）

（2） **使用锁或队列**：

用加锁或者队列的方式保证来保证不会有大量的线程对数据库一次性进行读写，从而避免失效时大量的并发请求落到底层存储系统上。不适用高并发情况

（3） **设置过期标志更新缓存：**

记录缓存数据是否过期（设置提前量），如果过期会触发通知另外的线程在后台去更新实际key的缓存。

（4） **将缓存失效时间分散开：**

比如我们可以在原有的失效时间基础上增加一个随机值，比如1-5分钟随机，这样每一个缓存的过期时间的重复率就会降低，就很难引发集体失效的事件。

### 分布式锁  

#### 问题描述

随着业务发展的需要，原单体单机部署的系统被演化成分布式集群系统后，由于分布式系统多线程、多进程并且分布在不同机器上，这将使原单机部署情况下的并发控制锁策略失效，单纯的Java API并不能提供分布式锁的能力。为了解决这个问题就需要一种跨JVM的互斥机制来控制共享资源的访问，这就是分布式锁要解决的问题！

分布式锁主流的实现方案：

1. 基于数据库实现分布式锁

2. 基于缓存（Redis等）

3. 基于Zookeeper

每一种分布式锁解决方案都有各自的优缺点：

1. 性能：redis最高

2. 可靠性：zookeeper最高

这里，我们就基于redis实现分布式锁。

#### 解决方案

redis:命令

\# set sku:1:info “OK” NX PX 10000

EX second ：设置键的过期时间为 second 秒。 SET key value EX second 效果等同于 SETEX key second value 。

PX millisecond ：设置键的过期时间为 millisecond 毫秒。 SET key value PX millisecond 效果等同于 PSETEX key millisecond value 。

NX ：只在键不存在时，才对键进行设置操作。 SET key value NX 效果等同于 SETNX key value 。

XX ：只在键已经存在时，才对键进行设置操作。

<img src="/img/Redis-16-07.png">

<img src="/img/Redis-16-08.png">

1. 多个客户端同时获取锁（setnx）

2. 获取成功，执行业务逻辑{从db获取数据，放入缓存}，执行完成释放锁（del）

3. 其他客户端等待重试
