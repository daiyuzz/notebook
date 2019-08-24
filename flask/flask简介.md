## 4flask

## 一、web框架的简介

M 模型 负责数据的操作

V 视图     负责数据的展示

C  控制器   控制你的M的操作及视图模板的渲染

在python中叫做MVT

M 模型 负责数据的操作

V  控制你的M的操作及视图模板的渲染  业务逻辑的操作

T  templates 模板 负责数据的展示



## 二、构架

BS brower-》server

CS client-》server



## 三、FLASK

概念：flask是一个非常小的web框架  被称为微型框架  只提供一个强健的核心 其它的都是通过第三方扩展库来实现



组成：

​	1.调试  路由 WSGI

​	2.模板引擎 jinja2（就是由flask核心人员开发的模板引擎）

使用：

​	安装 pip install flask

案例：

```python
from flask import Flask
app = Flask(__name__)#实例化flask

#路由地址 根据用户不同的url进行处理
@app.route('/')
def index(): #处理当前请求的函数
    return 'hello Flask'

if __name__=='__main__':
    app.run() #运行当前的flask
```



## 四、视图函数

##### （1）无参路由

```python
#路由地址和视图函数名称  是否同名没有关系
#
@app.route('/test/')
def test():
    return"我是测试使用的视图函数"
```

##### （2）带一个参数的路由

http://127.0.0.1:5000/page/10/

```python
@app.route('/page/<pagenum>/') #参数的语法格式  /路由器名称/<形参名>/
def page(pagenum):
    return "当前页码为{}".format(pagenum)
```



##### (3)带多个参数

```python
#带多个参数
#
@app.route('/arg/<age>/<name>/')
def getarg(age,name):
    return '我叫{}我今年{}岁了'.format(name,age)

@app.route('/arg/<name>_<age>/')
def getarg(age,name):
    return '我叫{}我今年{}岁了'.format(name,age)
```



##### （4）限制参数的类型

```python
#参数类型
# @app.route('/argtype/<arg>/')
# @app.route('/argtype/<int:arg>/') #限定参数类型为int
# @app.route('/argtype/<float:arg>/') #限定参数类型为float
# @app.route('/argtype/<string:arg>/') #限定参数类型为string 默认就是字符串
@app.route('/argtype/<path:arg>/') #其实path就是string 但是path会将路由地址后面的所有的路由或者值都认为是一个值 /不在作为分隔符来使用
def argtype(arg):
    print(type(arg))
    print(arg)
    return '获取参数类型的视图函数'
```

##### 注意：

​	1.路由地址末尾的/建议加上  因为如果输入的时候没有默认的/浏览器会自动帮你加上

​	2.形参名字写在路由的<>中间

​	3.参数默认类型都是string



## 五、视图函数的响应

##### （1）return字符串进行响应

```python
@app.route('/response/')
def res():
    return '我是响应'，404  #响应一个指定标准的状态码
```

##### (2)通过make_response构造响应

导入：

​	from flask import make_response

```python
@app.route('/make_response/')
def makeResponse():
    res=make_response('我是响应内容')
    res=make_response('我是响应的内容'，404)
    return res
```



## 六、重定向 redirect

作用：从一个地址跳向另一个地址

导入

from flask import redirect

实例

```python
@app.route('/')
def index():  #处理当前请求的函数
    return 'hello Flask'
#重定向
@app.route('/redirect')
def redirect_index():
    return redirect('/')  #参数为路由的地址
	return redirect('/argtype/redirect_index/')  #带参数路由地址的重定向
```



##### url_for 通过视图函数名称 反向构造出路由地址

导入

from flask import redirect，url_for

实例

```python
@app.route('/redirect/')
def redirect_index():
    url = url_for('test')
    url = url_for('getarg',name='zhangdan',age=18)#带两个参数
    return url
```

注意：

如果给定的视图函数名称不存在 则抛出异常

##### url_for 和 redirect 组合使用

```python
@app.route('/redirect')
def redirect_index():
    return redirect(url_for('test'))
	return redirect(url_for('getarg',name='zhangsan',age=18)) #带多个参数
```



## 七、abort终止  

概念：

在视图函数中处理的时候 可以使用abort抛出指定状态码的错误  下面代码不在执行 需要抛出标准http的状态码

from flask import abort

实例

```python
@app.route('/abort/')
def my_abort():
    abort(404)
    abort(500)
    return '抛出状态码'
#捕获500的错误
@app.errorhandler(500)
def server_error(e):
    return '现在能到到了吗{}'.format(e)
#捕获404的错误信息
@app.errorhandler(404)
def server_error(e):
    return '您访问的页面被外星人劫走了'
```





## 八、app.run()参数说明

| 参数       | 参数说明 | 默认值       |
| -------- | ---- | --------- |
| host     | 主机名  | 127.0.0.1 |
| port     | 端口号  | 5000      |
| debug    | 调试   | False     |
| threaded | 多线程  | False     |

实例

```python
if __name__=='__main__':
    #app.run(debug=True)  #开启调试模式
    app.run(host='0.0.0.0',port=5001,debug=True,threaded=True)
```



## 十、请求request

说明

request是由flask框架为我们提供好的对象 使用时 只要导入即可

用户在请求的时候 框架会为当前用户创建一个request（请求的对象）包含当前用户请求的所有信息



导入

from flask import request



1.url 用户请求的完整的url

2.base_url  去除get传参

