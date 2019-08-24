## Mongodb

是一个非关系型数据库 存储形式为键值对 水平扩展很容易  常作为缓存数据库来使用

Mongodb的存储文档称之为BSON 类似json对象  字段值可以包含其他的文档、数组及文档数组

##### mongodb和mysql的概念解析

| sql概念       | mongodb的概念  | 解释/说明       |
| ----------- | ----------- | ----------- |
| database    | database    | 数据库         |
|             | collection  | 表/集合        |
|             | document    | 行/文档        |
| column      | field       | 列/域         |
| table join  |             | 表关联         |
| primary key | primary key | 主键手动添加/自动创建 |



## 一、进入mongodb数据库

##### （1）cd mongo的bin目录

##### （2）输入 启动服务  并选择创建数据库的位置

```python
mongod.exe --
dbpath=创建数据库的位置
```

##### （3）再打开一个终端

cd mongodb目录

mongo.exe



## 二、数据库的操作

##### （1）查看所有数据库

show dbs

##### （2）创建/切换数据库

use 数据库名

##### （3）查看当前所在的数据库

db.getName()

##### （4）创建集合

1.db.createCollection(集合名)

db.createCollection('user')

2.db.集合名.insert（文档）

db.goods.insert({"goodsname":"牛奶"})

##### （5）查看所有的集合

show collections

##### （6）删除集合

db.collection.drop()

##### 注意：

mongodb区分大小写





## 三、数据的添加 INSERT

##### （1）insert 插入一条文档

db.集合名.insert({文档})

db.user.insert({"name":"张三"，“age”:18})

##### （2）insert插入多条文档

列表中存放多条文档

db.user.insert([{'name':'李四','age':20},{'name':‘王五’,'age':25}])



#### 3.xx新版本的插入文档

##### （1）插入一条文档

db.collection.insertOne()

db.user.insertOne({"name":"张里"，"age":30})

##### （2）插入多条文档

db.collection.insertMany()

db.user.insertMany([{"name":"jj","age":12},{'name':"hh","age":56}])

##### 插入多条文档 不管是insert还是insertMany都需要使用列表

insertMany 如果不适用列表 则报错

insert 插入多条不适用类表  则插入成功数据为第一条文档





## 四、update修改

**主体结构**

db.collection.update(

​	<query>,查询条件

​	<query>，要更改的东西

​	{

​		<upset> bool值  如果修改的数据查询不到 当前数据是否作为新数据插入   默认false 不插入

​		<multi> bool值  如果匹配到多条，默认是更改第一条 False 改为True则全部更改

​	}

)

##### update：$ set 和 $inc两个操作符

$set直接修改

$inc累加修改

##### 实例

##### 不使用更新操作符 会将文档到的内容除了_id以外的内容替换成当前update的内容

db.user.update({"name":"张三"},{"age":38})

##### 使用更新操作符号$set 将年龄直接修改为30 其余当前文档的内容不会发生改变

db.user.update({"name":"李四"},{$set:{"age":30}})

##### 使用更新操作符号$inc 将年龄值累加修改30 其余当前文档的内容不会发生改变

db.user.update({"name":"李四"},{$inc:{"age":30}})



##### upsert参数的使用

db.user.update({"name":"张三"}，{$set{"age":30}},true)

```python

```

##### multi的使用

```python
db.user.update({"name":"赵柳"},{$set:{age:18}},{multi:true})
```

##### 完整的修改语句

db.user.update({"name":"赵柳"},{$set:{age:18}},true,true)



#### 3.xx以后的修改

db.collection.updateOne()

##### 实例

```python
db.user.updateOne({"age":18},{$set:{age:10}})
```

db.collection.updateMany()

##### 实例

```python
db.user.updateMany({"name":"赵柳"},{$set:{age:12}})
```



## 五、remove删除

#### 主体结构

```python
db.collection.remove(
	<query>,条件
    justOne, bool值  默认flase 全部删除
)
```

##### 实例

##### 删除多条

```python
db.user.remove({"name":"赵柳"})
WriteResult({"nRemoved":2})
```

##### 删除一条

```python
db.user.remove({"name":"赵柳"},true)
db.user.remove({"name":"赵柳"},1)
```

##### 清除集合中所有文档数据

```python
db.user.remove({})
```



#### 3.xx版本以后的删除

db.collection.deleteOne()





db.collection.deleteMany()







## 六、查询

##### （1）find查询所有

db.collection.find()

##### （2）指定字段显示与隐藏

指定显示某些字段 字段名：true

```python
db.user.find({},{name:true}) 只显示name
db.user.find({},{name:true,age:true})显示name和age字段
```

指定除了某字段以外的其他字段显示

```python
db.user.find({},{name:false,age:false})
```

##### 注意：

自己定义的域 只能设置显示  或者不显示 不能一起设置

但是系统的id可以 因为不管你给其他域设置true或false都会显示













##### （8）$or查询

db.collection.find({$or[条件1，条件2......]})





##### （10）limit

db.collection.find().limit(num)





##### (11)skip 跳过num条

db.collection.find().skip(num)

db.user.find().skip(2)



##### (12)limit和skip的结合使用



##### （13）sort排序

db.collection.find()sort({key:1|-1}) #升序或者降序









## 六、数据库的删除

删除之前最好use一下

db.dropDatabase()















