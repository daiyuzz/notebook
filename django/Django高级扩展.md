# Django高级扩展

## 一、静态文件

css、js、图片、Json文件、字体文件等

```python
#配置setting.py文件
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR,'static')
]
```

## 二、中间件

### 1.概述

一个轻量级、底层的插件，可以介入Django的请求和响应

### 2.本质

一个Python类

### 3.方法

```python
__init__ :不需要传参数，服务器响应第一个请求的时候自动调用，用于确定是否启用该中间件
    
process_request(self,request) : 在执行视图之前被调用（分配url匹配视图之前），每个请求上都会调用，返回None或者HttpResponse对象
    
process_view(self,request,view_func,view_args,view_kwargs)
调用视图之前执行，每个请求都会调用，返回None或者HttpResponse对象

process_template_response(self,request,response)
在视图刚好执行完后调用，每个请求都会调用，返回None或者HttpResponse对象
使用render

process_response(self,request,response)
所有响应返回浏览器之前调用，每个请求都会调用，返回HttpResponse对象

process_exception(self,request,exception)
当视图跑出异常时调用，返回HttpResponse对象

```

### 4.执行位置

![1530434222873](C:\Users\带鱼\AppData\Local\Temp\1530434222873.png)

### 5.自定义中间件

工程目录下middleware目录下创建myApp目录

创建一个python文件

```python
from django.utils.deprecation import MiddlewareMixin
class MyMiddle(MiddlewareMixin):
    def process_request(self, request):
        print("get参数为：", request.GET.get("a"))
```

### 6.使用自定义中间件

配置settings.py文件,MIDDLEWARE中添加

```python
'middleware.myApp.myMiddle.MyMiddle'
```





## 三、上传图片

### 1.概述

文件上传时，文件数据存储在request.FILES属性中

注意：form表单要上传文件需要加enctype="multipart/form-data"

注意：上传文件必须是post请求

### 2.存储路径

在static目录下创建upfile目录用于存储接收上传的文件

配置settings.py文件

```python
MDEIA_ROOT=os.path.join(BASE_DIR,r'static\upfile')
```

### 3.代码示例

```python
<body>
    <form method="post" action="/savefile/" enctype="multipart/form-data">
        {%csrf_token%}
        <input type="file" name="file"/>
        <input type="submit" value="上传"/>
    </form>
```

```python
def upfile(request):
    return render(request, 'myApp/upfile.html')
import os
from django.conf import settings
def savefile(request):
    if request.method == "POST":
        f = request.FILES["file"]
        # 文件在服务器端的路径
        filePath = os.path.join(settings.MDEIA_ROOT, f.name)
        with open(filePath, 'wb') as fp:
            for info in f.chunks():
                fp.write(info)
        return HttpResponse("上传成功")
    else:
        return HttpResponse("上传失败")
```



## 四、分页

### 1.Paginator对象

##### 创建对象：

​	格式：Paginator(列表, 整数)

​	返回值：返回的分页对象

##### 属性：

​	count ：对象总数

​	num_pages：页面总数

​	page_range：页码列表，[1,2,3,4,5] ， 页码从1开始

##### 方法：

​	page(num) ： 获得一个Page对象，如果提供的页码不存在会抛出"InvalidPage"异常

##### 异常：

​	InvalidPage ： 当向page()传递的是一个无效的页码时抛出

​	PageNotAnInteger ：当向page()传递的不是一个整数时抛出

​	EmptyPage ： 当向page()传递一个有效值，但是该页面时没有数据时抛出

### 2.Page对象

##### 创建对象：

​	Paginator对象的page()方法返回得到Page对象

​	不需要手动创建

##### 属性：

​	object_list ： 当前页上所有的数据(对象)列表

​	number ： 当前页的页码值

​	paginator  ： 当前page对象关联的paginator对象

##### 方法：

​	has_next() ： 判断是否有下一页，如果有返回True

​	has_previous() ： 判断是否有上一页，如果有返回True

​	has_other_pages() ： 判断是否有上一页或下一页，如果有返回True

​	next_page_number() ： 返回下一页的页码，如果下一页不存在抛出InvalidPage异常

​	previous_page_number() ： 返回上一页的页码，如果上一页不存在抛出InvalidPage异常

​	len() ： 返回当前页的数据(对象)个数

### 3.Paginator对象与Page对象的关系

![1530434988320](C:\Users\带鱼\AppData\Local\Temp\1530434988320.png)

### 4.代码示例

```python
url(r'^studentpage/(\d+)/$', views.studentpage),
```

```python
from .models import Students
from django.core.paginator import Paginator
def studentpage(request, pageid):
    # 所有学生列表
    allList = Students.objects.all()
    paginator = Paginator(allList,6)
    page = paginator.page(pageid)
    return render(request,'myApp/studentpage.html',{"students":page})
```

```python
<body>
    <ul>
        {% for stu in students %}
        <li>
            {{stu.sname}}-{{stu.sgrade}}
        </li>
        {% endfor %}
    </ul>
    <ul>
        {% for index in students.paginator.page_range %}
            {% if index == students.number %}
                <li>
                {{index}}
            </li>
            {% else %}
                <li>
                <a href="/studentpage/{{index}}/">{{index}}</a>
            </li>
            {% endif %}
        {% endfor %}
    </ul>
</body>
```



