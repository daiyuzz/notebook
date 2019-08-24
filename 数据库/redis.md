## 一、启动redis

##### （1）启动服务

cd redis 目录

redis-server.exe redis.windows.conf

##### (2)进入终端

cd redis目录

redis-cli

##### (3)如果当前redis存在密码

auth“密码”

密码为123456



## 二、redis数据库的类型

1.字符串  string

2.哈希  hash

3.列表 list

4.集合  set

5.有序集合  zset

redis数据库有16个库



## 三、string字符串

概述：string是redis的最基本的数据类型  最大存储512M

key对应一个value

##### （1）设置键值

set key value

```python
set name zhangsan
```

##### （2）设置键值  并设置过期时间

setex key 时间 value

```python
setex age 10 8 #设置过期时间为10秒
```

##### （3）查看过期时间

ttl key

```python
ttl age
```

##### （4）在key不存在时 则设置键对值

setnx key value

##### （5）设置多个键值

mset key value key value

##### (6)获取一个值

get key

##### (7)获取多个值

mget k1 k2......

##### (8)设置新值 并将旧值返回

getset key value

##### (9)将值+1

incr key

##### (10)将值-1

derc key

##### (11)将对应的key+整数

incrby key num

##### （12）将对象的key-整数

decrby key num

##### (13)追加值

append key value

##### (14)获取所有的长度

strlen key

##### (15)获取所有的key

keys *

##### (16)获取某个键名开头的所有key

keys  k*

##### (17)获取以某个以某字符结尾的key

keys *k

##### (18)判断某个key是否存在

exists key

##### (19)查看key对应值的类型

type key

##### (20)设置key的过期时间

expire key seconds

##### (21)查看key的过期时间 以毫秒返回

pttl key

##### (22)移除过期时间

persist key

##### (23)删除所有的key

flushdb 清除当前库的key

flushall 清除所有库的key

##### (24)随机返回一个key

randomkey



## 四、hash

{

​	field:v1,

​	field:v2

​	.....

}

1.设置单个值

hset key field value

2.设置多个值

hmset key field v1 field v2....

3.为hash中的某个field添加增量increment

hincrby key field num

4.当field不存在时，则设置值

hsetnx key field value

5.获取一个属性的值

hget key field

6.获取多个值

hmget key field1 field2...

7.获取所有字段和值

hgetall key

8.获取所有的字段

hkeys key

9.获取所有的值

hvals key

10.返回字段的个数

hexists key field

12.删除字段和值

hdel key field.....

## 三、列表

概述：列表是一个简单的字符串列表 可以在列表的头部或尾部插入内容

1.在头部插入值

Ipush key value value.....

2.在尾部插入

rpush key value....

3.为已存在的列表添加值

rpushx key value

4.在一个元素的前后插入新元素

linsert key before | after value value

5.设置指定索引元素的值

lset key index value