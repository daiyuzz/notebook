## Flask-SQLalchemy flask的ORM模型

##### 使用ORM模型的原因

当项目越来越大的时候会出现很多问题：

1.原生SQL较多 重复使用率低

2.如果数据库发生了改变 所有原生SQL就都需要进行修改

3.写原生SQL的时候  会有安全隐患



ORM：中文件关系对象的映射  使用ORM去操作数据库的时候 不会再去写原生的SQL了 通过把表映射成 类  字段为你的属性  ORM在执行的时候 也会最终转换为 SQL语句 去操作数据库

1. 易用性   使用ORM可以减少重复SQL的概率  写出来的模型也更加的直观清晰
2. 可移植性   ORM支持很多不同的数据库  







##### 安装：

sudo pip3 install flask-sqlalchemy

## 一、执行原生SQL

##### （1）创建数据库

create database if not exits 库名 character set utf8；

##### （2）安装pymysql

sudo pip3 install pymysql

##### （3）配置数据库

DB_URI='mysql+pymysql://用户名:密码@主机:端口号/库名'



##### 实例

```python
from sqlalchemy import create_engine

DATABASE = 'hz03'
USERNAME = 'root'
PASSWORD = '123456'
HOST = '127.0.0.1'
PORT = '3306'
#创建连接和操作数据库的URI
DB_URI = 'mysql+pymysql://{}:{}@{}:{}/{}'.format(USERNAME,PASSWORD,HOST,PORT,DATABASE)

#创建操作数据库的引擎
engine = create_engine(DB_URI)

with engine.connect() as con:
    # con.execute('create table user(id int,username varchar(255),sex tinyint)')
    con.execute('insert into user values(1,"xxx",1)')
```





## 二、在flask中使用ORM

### （1）当前字段类型

| 类型名       | 说明                        |
| ------------ | --------------------------- |
| integer      | 整形                        |
| Smallinteger | 小整形                      |
| Biginteger   | 长整形                      |
| Float        | 浮点型                      |
| String       | varchar类型                 |
| Text         | 长文本                      |
| Boolean      | tingint                     |
| Date         | 日期 datetime.date          |
| Time         | 时间 datetime.time          |
| DateTime     | 时间和日期 datetime.datetim |

### (2)约束条件

| 选项        | 选项说明             |
| ----------- | -------------------- |
| primary_key | 主键  默认False      |
| index       | 常规 默认False       |
| Unique      | 唯一  默认True       |
| nullable    | 是否为null 默认False |
| default     | 默认值               |

##### 注意：

其中default默认值并不是更改表结构的默认值 而是在插入数据的时候 如果不插入数据则插入默认值



##### 实例

#### 配置

```python
from flask import Flask,render_template
from flask_script import Manager
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
#创建连接数据的URI
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://root:123456@127.0.0.1:3306/hz03'

app.config['SQLALCHEMY_COMMIT_ON_TEARDOWN'] = True #开启自动提交
#数据的追踪 当数据发生改变时 会返回信号量 进行关闭 
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)
manager = Manager(app)

```

#### 创建模型

```python
class User(db.model):
    id = db.Column(db.Integer,primary_key = True)
    username = db.Column(db.String(20),index=True)
    age = db.Column(db.Integer)
    icon = db.Colum(db.String(40),default = 'default.jpg')
```

#### 数据的添加修改删除

```python
@app.route('/create_table/')
def create_table():
    db.drop_all() #删除 和当前模型类同名的表
    db.create_all() #创建当前模型类的表
    return '创建表'


@app.route('/insert/')
def insert():
    try:
        u = User(username='张三',age=18)
        # print(u)
        db.session.add(u)
        db.session.commit() #因为sqlalchemy开启事物 所有所以需要提交或者回滚
    except:
        db.session.rollback()
    return '添加数据'
#开启了自动提交功能 不需要手动commit了
@app.route('/insert_two/')
def insert_two():
    u = User(username='李四',age=20)
    db.session.add(u)
    return '走我了'
#修改
@app.route('/update/')
def update():
    u = User.query.get(1)
    # print(u.id)
    # print(u.username)
    u.username = '王五'
    db.session.add(u)
    return 'update'

#删除
@app.route('/delete/')
def delete():
    u = User.query.get(2) #查询成功返回 对象 失败返回None
    # print(u)
    db.session.delete(u)
    return '删除'
```





