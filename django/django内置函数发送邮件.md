# django内置函数发送邮件

## 激活邮件
```python
from django.core.mail import send_mail
from itsdangerous import TimedJSONWebSignatureSerializer as Serializer
from itsdangerous import SignatureExpired
```

在settings.py中设置邮箱配置
```python
# 发送邮件配置
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
# smtp服务
EMAIL_HOST = 'smtp.qq.com'
# 是否SSL加密，QQ邮箱需要
EMAIL_USE_SSL = True
EMAIL_PORT = 465
EMAIL_HOST_USER = '1461562548@qq.com'
EMAIL_HOST_PASSWORD = 'ipkkjaksqhnigefb'
EMAIL_FROM = 'daiyu<1461562548@qq.com>'
```

发送激活邮件时，包含激活链接如：http://127.0.0.1:8000/user/active/12
其中数字12是用户id，用来辨识是那个用户激活的信息。
如果直接暴露id的话会带来漏洞风险，所以需要将用户信息进行加密，使用第三方工具包`itsdangerous`
使用`pip install itsdangerous`进行安装

```python
serializer = Serializer(settings.SECRET_KEY,3600) # 有效时间3600s
info = {'confirm':user.id}
token = serializer.dumps(info)
# 加密之后的token是字节类型，需要编码为utf-8,decode默认为utf-8
token = token.decode()
# 发邮件
subject = 'xxx欢迎信息'
message = ''
from_email = settings.EMAIL_FROM
receiver = [email]
html_message =  '<h1>%s,欢迎您成为天天生鲜注册会员</h1>请点击下面的链接激活您的账户<br><a href="http://127.0.0.1:8000/user/active/%s">http://127.0.0.1:8000/user/active/%s</a>' % (username, token, token)

# message中有html内容时，使用html_message,但message为空。
send_mail(subject,message,from_email,receiver,html_message=html_message)

```

## 激活邮件确认
因为激活邮件地址为：`http://127.0.0.1:8000/user/active/xxx`,只需要截取active/之后的用户信息进行解密，然后将对应用户的`is_active`置为1即可。

urls.py

```python

urlpatterns = [
    url(r'^active/(?P<token>.*)$',ActiveView.as_view(),name='active')
]
```



下面是激活的类视图
```python
class ActiveView(View):
    '''用户激活'''
    def get(self,request,token):
        # 进行解密，获取激活所需要的信息
        serializer = Serializer(settings.SECRET_KEY,3600)
        try:
            info = serializer.loads(token)
            user = User.objects.get(id=user_id)
            user.is_active = 1
            user.save()
            return redirect(reverse('user:login'))
        except SignatureExpired as e:
            	return HttpResponse('激活链接已过期')

```