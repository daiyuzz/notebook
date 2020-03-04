# 用django-notifications实现消息通知

怎么将未读的留言呈现给正确的用户呢？总不能用户自己去茫茫文章中寻找吧，那也太蠢了。给评论增加通知功能就是很流行的解决方案：比如微信朋友圈留言的通知、新浪微博留言的通知、以及各种社交平台的“小红点”。

本篇将以`django-notifications`为基础,非常高效的搭建一个简易的通知系统

## 发送通知
首先安装`django-notifications`:

```
pip install django-notifications-hq
```

注册app:
```python
my_blog/settings.py

...
INSTALLED_APPS = [
    ...
    'notifications',
    ...
]
...

```
在根路由中安装路径:
```python
my_blog/urls.py

...
urlpatterns = [
    ...
    path('inbox/notifications/',include(notifications.urls,namespace='notifications')),
    ...
    
]
```
注意这里的`notifications.urls`没有像之前一样用字符串,是为了确保模块安装到正确的命名空间中.

数据迁移
```
python manage.py migrate
```
app就安装好了
接下来你就可以在项目的**任何地方**发送通知了!就像这样:

```python
from notifications.signals import notify

notify.send(actor,recipient,verb,target,action_object)
```
其中的参数释义:
- `actor`:发送通知的对象
- `recipient`:接收通知的对象
- `verb`:动词短语
- `target`:链接到动作的对象(可选)
- `action_object`:执行通知的对象(可选)

有点绕,举个例子:杜塞(`actor`)在`Django搭建个人博客(target)`中对你`recipient`发表了`(verb)`评论`(action_object)`.

因为我们想要在用户`发表评论的时候发送通知`,因此修改一下发表评论的视图:

```python
comments/views.py

...
comments/views.py

...
from notifications.signals import notify
from django.contrib.auth.models import User

...
def post_comment(...):
    ...

    # 已有代码，创建新回复
    if comment_form.is_valid():
        ...

        # 已有代码，二级回复
        if parent_comment_id:
            ...

            # 新增代码，给其他用户发送通知
            if not parent_comment.user.is_superuser:
                notify.send(
                    request.user,
                    recipient=parent_comment.user,
                    verb='回复了你',
                    target=article,
                    action_object=new_comment,
                )

            return HttpResponse('200 OK')

        new_comment.save()

        # 新增代码，给管理员发送通知
        if not request.user.is_superuser:
            notify.send(
                    request.user,
                    recipient=User.objects.filter(is_superuser=1),
                    verb='回复了你',
                    target=article,
                    action_object=new_comment,
                )

        return redirect(article)
...

```
增加了两条`notify`的语句,分别位于两个`if`语句:
- 第一个`notify`:用户之间可以相互评论,因此需要发送通知.`if`语句是为了防止管理人员收到重复的通知.
- 第二个`notify`:普通用户回复时给管理员发动通知.

```
普通用户回复普通用户时，这段代码是不会发送通知给管理员的。如果需要发送给管理员，在第一个 notify 的 recipient 里以列表形式，将被回复用户和管理员添加进去即可
```

## 小红点
后台创建通知的逻辑已经写好了,但是如果不能在前端显示出来,那也没起作用.

而前端显示消息通知比较流行的**小红点**,流行得都已经泛滥了,尽管很多软件其实根本就不需要.另一种形式是消息**徽章**,即一个红色方框中带有消息条目的计数.这两种方式都会用到博客页面中.

在位置的选择上,`header`是很合适的,因为它在博客的所有位置都会显示,很符合通知本身的定位.

因此修改`header.html`:

```html
templates/header.html

<!-- 引入notifications的模板标签 -->
{% load notifications_tags %}
{% notifications_unread as unread_count %}

...

<!-- 已有代码，用户下拉框 -->
<li class="nav-item dropdown">
    <a class="nav-link dropdown-toggle" ...>

        <!-- 新增代码，小红点 -->
        {% if unread_count %}
            <svg viewBox="0 0 8 8"
                 width="8px"
                 height="8px">
                <circle cx="4"
                        cy="4"
                        r="4"
                        fill="#ff6b6b"
                        ></circle>
            </svg>
        {% endif %}

        {{ user.username }}
    </a>
    <!-- 已有代码，下拉框中的链接 -->
    <div class="dropdown-menu" ...>

        <!-- 新增代码，通知计数 -->
        <a class="dropdown-item" href="#">通知
            {% if unread_count %}
            <span class="badge badge-danger">{{ unread_count }}</span>
            {% endif %}
        </a>

        ...
        <a ...>退出登录</a>
    </div>
</li>

...

```

django-notifications自带简易的模板标签，可以在前台模板中调用重要的通知相关的对象，在顶部引入就可以使用了。比如unread_count是当前用户的未读通知的计数。

## 未读与已读

既然是通知,那么肯定能够分成`未读的`和`已读的`两种.在适当的时候,未读通知又需要转换为已读的.
现在我们来开发功能集中处理它.

新建一个app:

```python
python manage.py startapp notice
```

注册:
```
my_blog/settings.py

...
INSTALLED_APPS = [
    ...
    'notice',
]
```

根路由:

```python
...
urlpatterns=[
...
#notice
path('notice/',include('notice.urls',namespace='notice')),
]
```

接下来就是视图了,之前所有的视图都是用的`视图函数`,这次更进一步,用`类视图`来完成.

编写视图:

```python
notice/views.py

from django.shortcuts import render,redirect
from django.views import View
from django.views.generic import ListView
from django.contrib.auth.mixins import LoginRequiredMixin
from article.models import ArticlePost


class CommentNoticeListView(LoginRequiredMixin,ListView):
    """通知列表"""
    # 上下文的名称
    context_object_name = 'notices'
    # 模板位置
    template_name = 'notice/list.html'
    # 登录重定向
    login_url = '/userprofile/login/'
    
    # 未读通知的查询集
    def get_queryset(self):
        return self.request.user.notifications.unread()

class CommentNoticeUpdateView(View):
    """更新通知状态"""
    # 处理get请求
    def get(self,request):
        # 获取未读消息
        notice_id = request.GET.get('notice_id')
        # 更新单条通知
        if notice_id:
            article=ArticlePost.objects.get(id=request.GET.get('article_id'))
            request.user.notifications.get(id=notice_id).mark_as_read()
            return redirect(article)
        else:
        # 更新全部通知
            request.user.notifications.mark_all_read()
            return redirect('notice:list')
            
```
视图共两个:
    `CommentNoticeListView`:继承自`ListView`,用于展示所有的未读通知.`get_queryset`方法返回了传递给模板上下文对象,`unread()`方法是`django-notifications`提供的,用于获取所有未读通知的集合.另外视图还继承了"混入类" `LoginRequestMixin`,要求调用此视图必须先登录.


`CommentNoticeUpdateView`:继承自View,获得了如get,post等基础的方法.`mark_as_read()`,`mark_all_as_read`都是模块提供的方法,用于将未读通知转化为已读.`if`语句用来判断转换单条还是所有未读通知.

接下来就是新建`urls.py`了,写入:
```python
notice/urls.py

from django.urls import path
from .import views

app_name = 'notice'

urlpatterns=[
    # 通知列表
    path('list/',views.CommentNoticeListView.as_view(),name='list'),
    # 更新通知状态
    path('update/',views.CommentNoticeUpdateView.as_view(),name='update'),
]
```

