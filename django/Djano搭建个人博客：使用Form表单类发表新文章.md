# Djano搭建个人博客：使用Form表单类发表新文章

## Forms表单类
在HTML中，表单是在`<form>...</form>`中的一些元素，它允许访客做类似输入文本、选择选项、操作对象或空间等动作，然后发送这些信息到服务端。一些表单界面元素（文本框或复选框）非常简单并内置在HTML中，而其他复杂些，像弹出日期选择等操作控件。

处理表单是一件挺复杂的事情。想想看Django的admin，许多不同的数据可能需要在一张表单中准备显示，渲染成HTML，使用方便的界面进行编辑，传到服务器，验证和清理数据，然后保存或跳过进行下一步处理。

Django表单系统的核心组件是Form类 ，它能够描述一张表单并决定它是如何工作及呈现的。

要使用Form类也很简单，需要在article/中创建forms.py文件，并写入如下代码：
```python
article/forms.py

from django import forms
from .models import ArticlePost

# 写文章的表单类
class ArticlePostForm(forms.ModelForm):
    class Meta:
        # 指明数据模型来源
        model = ArticlePost
        # 定义表单中包含的字段
        fields = ('title','body')
```
代码中`ArticlePostForms`类继承了Django的表单类`froms.ModelForm`，并在类中定义了内部类`class Meta`，指明了数据模型的来源，以及表单中应该包含数据模型的那些字段。

在`ArticlePost`模型中，`created`和`updated`字段为自动生成，不需要填入；`author`字段暂时固定为id=1的管理员用户，也不用填入；剩下的`title`和`body`就是表单中需要填入的内容了。

接下来，改写`article/views.py`，添加一个视图函数以处理写文章的请求：
```python
article/views.py

from django.shortcuts import render,redirect
from django.http import HttpResponse
from .forms import ArticlePostForm
from django.contrib.author.models import User

# 写文章的视图
def article_create(request):
    if request.method == 'POST':
        article_post_form = ArticlePostForm(data=request.POST)
        if article_post_form.is_valid():
            new_article = article_post_form.save(commit=False)
            new_article.author = User.objects.get(id=1)
            new_article.save()
            return redirect('article:article_list')
        else:
            return HttpResponse("表单内容有误，请重新填写。")
    else:
        article_post_form = ArticlePostForm()
        context = {'article_post_form':article_post_form}
        return render(request,'article/create.html',context)
```
分析一下上面的代码，当视图函数接收到一个客户端的`request`请求时，首先根据`request.method`判断用户是要提交数据（POST）、还是要获取数据（GET）：
    如果是用户提交数据，将POST给服务器的表单数据赋于`article_post_form`实例。然后使用Django内置的方法，`is_valid()`判断提交的数据是否满足模型要求。
    如果满足要求，保存表单中的数据（但是`commit=False`暂时不提交到数据库，因为author还未定），并指定author为id=1的管理员用户。然后提交到数据库，并通过redirect返回文章             列表，redirect可通过url地址的名字，反向解析到对应的url。
    如果不满足要求，则返回一个字符串”表单内容有误，请重新填写。“告诉用户出现什么问题。
    如果用户是获取数据，返回一个空的表单类对象，提供用户填写。
Form实例可以绑定到数据，也可以不绑定数据。
    如果绑定到数据，就能够验证该数据并将表单呈现为HTML并显示数据。
    如果不绑定数据，则无法进行验证，但仍然可以将空白的表单呈现为HTML。

要将数据绑定到表单，就将数据作为字典的第一个参数传递给Form类构造函数：

```python
data = {'subject':'hello','message':'Hi there','sender':'foo@example.com','cc_myself':True}
f = ContactForm(data)
```
Form对象的主要任务是验证数据。使用绑定后的Form实例，调用`is_valid()`方法验证并返回指定数据是否有效的布尔值：
```python
data = {'subject':'hello','message':'Hi there','sender':'foo@example.com','cc_myself':True}
f = ContactForm(data)
f.is_valid()

True
```

让我们尝试一些无效的数据。subject为空（错误，因为默认情况下是需要有字段的），并且sender不是有效的电子邮件地址：
```python
data = {'subject':'','message':'Hi','sender':'invalid email address','cc_myself':True}
f = ContactForm(data)
f.is_valid()

False
```
