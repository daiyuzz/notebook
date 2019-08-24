
## 一、原生表单

form.html

```python
{% extends 'common/base.html' %}
{% block title %}
    原生表单
{% endblock %}
{% block pagecontent %}
{#    <form action="{{ url_for('check') }}" method="post">#}
    <form action="{{ url_for('form') }}" method="post">
        <p>用户名: <input type="text" name="username" placeholder="请输入用户名" maxlength="12"></p>
        <p>密码： <input type="password" name="userpass" placeholder="请输入密码..."></p>
        <p><input type="submit" value="提交"></p>
    </form>
{% endblock %}
```

manage.py

```python
@app.route('/form/')
def form():
    return render_template('form1.html')

#接收表单的数据
@app.route('/check/',methods=['POST'])
def check():
    print(request.form)
    return '提交过来了'
```

将两个路由地址合并为同一个

```python
@app.route('/form/',methods=['GET','POST'])
def form():
    if request.method == 'POST':
        print(request.form)
    return render_template('form1.html')

```



## 二、使用flask-wtf表单扩展库

作用：是一个用于表单处理的扩展库  提供表单的校验 csrf的功能

pip install flask-wtf



##### 使用

##### （1）字段类型

| 字段名称          | 字段类型   |
| :------------ | ------ |
| stringField   | 普通文本字段 |
| PasswordField | 密码框    |
| SubmitField   | 提交按钮   |
| TestAreaField | 多行文本域  |
| HiddenField   | 隐藏域    |
| DateField     | 日期     |
| DateTimeField | 日期时间   |
| IntergerField | 整形     |
| FloatField    | 浮点型    |
|               | 单选字段   |
|               | 下拉     |
|               | 文件上传字段 |
|               | 布尔字段   |



##### （2）验证器

| 验证器          | 说明             |
| ------------ | -------------- |
| DataRequired | 必填             |
| Length       | 长度 min max     |
| IPAddress    | ip地址           |
| Email        | 邮箱             |
| URL          | 地址             |
| Regexp       | 正则匹配           |
| EqualTO      | 验证两个字段值的正确性    |
| NumberRange  | 输入值的范围 min max |

##### 实例

在manage中

```python
from flask import Flask,render_template,request
from flask_script import Manager
from flask_bootstrap import Bootstrap
#导入自定义表单类的基类
from flask_wtf import FlaskForm
#导入表单的字段
from wtforms import StringField,PasswordField,SubmitField
#导入验证器
from wtforms.validators import Length,DataRequired


app = Flask(__name__)
bootstrap = Bootstrap(app)
#加密种子 csrf需要使用
app.config['SECRET_KEY'] = 'abcdedff'
manager = Manager(app)

class Login(FlaskForm):
    username = StringField('用户名',validators=[Length(min=6,max=12,message='用户名的长度为6~12为'),DataRequired(message='用户名不能为空!!!')])
    userpass = PasswordField('密码',validators=[Length(min=6,max=12,message='用户名的长度为6~12为'),DataRequired(message='密码不能为空!!!')])
    submit = SubmitField('登录')

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/form/',methods=['GET','POST'])
def form():
    #将表单类实例化
    form = Login()
    if request.method == 'POST':
        #验证是否存在正确的csrftoken和 数据的正确性 如果都正确则为真
        if form.validate_on_submit():
            # print(request.form)
            print(form.username.data)
    return render_template('form2.html',form=form)
from flask import Flask,render_template,request
from flask_script import Manager
from flask_bootstrap import Bootstrap
#导入自定义表单类的基类
from flask_wtf import FlaskForm
#导入表单的字段
from wtforms import StringField,PasswordField,SubmitField
#导入验证器
from wtforms.validators import Length,DataRequired


app = Flask(__name__)
bootstrap = Bootstrap(app)
#加密种子 csrf需要使用
app.config['SECRET_KEY'] = 'abcdedff'
manager = Manager(app)

class Login(FlaskForm):
    username = StringField('用户名',validators=[Length(min=6,max=12,message='用户名的长度为6~12为'),DataRequired(message='用户名不能为空!!!')])
    userpass = PasswordField('密码',validators=[Length(min=6,max=12,message='用户名的长度为6~12为'),DataRequired(message='密码不能为空!!!')])
    submit = SubmitField('登录')

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/form/',methods=['GET','POST'])
def form():
    #将表单类实例化
    form = Login()
    if request.method == 'POST':
        #验证是否存在正确的csrftoken和 数据的正确性 如果都正确则为真
        if form.validate_on_submit():
            # print(request.form)
            print(form.username.data)
    return render_template('form2.html',form=form)

```

##### 在模板中

```python
{% extends 'common/base.html' %}
{% block title %}
    原生表单
{% endblock %}
{% block pagecontent %}
    <form action="{{ url_for('form') }}" method="post">
        <p>{{ form.csrf_token }}</p>
        <p>{{ form.username.label() }} {{ form.username(style='color:red;',class='userclass',placeholder='请输入用户名') }}
            {% if  form.errors%}
            <span style="color:red;">{{ form.errors.username.0 }}</span>
            {% endif %}
        </p>
        <p>{{ form.userpass.label() }} {{ form.userpass() }}</p>
        <p>{{ form.submit() }}</p>
    </form>
{% endblock %}
```

#### 使用bootstrap渲染表单

```python
{% import 'bootstrap/wtf.html' as wtf %}
{% block pagecontent %}
    <div class="row">
        <div class="col-md-8">图片</div>
        <div class="col-md-4">{{ wtf.quick_form(form) }}
        </div>
    </div>
{% endblock %}
```

#### 自定义表单验证器

```python
class Login(FlaskForm):
    ...
    def validate_username(self,field):
        # print(field)
        if field.data == 'zhangsan':
        # if self.username.data == 'zhangsan':
            raise ValidationError('该用户已存在')
```
**注意：**

validate_ 验证的字段名称   为固定格式 

**所有字段和验证器方法的使用**

```python
class Login(FlaskForm):
    username = StringField('用户名',validators=[Length(min=6,max=12,message='用户名的长度为6~12为'),DataRequired(message='用户名不能为空!!!')])
    userpass = PasswordField('密码',validators=[Length(min=6,max=12,message='用户名的长度为6~12为'),DataRequired(message='密码不能为空!!!'),EqualTo('confirm',message='俩次密码输入不一致')])
    confirm = PasswordField('确认密码')
    info = TextAreaField('个人简介',validators=[Length(min=6,max=20,message='内容为6-20个长度'),DataRequired(message='内容不能为空')],render_kw={"style":"resize:none;",'placeholder':"请输入你此刻的感谢..."})
    hidde =  HiddenField()
    birth = DateField('出生日期')
    birth = DateTimeField('出生日期')
    age = IntegerField('年龄',validators=[NumberRange(min=6,max=99,message='年龄为6~99岁')])
    money = FloatField()

    sex = RadioField(choices=[('w','女'),('m','男')])
    address = SelectField(choices=[('1001','北京'),('1002','上海'),('1003','天津')])

    file = FileField('文件上传')

    argee = BooleanField('请仔细阅读以上条款')

    ip = StringField('IPV4',validators=[IPAddress(message='请输入正确的ip地址')])
    url = StringField('url地址',validators=[URL(message='输入正确的url地址')])
    email = StringField('email',validators=[Email(message='请输入正确的邮箱地址')])
    preg = StringField('手机号码',validators=[Regexp('^[1][3-8][0-9]{9}$',flags=re.I,message='请输入正确的手机号码')])
    submit = SubmitField('登录')
```
