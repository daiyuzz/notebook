
## 一、原生文件上传

#### form.html

```python
    <img src="{{ url_for('static',filename='img/17.jpg') }}" width="300" alt="">
    <form action="" method="post" enctype="multipart/form-data">
        <p>修改头像 <input type="file" name="file"></p>
        <p><input type="submit" value="提交"></p>
    </form>
```

#### manage.py

```python
#文件上传的视图函数
@app.route('/upload/',methods=['GET','POST'])
def icon():
    img_name = None
    if request.method == 'POST' and 'file' in request.files:
        # return '有文件上传了'
        file = request.files.get('file')
        filename = file.filename #拿到文件名称
        #图片上传保存的路径
        imgPath = os.path.join(os.getcwd(), 'static/upload/'+filename)
        file.save(imgPath)
        img_name = filename
    return render_template('user/change_icon.html',img_name=img_name)
```

### 使用wtf和bootstrap渲染文件上传

```python
from flask import Flask,render_template,request
from flask_script import Manager
from flask_wtf import FlaskForm
from flask_wtf.file import FileField,FileAllowed,FileRequired
from wtforms import SubmitField
from flask_uploads import UploadSet,patch_request_class,configure_uploads,IMAGES
from flask_bootstrap import Bootstrap
import os
from PIL import Image

app = Flask(__name__)
app.config['MAX_CONTENT_LENGTH'] = 1024*1024*64
app.config['SECRET_KEY'] = 'abcdef'
app.config['UPLOADED_PHOTOS_DEST'] = os.path.join(os.getcwd(),'static/upload')
bootstrap = Bootstrap(app)
file = UploadSet('photos',IMAGES)
configure_uploads(app,file)
patch_request_class(app,size=None)
manager = Manager(app)

#自定义一个文件上传的表单类
class File(FlaskForm):
    photos = FileField('修改头像',validators=[FileRequired(message='文件不能为空'),FileAllowed(file,message='该文件类型不允许上传')])
    submit = SubmitField('提交')

@app.route('/')
def index():
    return render_template('index.html')

#生成随机的图片名称
def random_name(suffix,length=32):
    import string,random
    myStr = string.ascii_letters + '0123456789'
    return ''.join(random.choice(myStr) for i in range(length))+suffix

@app.route('/upload/',methods=['GET','POST'])
def icon():
    form = File()
    img_url = None
    if form.validate_on_submit():
        data = request.files.get('photos')
        suffix = os.path.splitext(data.filename)[-1]
        newName = random_name(suffix)
        file.save(data,name=newName)
        img_url = file.url(newName)

        img = Image.open(os.path.join(app.config['UPLOADED_PHOTOS_DEST'],newName))
        print(img.size)  # 获取图片大小
        # 设置尺寸
        img.thumbnail((128, 128))  # 当前缩放不是等比缩放 变成等比缩放
        img.save(os.path.join(app.config['UPLOADED_PHOTOS_DEST'],'s_'+newName))
    return render_template('user/wtf-uplods.html',form=form,img_url=img_url)

if __name__ == '__main__':
    manager.run()
```

#### 模板中的代码

```python
from flask import Flask,render_template,request
from flask_script import Manager
from flask_wtf import FlaskForm
from flask_wtf.file import FileField,FileAllowed,FileRequired
from wtforms import SubmitField
from flask_uploads import UploadSet,patch_request_class,configure_uploads,IMAGES
from flask_bootstrap import Bootstrap
import os
from PIL import Image

app = Flask(__name__)
app.config['MAX_CONTENT_LENGTH'] = 1024*1024*64
app.config['SECRET_KEY'] = 'abcdef'
app.config['UPLOADED_PHOTOS_DEST'] = os.path.join(os.getcwd(),'static/upload')
bootstrap = Bootstrap(app)
file = UploadSet('photos',IMAGES)
configure_uploads(app,file)
patch_request_class(app,size=None)
manager = Manager(app)

#自定义一个文件上传的表单类
class File(FlaskForm):
    photos = FileField('修改头像',validators=[FileRequired(message='文件不能为空'),FileAllowed(file,message='该文件类型不允许上传')])
    submit = SubmitField('提交')

@app.route('/')
def index():
    return render_template('index.html')

#生成随机的图片名称
def random_name(suffix,length=32):
    import string,random
    myStr = string.ascii_letters + '0123456789'
    return ''.join(random.choice(myStr) for i in range(length))+suffix

@app.route('/upload/',methods=['GET','POST'])
def icon():
    form = File()
    img_url = None
    if form.validate_on_submit():
        data = request.files.get('photos')
        suffix = os.path.splitext(data.filename)[-1]
        newName = random_name(suffix)
        file.save(data,name=newName)
        img_url = file.url(newName)

        img = Image.open(os.path.join(app.config['UPLOADED_PHOTOS_DEST'],newName))
        print(img.size)  # 获取图片大小
        # 设置尺寸
        img.thumbnail((128, 128))  # 当前缩放不是等比缩放 变成等比缩放
        img.save(os.path.join(app.config['UPLOADED_PHOTOS_DEST'],'s_'+newName))
    return render_template('user/wtf-uplods.html',form=form,img_url=img_url)

if __name__ == '__main__':
    manager.run()
```









## 二、发送邮件  flask-mail

pip install flask-mail

#### 设置临时环境变量

windows   set 名=值

Ubuntu下   export 名=值



### 单线程发送邮件

```python
from flask import Flask,render_template
from flask_script import Manager
from flask_mail import Mail,Message
import os

app = Flask(__name__)
app.config['MAIL_SERVER'] = 'smtp.1000phone.com'
app.config['MAIL_USERNAME'] = 'xialigang@1000phone.com'
app.config['MAIL_PASSWORD'] = os.environ.get('MAIL_PASSWORD','123456')

mail = Mail(app)
manager = Manager(app)

@app.route('/send_mail/')
def send_mail():
    msg = Message(subject='大郎',recipients=['793390457@qq.com'],sender=app.config['MAIL_USERNAME'])
    msg.html = render_template('email/activate.html',username='大郎')
    mail.send(message=msg)
    return '发送邮件'

if __name__ == '__main__':
    manager.run()
```



### 异步发送邮件

```python
from flask import Flask,render_template
from flask_script import Manager
from flask_mail import Mail,Message
import os
from threading import Thread

app = Flask(__name__)
app.config['MAIL_SERVER'] = 'smtp.1000phone.com'
app.config['MAIL_USERNAME'] = 'xialigang@1000phone.com'
app.config['MAIL_PASSWORD'] = os.environ.get('MAIL_PASSWORD','123456')

mail = Mail(app)
manager = Manager(app)

@app.route('/send_mail/')
def send_mail():
    msg = Message(subject='大郎',recipients=['793390457@qq.com'],sender=app.config['MAIL_USERNAME'])
    msg.html = render_template('email/activate.html',username='大郎')
    thr = Thread(target=async_send_mail,args=(msg,)) #创建线程 参数1为创建子线程 参数2为传递参数 类型为元组
    thr.start() #开启线程
    return '发送邮件'

def async_send_mail(msg):
    #开启程序上下文  把当前请求 作为同一个请求
    with app.app_context():
        mail.send(message=msg)

if __name__ == '__main__':
    manager.run()
```



