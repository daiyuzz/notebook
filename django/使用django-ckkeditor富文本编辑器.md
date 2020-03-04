# 使用django-ckkeditor富文本编辑器

前面我们已经实现了Markdown语法写文章了.但是文章评论用Markdown就不太合适了,你不能强求用户也花时间去熟悉语法.另外评论中通常还有表情, 带颜色的字体功能,这些也是Markdown不具备的.

因此富文本编辑器`Django-ckeditor`就派上用场了.

## 在后台使用Ckeditor
在虚拟环境安装`django-ckeditor`
`pip install django-ckeditor`
安装后在`settings.py`中注册app:

```python
...
INSTALLED_APPS = [
    ...
    'ckeditor',
    ...
]
...

```
接下来需要修改模型了.用django-ckeditor库自己的富文本字段`RichTextField`替换普通文本字段`TextField`:

```python
comment/models.py
...
# django-ckeditor
from ckeditor.fields import RichTextField
class Comment(models.Model):
    ...
    # 之前body=models.TextField()
    body = RichTextField()
    ...
   
```
每次更该模型后要进行**迁移数据**

为了方便测试,修改`comment/admin.py`文件,将评论模块注册到后台中:
```python
from django.contrib import admin
from .models import Comment

admin.site.register(Comment)
```

启动服务,进入后台评论页面,发现已经可以使用django-ckeditor了

功能相当齐全,字体,字号,颜色,连接,表情应有尽有.
如果我们只需要部分功能怎么办?比如**插入flash动画**基本用不到.另外似乎也没看到插入代码块的功能.

`ckeditor`允许你在`settings.py`中进行自定义配置:

```python
my_blog/settings.py

...
CKEDITOR_CONFIGS = {
    # django-ckeditor默认使用default配置
    'default': {
        # 编辑器宽度自适应
        'width':'auto',
        'height':'250px',
        # tab键转换空格数
        'tabSpaces': 4,
        # 工具栏风格
        'toolbar': 'Custom',
        # 工具栏按钮
        'toolbar_Custom': [
            # 表情 代码块
            ['Smiley', 'CodeSnippet'], 
            # 字体风格
            ['Bold', 'Italic', 'Underline', 'RemoveFormat', 'Blockquote'],
            # 字体颜色
            ['TextColor', 'BGColor'],
            # 链接
            ['Link', 'Unlink'],
            # 列表
            ['NumberedList', 'BulletedList'],
            # 最大化
            ['Maximize']
        ],
        # 加入代码块插件
        'extraPlugins': ','.join(['codesnippet']),
    }
}


```

在`toolbar_Custom`中定义需要使用的功能模块;没列出来的功能就不再显示了.代码块功能是编辑器自带的插件,需要在`extraPlugins`中指定使用.效果如下:


编辑器富文本搞定之后,还需要在前台界面中展示出来.富文本以类似`html`的格式进行保存,因此还要在展示评论的代码中加入`|safe`过滤器,防止浏览器进行转义.

修改`detail.html`中展示评论的部分代码:
```html
templates/article/detail.html

...

<!-- 显示评论 -->
<h4>共有{{ comments.count }}条评论</h4>
<div>
    {% for comment in comments %}
        ...
        <!-- 修改这里 -->
        <div>{{ comment.body|safe }}</div>
    {% endfor %}
</div>

...
```

## 代码高亮
代码高亮需要添加额外的插件**Prism**.在prism插件官方页面下载,将解压出来的`prism`放到静态文件目录`static\ckeditor\ckeditor\plugins\prism`中.

然后在Prism官网选择主题:
![](assets/20200207210710884_1876071586.png =800x)


- 根据喜好选择一个喜欢的主题
- 然后选择需要高亮的语言,不清楚就可以全选
- 勾选行号插件
- 最后点击`DOWNLOAD CSS`下载样式

在`static`目录中新建`prism`目录,将下载好的CSS文件放进去.

然后在需要代码高亮的**模板文件**中引用prism静态文件,对代码进行渲染:

