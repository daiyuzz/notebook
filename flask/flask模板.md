

模板引擎

说明：模板文件就是按照一定的书写规则的展示效果的HTML文件，模板引擎就是负责按照指定规则进行替换的工具



模板引擎选择jinja2



## 一、    渲染模板的方法

##### 1.将渲染的模板进行返回

render_template()

##### 2.渲染字符串返回

render_template_string()

##### 实例

```python
@app.route('/')
def index():
    #将模板内容响应给用户
    return render_template('index.html')
	#渲染一内容响应给客户
    return render_template_string('<h1 style='color:green;font-size:18px;'>原谅色</h1>)
```







## 二、模板的语法

1.变量

{{var}}

```python
#向模板文件中传参
return render_template('index.html',title='首页')

在index.html
文件中{{data.key}}
```



2.标签

{%标签名%}

##### 注意：

```python
在模板中使用字典中的键 需要像使用对象的方式调用
{{data.key}}
```



如果在模板中给定的变量不存在 则插入的是空字符串 不会报错

## 三、过滤器

过滤器使用管道符 | 来使用的 

1.{{var|abs}}返回一个数值的绝对值

2.default 设置默认值

​	只有当给定的变量不存在时 则执行默认值

​	当设置default的boolean的时候 会执行默认值

```python
<li>{{data.bool|default('我是默认值'，Boolean=True)}}</li>
```

3.first:取出变量中的第一个字符

4.last：取出变量中的最后一个字符

5.format：字符的格式化

```python
<li>{{ '我叫%s 我今年%d岁了 我的存款为 %.2f'|format('罗铁汉',38,23) }}</li>
```

6.length:返回变量值的长度

7.join：拼接成字符串

```python
<li>{{ [1,2,3,4]|join('') }}</li>
<li>{{ [1,2,3,4]|join('x') }}</li>
```

8.safe:不转义标签 原样显示

9.lower：转为小写

10.upper 转为大写

11.replace 替换

```python
<li>{{ data.string|replace('a','x') }}</li>
```

12.striptages 去除HTML标签



## 四、标签

##### 语法格式：{%标签名%}

##### （1）if

实例

```python
{% if data.bool %}
        <li>{{ data.bool }}值为真</li>
{% elif True %}
        <li>{{ True }}职位真</li>
{% else %}
        <li>{{ data.bool }}值为假</li>
{% endif %}
```



##### (2)for 循环

##### 实例

```python
{% for i in data.xxxx %}
{#  错误的迭代方法TypeError: 'bool' object is not iterable #}
{#    {% for i in data.bool %}#}
        <li>{{ i }}</li>
{% else %}
        <li>当迭代的变量不存在时 则执行else</li>
{% endfor %}
```

##### 注意：

break continue 不能够在这里使用

##### 迭代字典

```python
{% for k,v in data.items() %}
     <li>{{ k }}=>{{ v }}</li>
{% endfor %}
```



#### 获取当前迭代的状态

| 变量          | 描述             |
| ----------- | -------------- |
| loop.index  | 获取当前迭代的索引 从1开始 |
| loop.index0 | 获取当前迭代索引 从0开始  |
| loop.first  | 是否为第一次迭代       |
| loop.last   | 是否为最后一次迭代      |
| loop.length | 迭代长度           |



## 六、注释

