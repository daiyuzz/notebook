# django使用celery进行异步任务

## celery介绍

celery：任务队列
是一种在线程或机器间分发任务的机制

消息队列的输入是工作的一个单元，称为任务，独立的职称（Worker）进程持续的监视队列中是否有需要处理的新任务。

celery用消息通信，通常使用中间人(Broker)在客户端和worker间通信。这个过程从客户端像队列添加消息开始，之后中间人在把消息派送给worker


![2019-09-30 17-40-00屏幕截图](assets/20190930191049255_380846559.png =1144x)

### 在django中的应用
用户注册时发送的激活邮件，在网络不好的时候会非常占用时间，这时候就需要使用celery进行异步处理。
在项目根目录下新建一个`celery_tasks`的包，新建`tasks.py`文件，将发送邮件的代码移到该文件下

tasks.py
```python
# 使用celery

from celery import Celery
from django.conf import settings
from django.core.mail import send_mail

# 创建一个celery类的实例对象
app = Celery('celery_tasks.tasks', broker='redis://127.0.0.1:6379/8')

# 在任务处理者上加上下面四行代码，Django环境的初始化
import os
import django

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'dailyfresh.settings')
django.setup()


# 定义任务函数
@app.task
def send_register_active_email(to_email, username, token):
    '''发送激活邮件'''
    # 发邮件
    subject = '天天生鲜欢迎信息'
    message = ''
    html_message = '<h1>%s,欢迎您成为天天生鲜注册会员</h1>请点击下面的链接激活您的账户<br><a href="http://127.0.0.1:8000/user/active/%s">http://127.0.0.1:8000/user/active/%s</a>' % (
        username, token, token)

    from_email = settings.EMAIL_FROM
    receiver = [to_email]
    # message中有html时，使用html_message。
    send_mail(subject, message, from_email, receiver, html_message=html_message)

```
- 新建一个celery的实例
- 指定中间人redis的地址
- 在使用`app.task`修饰发送邮件函数
- celery中任务发出人、中间人、处理者可在一台服务器上也可在不同的服务器上
- `celery -A celery_tasks.tasks worker -l info`启动worker
- 需要在worker端添加上django的初始化代码，如上所示，无论是三者在同一台服务器还是不同服务器都需要。