### 拆分MVT

#### 目录结构

```python
project/
	APP/
    	__init__.py 包文件必须的
        model.py    模块
        views.py    视图
    ext.py  extensions.py  加载第三方扩展的文件
    settings.py   配置文件
    static/
    templates/
    manage.py      启动项
```



#### 细致的

```python
project/
	App/
    	__init__.py
        static/
        	js/
            img/
            upload/
            css/
        templates/
        	common/
            ...
        forms/
        	__init__.py
            ...
        models/
        	__init__.py
        views/
        	__init.py__.py
            ...
        settings.py
        email.py
        extensions.py
    manager.py
    migrations/
    venv/
```



### 三、数据的操作

##### 创建模型类

```python
class User(Base,db.Model):
    __tablename__ = 'user' #给表起名
    id = db.Column(db.Integer,primary_key=True)
    username = db.Column(db.String(20),index=True)
    age = db.Column(db.Integer)
    icon = db.Column(db.String(40),default ='default.jpg')
    def __init__(self,username='',age=0,icon='default.jpg'):
        self.username = username
        self.age = age
        self.icon = icon
```

#### (1)添加 add   add_all

#### 添加一条

```python
@main.route('/add/')
def add():
    u = User(username = '张三',age=18)
    db.session.add(u)
    db.session.commit()
    return '数据添加一条成功'   
```

#### 添加多条

```python
@main.route('/add_all/')
def add_all():
    u1 = User(username='李四',age=50)
    u2 = User(username='王五',age=22)
    db.session.add_all([u1,u2])
    db.session.commit()
    return '添加多条'
```



#### （2）自定义增删改的基础类

```python
class Base:
    #定义一个添加一条数据的方法
    def save(self):
        try:
            db.session.add(self)
            db.session.commit()
        except:
            db.session.rollback()

    #定义添加多条数据的方法
    @staticmethod
    def save_all(*args):
        try:
            db.session.add_all(args)
            db.session.commit()
        except:
            db.session.rollback()

    #自定义删除方法
    def delete(self):
        try:
            db.session.delete(self)
            db.session.commit()
        except:
            db.session.rollback()

```



使用

```python
class User(Base,db.Model):
    ...
```



#### 在视图中使用

```python
@main.route('/add/')
def add():
    # u = User(username='张三',age=18)
    u = User('张三',18)
    # db.session.add(u)
    # db.session.commit()
    u.save() #使用自定义的添加方法
    return '数据添加一条成功'

@main.route('/add_all/')
def add_all():
    # u1 = User(username='李四',age=20)
    # u2 = User(username='王五',age=22)
    u1 = User('赵六',27)
    u2 = User('李七',12)
    # db.session.add_all([u1,u2])
    # db.session.commit()
    User.save_all(u1,u2)
    return '添加多条'

@main.route('/delete/')
def delete():
    u = User.query.get(1)
    u.delete()
    return '删除'
```



## 四、数据库操作

#### 查询集

查询数据的集合

##### 分类

1.原始查询集

​	类名.query得到的结果就是原始查询集

2.数据查询集

​	加上各种过滤器的方法 最终返回的结果 为数据查询集  都使用数据查询集



#### 过滤器

##### （1）all  查询多有  以列表的形式返回  不支持连贯操作

类名.query.all()

```python
@main.route('/all/')
def all():
    data = User.query.all()
    print(data)
    return render_template('show.html',data=data)
```

##### (2)filter() 过滤

类名.query.filter([类名.属性  条件操作符  值])

默认返回所有

```python
@main.route('/filter/')
def filter():
    # data = User.query.filter() #返回所有
    # data = User.query.filter(User.age>20) #查询年龄大于20的数据
    data = User.query.filter(User.age>20,User.age<40) #查询年龄大于20的数据 and 小于40
    print(data)
    return render_template('show.html',data=data)
```

##### （3）filter_by 只支持参数为关键字参数

类名.query.filter_by(属性名=值.....)

```python
@main.route('/filter_by/')
def filter_by():
    data = User.query.filter_by(id = 2)
    #data = User.query.filter_by(id>2)  #错误写法
    data = User.query.filter_by(id=2,age=27)
    return render_template('show.html',data=data)
```

##### （4）offset偏移量

offset（num）

```python
@main.route('/offset/')
def offset():
    data = User.query.filter().offset(2)
    return render_template('show.html',data=data)
```

x