```html
templates/article/detail.html

...

<script src="{% static 'ckeditor/ckeditor/plugins/prism/lib/prism/prism_patched.min.js' %}"></script>
<link rel="stylesheet" href="{% static 'prism/prism.css' %}">

...

```
将Prism,widget,lineutils插件添加到配置文件中.后面两个编辑器自带,不用单独下载,添上就可以了:

```python
my_blog/settings.py

...
CKEDITOR_CONFIGS = {
    'default': {
        ...
        # 添加 Prism 相关插件
        'extraPlugins': ','.join(['codesnippet', 'prism', 'widget', 'lineutils']),
    }
}

```


## 在前台使用Ckeditor

为了让用户在前台也能使用富文本编辑器,还得对代码稍加改动
首先需要把评论的表单传递到文章详情页面中.因此修改`articcle_detail`视图:

```python
...
# 引入评论表单
from comment.forms import CommentForm

...
# 文章详情
def article_detail(request, id):
    ...

    # 引入评论表单
    comment_form = CommentForm()
    context = { 
        ...
        'comment_form': comment_form,
    }
    ...

```

然后将`detail.html`原来评论表单中的正文部分(即前面章节写的`<textarea>`)替换如下:

```python

templates/article/detail.html

...

<!-- 发表评论 -->
<form ...>
{% csrf_token %}
    <div class="form-group">
        <label for="body">...</label>

        <!-- 将之前的<textarea>替换掉 -->
        <!-- <textarea type="text" 
                       class="form-control" 
                       id="body" 
                       name="body" 
                       rows="2"></textarea>  -->
        <div>
            {{ comment_form.media }}
            {{ comment_form.body }}
        </div>

    </div>
    <!-- 提交按钮 -->
    ...                   
</form>

...

```

其中的`comment_form.media`是编辑器自身的渲染代码,`comment_form.body`则是评论正文字段

看看效果:

![](assets/20200208163425830_772111272.png =800x)


## 宽度自适应
首先在配置文件中将宽度设置为`auto`,这一步我们已经做好了.
Ckeditor编辑器本身有一个`inline-block`的样式,阻碍了自适应效果,需要用`jquery`语法将其清除掉.在详情页底部加入代码:

```html
templates/article/detail.html

<!-- 注意这是错误的示范！ -->

...
<!-- 新增代码 -->
<script>
    $(".django-ckeditor-widget").removeAttr('style');
</script>

<!-- 这个已经有了 -->
{% endblock content %}

```

`$`符号代表Jquery语句.这句的意思是:找到页面中`class='djnago-ckeditor-widget'`容器,然后删除这个容器的`style`属性.

**看似没什么问题，然而Bug藏在细节中**。注意这是个Jquery语句，那么就要求运行之前先载入Jquery.js。然而在渲染页面时，包含`$`语句的{% block content %}会插入到base.html模板的Jquery.js标签的前面，导致语句不会生效，并且控制台会报出`$ is not defined`的错误。

解决方案是在base.html中新增专门用于拼接JavaScript脚本的位置，命名为{% block script %}。注意它必须放置在Jquery标签的后面：

```html
templates/base.html

...

<body>
    ...
    <!-- 已有代码 -->
    <script src="{% static 'jquery/jquery-3.3.1.js' %}"></script>
    ...
    <!-- 新增代码 -->
    {% block script %}{% endblock script %}
</body>

```

然后将`detail.html`中的JS代码放到这个块中:

```html

...

{% block script %}
<script>
    $(".django-ckeditor-widget").removeAttr('style');
</script>
{% endblock script %}

```

这种方法可以灵活的定义JS脚本的运行顺序，并且代码看起来更加整洁。推荐所有的JS代码都采取这种方法插入。

刷新页面，编辑器就能够宽度自适应了：

![](assets/20200208165904586_1216216254.png =800x)

发表含有代码块的评论，详情页面的显示如下：

![](assets/20200208170012260_1244603169.png =800x)


## 总结
现在，博文和其评论都可以漂亮的排版了。对于有些不喜欢Markdown的人来说，甚至可以连博文都使用django-cdeditor提供的富文本编辑器。我自己还是倾向用Markdown写文章：写作效率比好看更重要，并且主流网站几乎都支持Markdown，多平台发稿很方便。
