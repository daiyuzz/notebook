## python redis 连接池原理

### 什么是连接池

```python
通常情况下，当我们需要做redis操作时，会创建一个连接，并基于这个连接进行redis操作，操作完成后，释放连接，
一般情况下，这是没有问题的，但当并发量比较高的时候，频繁的创建和释放对性能会有较高的影响，于是，连接池就返回作用
连接池原理：通过预先创建多个连接，当进行redis操作时，直接获取已经创建的链接进行操作，而且操作完成过后，不会释放，用于后续其他的redis操作
这样就达到了避免频繁的redis连接创建和释放的目的，从而提高性能
```



### 原理

#### 连接池的使用

```python
rdp = redis.ConnectionPool(host='127.0.0.1', port=6379, password='xxxxx')  #未做连接，只设置连接参数
rdc = redis.StrictRedis(connection_pool=rdp)  
rdc.set('name', 'Yi_Zhi_Yu')
rdc.get('name')
```