{#多行注释#}





## 七、文件包含  include

相当于把一文件拷贝到当前的位置

##### 实例

```python
{%include 'common/header.html'%}
<div>我是中间的内容</div>
{%include 'common/footer.html'%}
```

##### 注意：

1.包含的公共文件中 只存放 公共的代码 除此之外什么也不要存在

2.导入的时候 如果文件在同一级别 直接导入就可以 如果包含在某个目录中 需要写出路径

```python
{% include 'common/header.html' %}
{% include 'test.html' %}
```



## 八、宏 macro

概念：类似python中的函数

##### 实例

在macro.html中

```python
{% macro input(name,type='text',value='') %}
    <input type="{{ type }}" name="{{ name }}" value="{{ value }}">
{% endmacro %}
```

##### 宏的调用

```python
{{ input('text','username','') }}
{{ input() }} 
{{ input(type='password',name='userpass') }}
```



## 宏的导入

##### （1）import

```python
{% import 'test.html' as test %}
{% import 'common/test.html' as test %}
<p>用户名： {{ test.input(type='password',name='userpass') }}</p>
```

##### (2)from import

```python
{% from 'test.html' import input %}
{% from 'common/test.html' import input %}
<p>用户名： {{ input(type='password',name='userpass') }}</p>
```

##### 注意

1.宏的调用只能在定义的下方去调用 否则未定义

2.宏如果存在形参 且没有默认值 则可以调用（没意义）

3.形参的默认值 要遵循默认值规则 有默认值的参数 放右侧

4.可以正常使用关键字参数





## 九、继承extends

语法：

1.{%extends%}继承某个模板

2.{%block%}挖坑和填坑

3.{{super()}}调用被替换掉的代码

##### base.html

```python
<!DOCTYPE html>
<html lang="en">
<head>
{% block header %}
    <meta charset="UTF-8">
    {% block meta %}
    {% endblock %}
    <title>{% block title%}首页{% endblock %}</title>
    <style>
        {% block style %}
            p{color:red;}
        {% endblock %}
    </style>
    {% block link %}
    {% endblock %}
    {% block script %}
    {% endblock %}
{% endblock %}
</head>
<body>
<header>头部</header>
{% block con %}
    我是中间的内容部分
{% endblock %}
<footer>尾部</footer>
</body>
</html>

```



##### index.html继承base.html

```python
{% extends 'common/base.html' %}
{% block title %}
我的首页
{% endblock %}
{% block style %}
    {{ super() }}
    p{color:green;}
{% endblock %}
{% block con %}
<p>我是首页的内容</p>
    我是首页的内容
{% endblock %}
```



##### 注意

如果当替换某个样式的时候 所有原来的样式都消失了  去查看是否使用了super（）





## 十、flask-bootstrap

安装

pip install flask-bootstrap

sudo pip3 install flask-bootstrap

使用：

继承bootstrap/base.html基础模板 改造成适用自己网站的base.html基础模板

##### 自己的base.html

```python
{% extends 'bootstrap/base.html' %}
{% block navbar %}
    <nav class="navbar navbar-inverse" style="border-radius: 0px;">
        <div class="container-fluid">
            <!-- Brand and toggle get grouped for better mobile display -->
            <div class="navbar-header">
                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse"
                        data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
                    <span class="sr-only">Toggle navigation</span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                <a class="navbar-brand" href="#"><span class="glyphicon glyphicon-signal" aria-hidden="true"></span></a>
            </div>

            <!-- Collect the nav links, forms, and other content for toggling -->
            <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
                <ul class="nav navbar-nav">
                    <li class="active"><a href="#">首页 <span class="sr-only">(current)</span></a></li>
                    <li><a href="#">发帖子</a></li>

                </ul>

                <ul class="nav navbar-nav navbar-right">
                    <form class="navbar-form navbar-left">
                    <div class="form-group">
                        <input type="text" class="form-control" placeholder="Search">
                    </div>
                    <button type="submit" class="btn btn-default">Submit</button>
                </form>
                    <li><a href="#">注册</a></li>
                    <li><a href="#">登录</a></li>
                    <li class="dropdown">
                        <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true"
                           aria-expanded="false">个人中心 <span class="caret"></span></a>
                        <ul class="dropdown-menu">
                            <li><a href="#">个人信息</a></li>
                            <li><a href="#">修改头像</a></li>
                            <li><a href="#">修改密码</a></li>
                            <li role="separator" class="divider"></li>
                            <li><a href="#">Separated link</a></li>
                            <li role="separator" class="divider"></li>
                            <li><a href="#">退出登录</a></li>
                        </ul>
                    </li>
                </ul>
            </div><!-- /.navbar-collapse -->
        </div><!-- /.container-fluid -->
    </nav>
{% endblock %}
{% block content %}
    <div class="container">
        {% block pagecontent %}
        	网页的中间内容部分写在当前的位置
        {% endblock %}
    </div>
{% endblock %}
```

##### 使用 index.html

```python
{% extends 'common/base.html' %}
{% block title %}
首页
{% endblock %}
```



## 十一、错误页面的定制

##### manage.py

```python

@app.errorhandler(404)
def page_not_found(e):
    return render_template('common/error.html',error=e,code=404)

@app.errorhandler(500)
def server_error(e):
    return render_template('common/error.html',error=e,code=500)
```

##### error.html

```python
{% extends 'common/base.html' %}
{% block title %}
    {{ code }}错误
{% endblock %}
{% block pagecontent %}
    <div class="alert alert-danger" role="alert">{{ error }}</div>
{% endblock %}
```



## 十二、视图传递多个参数

##### (1)原始传参

```python
@app.route('/')
def index():
    return render_template('index.html',arg1=1,arg2=2...)
```



##### (2)使用字典

```python
@app.route('/')
def index():
    return render_template('index.html',arg={arg1:1,arg2:2...})
	kwarg={arg1:1,arg2:2...}
    return render_template('index.html',``)
```



##### (3)使用全局变量g

```python
@app.route('/')
def index():
    g.name = '张三'
    g.age = 18
    return render_template('index.html')
模板中
    <ol>
        <li>{{ g.name }}</li>
        <li>{{ g.age }}</li>
    </ol>
```

##### (4)使用**locals()

```python
@app.route('/')
def index():
    name = '张三'
    age = 18
    print(locals())
    return render_template('index.html',**locals())
模板中
<li>{{ name }}</li>
<li>{{ age }}</li>
```



## 十三、url_for构造绝对的链接地址

```python
@app.route('/test/')
def test():
    print(url_for('index',_external=True))
    return 'test'
```





## 十四、加载静态资源

静态资源：图片，css，jss，视频、音频

##### 实例

```python
<img src="{{ url_for('static',filename='img/17.jpg') }}" alt="">
```

