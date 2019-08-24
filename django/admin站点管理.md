## admin站点管理

## 一、概述

内容发布：负责添加、修改、删除内容

公告访问

## 二、配置Admin应用

```python
在setting.py文件中的INSTALLED_APPS中添加'django.contrib.admin' 
#默认是已经添加好的
```

## 三、创建管理员用户

```python
执行 python manager.py createsuperuser 依次输入用户名、邮箱、密码
```



## 四、汉化

```python
修改setting.py文件
LANGUAGE_CODE = 'zh-Hans'
TIME_ZONE = 'Asia/Shanghai'
```



## 五、管理数据表

### 1.修改admin.py文件

```python
from .models import Grades,Student
admin.site.register(Grades)
admin.site.register(Students)
```

### 2.自定义管理页面

```python
from .models import Grades,Students
# 注册
class GradesAdmin(admin.ModelAdmin):
    #列表页属性
    list_display = ['pk','gname','gdate','ggirlnum','gboynum','isDelete']
    list_filter = ['gname']
    search_fields = ['gname']
    list_per_page = 5
    # 添加、修改页属性
    # fields = ['ggirlnum','gboynum','gname','gdate','isDelete']
    fieldsets = [
        ("num",{"fields":['ggirlnum','gboynum']}),
        ("base",{"fields":['gname','gdate','isDelete']}),
    ]
admin.site.register(Grades, GradesAdmin)

```

### 3.属性说明

#### 列表属性：

```python
list_display   显示字段
list_filter    过滤字段
search_field   搜索字段
list_per_page  分页
```

#### 添加、修改页属性

```python
fields      属性的先后顺序
fieldsets   给属性分组
#注意：field和fieldsets不能同时使用
```

#### 关联对象

```python
需求：在创建一个班级时可以直接添加几个学生
class StudentsInfo(admin.TabularInline):#StackedInline
    model = Students
    extra = 2
class GradesAdmin(admin.ModelAdmin):
    inlines = [StudentsInfo]
```

#### 布尔值显示问题

```python
class StudentsAdmin(admin.ModelAdmin):
    def gender(self):
        if self.sgender:
            return "男"
        else:
            return "女"
    #设置页面列的名称
    gender.short_description = "性别"

    list_display = ['pk','sname','sage',gender,'scontend','sgrade','isDelete']
    list_per_page = 10
admin.site.register(Students,StudentsAdmin)
```

#### 执行动作位置

```python
class StudentsAdmin(admin.ModelAdmin):
    def gender(self):
        if self.sgender:
            return "男"
        else:
            return "女"
    #设置页面列的名称
    gender.short_description = "性别"

    list_display = ['pk','sname','sage',gender,'scontend','sgrade','isDelete']
    list_per_page = 10
admin.site.register(Students,StudentsAdmin)
```



#### 使用装饰器完成注册

```python
@admin.register(Students)
class StudentsAdmin(admin.ModelAdmin):
    def gender(self):
        if self.sgender:
            return "男"
        else:
            return "女"
    #设置页面列的名称
    gender.short_description = "性别"
    list_display = ['pk','sname','sage',gender,'scontend','sgrade','isDelete']
    list_per_page = 10
    # 执行动作的位置
    actions_on_top = False
    actions_on_bottom = True
# admin.site.register(Students,StudentsAdmin)
```