3.host_url 只有主机和端口号的url

4.path    获取请求的路由地址

5.method  请求的方法

6.args   获取get传参

7.form  获取表单传递过来的数据

8.file  获取文件传递过来的数据

9.headers  获取用户请求的头信息

10.cookies  获取用户请求过来的所有cookies

11.json     获取用户请求过来的json数据

实例

```python
@app.route('/request/')
def get_request():
    print('用户请求的完整的url',request.url)
    print('去除get传参后的url',request.base_url)
    print('只有主机和端口号的url',request.host_url)
    print('获取请求的路由地址',request.path)
    print(' 请求的方法',request.method)
    print('获取拼凑的get传参',request.args)
    print('获取拼凑的get传参',request.args.get('name'))
    print('获取拼凑的get传参',request.args.get('age'))
    print('获取表单传递过来的数据',request.form)
    print('获取文件上传过来的数据',request.files)
    print('获取用户请求过来的头信息',request.headers)
    print('获取用户请求过来的所有cookie',request.cookies)
    print('获取用户请求过来的json数据',request.json)
    return 'request对象'

```



## 十一、会话控制cookie和session

***cookie***

设置cookie

```python
response.set_cookie(
	key, #设置键
    value，#设置值
    max_age=None, #过期时间
    path='/' #当前cookie的存储路径
)
```

##### 获取cookie

```python
@app.route('/get_cookie/')
def get_cookie():
    print(request.cookies)
    return request.cookies.get('name','default默认值')
```

##### 删除cookie

```python
#清除cookie
@app.route('/del_cookie')
def del_cookie():
    res = make_response('清除cookie')
    res.delete_cookie('name')
    return res
```

cookie存储值为明文存储   安全性低

cookie存在客户端 （浏览器中）

cookie默认存活时间为 当前浏览结束（关闭当前浏览器）



#### session

session的使用需要一个secret_key来进行加密生产加密的字符串

```python
app.config['SECRET_KEY'] = 'secretkey'
```

会给cookie设置一个唯一的标识符sessionld服务器端会通过cookie携带着惟一的sessionld来区分哪一个用户的请求 如果客户端的cookie被禁用了，那么服务器端的session将无法使用   session基于cookie

##### 设置session

```python
#设置session
@app.route('/set_session/')
def set_session():
     默认存活当前浏览器结束
    session['username'] = '张三'
    return '设置session'
```





##### 获取session及过期时间

```python
#设置session
@app.route('/set_session/')
def set_session():
    session.permanent = True #设置session持久化存储
    #设置当前session的存活时间60秒 如果当前设置失败 那么存活时间为1月
    app.permanent_session_lifetime = timedelta(seconds=60)
    session['username'] = '张三'
    return '设置session'
```



##### 获取session

```python
#获取session
@app.route('/get_session/')
def get_session():
    return session.get('username','default默认值')
```



##### 删除session

```python
@app.route('/del_session/')
def del_session():
    #删除 key为username的session
    session.pop('username')
    #删除所有session
    # session.clear()
    return '删除session'
```









## 十二、flask-script扩展

简介：

就是一个flask终端运行的解析器 通过不同参数来设置flask的启动项



##### 安装：

sudo pip3 install flask-script

##### 使用：

```python
from flask_script import Manager #导入终端运行的解析器
app = Flask(__name__)
manager = Manager(app)
...
if __name__ == '__main__':
    manager.run()
```



##### 启动参数

| -h        | 主机   |
| --------- | ---- |
| -p        | 端口号  |
| -d        | 调试   |
| -r        | 重新加载 |
| -threaded | 多线程  |

python manage.py runserver -h

python manage.py runserver -h0.0.0 -p5000 -d -r -threaded

python manage.py runserver -d -r







## 十三、蓝本蓝图 Blueprint

概述

当所有代码越来越多的时候 在manage.py 中 很明显是不合理的 我们需要将不同功能的视图函数存放在不同的 文件中 使我们的项目的目录结构更加的清晰



##### 使用

##### user.py用户的处理

```python
from flask import Blueprint

user = Blueprint('user',__name__)

@user.route('/login/')
def login():
    return '登录'
```

manage.py中

```python
from mysession import mysession
from user import user
#http://127.0.0.1:5000/login/
app.register_blueprint(user) #注册蓝本
#http://127.0.0.1:5000/user/login/
app.register_blueprint(user,url_prefix='/user') #注册蓝本并添加前缀
```

##### 蓝本的重定向

```python
@app.route('/')
def index():
    # return '首页'
    return redirect('/user/login/')
    return redirect(url_for('user.login')) #使用url_for反向构造出路由的时候 需要指定当前的视图函数 是哪一个蓝本对象的
```





## 十四、请求钩子函数

在manage文件中使用

| 钩子函数                 | 功能描述           |
| -------------------- | -------------- |
| before_first_request | 第一次请求之前        |
| before_request       | 每次请求之前         |
| after_request        | 每次请求之后         |
| teardown_request     | 每次请求之后 即使有异常出现 |

##### 实例

```python
@app.before_first_request
def before_first_request():
    print('before_first_request')

@app.before_request
def before_request():
    print('before_request')
    if request.method == 'GET' and request.path == '/form/':
        abort(500)

@app.after_request
def before_request(r):
    print('before_request',r)
    return r

@app.teardown_request
def teardown_request(r):
    print('teardown_request')
    return r
```

