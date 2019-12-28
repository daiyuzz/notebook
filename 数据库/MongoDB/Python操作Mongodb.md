# Python操作Mongodb

### 一 导入 pymongo

###### 		from pymongo import MongoClient

### 二 连接服务器 端口号	27017

##### 连接MongoDB

###### 	连接MongoDB我们需要使用PyMongo库里面的MongoClient，一般来说传入MongoDB的IP及端口即可，第一个参数为地址host，第二个参数为端口port，端口如果不传默认是27017。

###### 		conn = MongoClient("localhost")

###### 	MongoClient(host='127.0.0.1',port=27017)

### 三 连接数据库
###### 		db = conn.数据库名称

##### 连接集合
###### 	collection = db[collection_name]

##### or 

###### 	collection = db.collection_name

##### 查看全部聚集名称

###### 	db.collection_names()

###四 插入数据

##### 	(1) 插入一条数据

###### 			db.user.insert({"name":"夏利刚","age":18,"hobby":"学习"})

##### 	(2) 插入多条数据

###### 			db.user.insert([{**"name"**:**"夏利刚"**,**"age"**:18,**"hobby"**:**"学习"**},{**"name"**:**"xxxoo"**,**"age"**:48,**"hobby"**:**"学习"**}]

##### (3) 在3.x以上 建议 使用

##### 	insert_one 插入一条数据 

#####       	insert_many() 插入多条数据

##### (4) 返回 id  使用insert_one()

###### data.inserted_id

###### data.inserted_ids

### 五 查询数据

##### (1) 查询所有

###### 		db.user.find()

```python
#带条件的查询
# data = db.user.find({"name":"周日"})
# print(data) #返回result类似一个迭代器  可以使用 next方法 一个一个 的取出来
# print(next(data))   #取出一条数据
```

##### (2) 查询一条

###### 		db.user.find_one()

##### (3) 带条件查询

###### 		db.user.find({"name":"张三"})

##### (4) 查询 id

###### 		**from **bson.objectid **import **ObjectId*#用于ID查询

###### 		data = db.user.find({**"_id"**:ObjectId(**"59a2d304b961661b209f8da1"**)})

##### (5) 模糊查询

###### （1）{**"name"**:{'$regex':**"张"**}}

###### （2）import re  	{'xxx':re.compile('xxx')}  

### 六 sort limit count skip

##### (1) sort		排序

​	年龄 大于10

###### 		data = db.user.find({**"age"**:{**"$gt"**:10}}).sort(**"age"**,-1) #年龄 升序 查询  pymongo.ASCENDING   --升序

###### 		data = db.user.find({**"age"**:{**"$gt"**:10}}).sort(**"age"**,1) #年龄 降序 查询	pymongo.DESCENDING --降序

##### (2) limit		取值

​	**取三条数据**

###### 		db.user.find().limit(3)

###### 		data = db.user.find({**"age"**:{**"$gt"**:10}}).sort(**"age"**,-1).limit(3)

##### (3) count 	统计数据条数

###### 		db.user.find().count()

##### (4) skip 		从第几条数据开始取

###### 		db.user.find().skip(2)

### 七 update 修改

​	update()方法其实也是官方不推荐使用的方法，在这里也分了**update_one()**方法和**update_many()**方法，用法更加严格，

##### (1) update()

###### 		db.user.update({"name":"张三"},{"$set":{"age":25}})

###### 		db.user.update({"name":"张三"},{"$inc":{"age":25}})

##### (2) update_one() 第一条符合条件的数据进行更新

​	db.user.update_one({"name":"张三"},{"$set":{"age":99}})

##### (3) update_many() 将所有符合条件的数据都更新

###### 	db.user.update_many({**"name"**:**"张三"**},{**"$set"**:{**"age"**:91}})

**(4)** 其返回结果是UpdateResult类型，然后调用**matched_count**和**modified_count**属性分别可以获得匹配的数据条数和影响的数据条数。

###### 	print(result.matched_count, result.modified_count)没

### 八 remove 删除

删除操作比较简单，直接调用remove()方法指定删除的条件即可，符合条件的所有数据均会被删除，

##### (1) 删除 张三

###### 		collection.remove({"name":"lilei"})

##### (2) 全部删除

###### 		collection.remove()

##### (3) 依然存在两个新的推荐方法，delete_one()和delete_many()方法，示例如下：

##### 	delete_one()即删除第一条符合条件的数据

###### 		collection.delete_one({“name”:“ Kevin”})

##### 	delete_many()即删除所有符合条件的数据，返回结果是DeleteResult类型

###### 		collection.delete_many({“age”: {$lt:25}})

##### (4) 可以调用deleted_count属性获取删除的数据条数。

###### 	result.deleted_count



### 九 关闭连接

##### 		conn.close()