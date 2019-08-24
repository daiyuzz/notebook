# Django第一天：从做Helloworld了解Django

## 1. 课前知识复习与扩展

### bytes

计算机存数据    字节队列对象

### str

utf-8

显示数据

## 2. 本阶段课程

1. 主题 ：Web开发 Django

2. 环境：

    1） Ubuntu

    2）Python 3.5以上

    3）开发工具（随意）

    4）Django 1.11

   ​

## 3. B/S 和  C/S

Browser  Server

Client    Server

![](images/01day-1.png)

## 4. 和我一起做Helloworld

创建虚拟环境

virtualenv  隔离多个项目之间的环境

pyenv   管理Python的多个版本

```
pip install virtualenv
virtualenv venv  --python=python3
```

激活环境

```
gavin@x:~/helloworld-dir$ source venv/bin/activate
(venv) gavin@x:~/helloworld-dir$ pip list
Package    Version
---------- -------
pip        10.0.1 
setuptools 39.0.1 
wheel      0.31.0 
(venv) gavin@x:~/helloworld-dir$ 
(venv) gavin@x:~/helloworld-dir$
```

安装Django 1.11

```
pip install django==1.11
```

创建项目

```
django-admin startproject  helloworld
```

运行

```
cd helloworld
python manage.py runserver 0.0.0.0:8888
#  sudo ufw allow 8888
```

测试

```
http  http://localhost:8888
# http  http://10.31.161.66:8888

```

创建一个hello  app

```
python manage.py startapp  helloapp
```

写  helloapp/views.py

```python
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.

def hello(request):
    return HttpResponse('Hello World!')
```

配置 从浏览器发来的请求路由

```python
from django.conf.urls import url,include
from django.contrib import admin
from helloapp.views import hello

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^hello/', hello), 
]

```

在settings.py中启用helloapp

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'helloapp',
]
```

## 5. Helloworld 中的 B/S架构

server   :  python manage.py runserver    127.0.0.1:8000

browser:  http://127.0.0.1:8000/hello

## 6. Helloworld中的MTV架构

### 如果不使用MVC，写Web应用比较困难，耦合太大

数据库   中  有  100个文章

Server:使用Python 把数据库中的100个文章读取出来，并显示在网页上。

Browser:  http://10.31.161.78/articles/

CGI :

```
# 读取所有  数据
# 连接数据库
# 执行  select * from articles
# articles
# 拼接html文档（包含所有文章，文章如何显示）
html = ""
html +=  " 
# for article in articles:


	
```

### 如果使用MVC

解耦

![](images/01day-4.png)

![](images/01day-2.jpg)

M：Model,  对数据的抽象，它不是数据，它是数据的接口。

```
例如：  article表

class  Article():

	title，content

Article.object.all()
Article.save()

```

V：View，负责把数据格式化后展现给用户。

C:Controller，根据具体的request，控制model获取相应的数据，把数据传给对应的视图View。

### Django中的MVT

传统MVC中的V  ： 展现数据   （展现哪些数据+ 如何展现）

M：Model

V： View  （  展现哪些数据）

T： Template（如何展现）

温馨提示：

```
必太在意如何理解MVC和MVT的区别，分层的定义是为了教授知识的方便，如果你工作一段时间后，你会深刻体会到Django框架的架构，体会到如何使用Django的各个部件，到那个时候你就达到了融会贯通，融会贯通后就不必在乎一些概念性的东西，正所谓无招胜有招，就是这个道理。

所以，同学们在以后每天的自习课上学，要勤动手，减少看视频教程的时间，增加练习和思考的时间。

```

## 7. Helloworld中的目录结构

```
├── db.sqlite3   # 数据库
├── helloapp   # 一个应用
│   ├── admin.py  # 管理
│   ├── apps.py   # 应用的配置
│   ├── __init__.py
│   ├── migrations  # model到数据库映射的中间文件
│   │   └── __init__.py
│   ├── models.py  # model
│   ├── tests.py   # 在这里可以写测试代码
│   └── views.py   # 视图
├── helloworld  #  创建项目时创建的目录
│   ├── __init__.py
│   ├── settings.py  # 项目的配置文件
│   ├── urls.py  # 根路由
│   └── wsgi.py  # 
└── manage.py

```

## 8.Admin管理

1. 查看数据库

   ```
   sqlite> .tables
   django_migrations
   sqlite> 
   ```

   ​

2. 创建表（迁移数据库）

   ```
   python manage.py makemigrations
   python manage.py migrate  # 创建表
   ```


   ```

3. 创建超级用户

   ```
   python manage.py createsuperuser
   ```

4. 测试

   ```
   python manage.py runserver
   ```

   在浏览器中输入  http://localhost:8000/admin

# 内容回顾

## pyenv的使用

1. 安装pyenv

   ```
    git clone https://github.com/yyuu/pyenv.git ~/.pyenv
    echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
    echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile
    echo 'eval "$(pyenv init -)"' >> ~/.bash_profile
    ​```

2. 重新载入配置文件

    ```
    source ~/.bash_profile
    source ~/.bashrc
    ```

3. 查看pyenv使用指南

    ```
    pyenv --help
    ```

4. 查看使用pyenv可以安装的Python版本

    ```
    pyenv  install --list|grep python
    ```

5. 使用pyenv安装不同的版本

    ```
    pyenv install -v 3.6.0
    ```

6. 查看pyenv已经安装的版本

   ```
   pyenv versions
   ```

7. 使用 pyenv切换使用的Python版本

   ```
   pyenv global 3.6.0
   ```

8. 使用pyenv卸载指定的Python版本

   ```
   pyenv uninstall 2.7.10
   ```

9. 使用virtualenv管理不同的项目

   virtualenv本身是一个独立的项目，用以隔离不同项目的工作环境。


## 主要内容总结
1）课程目标
2）理解  B/S  C/S
3) 深刻理解 MVC  MVT
4）目录结构（urls.py   settings.py, views.py)
5) admin  (掌握如何创建超级用户)

作业：熟练掌握如何使用Django创建Helloworld项目。、

## 附录：常见问题解答

1. Ubuntu中如何设置IP地址
   python manage.py runserver    127.0.0.1:8000

   python manage.py runserver  0.0.0.0:8888

   sudo  ufw allow  8888

   http://10.31.161.xx:8888

   settings.py  ALLOW_HOSTS=['10.31.161.78']



