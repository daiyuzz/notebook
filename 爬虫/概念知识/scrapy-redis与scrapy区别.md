## scrapy-redis与scrapy区别

利用redis实现分布式爬虫

### 调度器scheduler

```python
1.scrapy
	1.改写python双向队列为自己的优先队列，但是scrapy中存在多个spider时不能共享同一个带爬队列
2.scrapy-redis
	1.将scrapy队列放到redis数据库中读取，实现多个爬虫共享一个队列
    2.同时还支持使用FIFO队列和LIFO队列
```

### 数据管道Item Pipeline

```python
1.scrapy
	爬取到的数据直接传给管道文件
2.scrapy-redis
	将爬取到的数据存入redis数据队列，可实现items proceess集群
```

### 爬虫引擎Base Spider

```python
1.scrapy
	Spider类
2.scrapy-redis
	继承Spider类和RedisMixin类，从redis读取url
```

### 代码部分

#### settings



