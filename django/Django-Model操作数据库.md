# Django-Model操作数据库

## 一、数据库操作

### 1、创建model表

```python
from django.db import models

class userinfo(models.Model):
#如果没有models.AutoField，默认会创建一个id的自增列
name = models.CharField(max_length=30)
email = models.EmailField()
memo = models.TextField()
```


## 二、对数据进行增删改查

### 1、查
```python
models.UserInfo.objects.all()
models.UserInfo.objects.all().values('user')    #只取user列
models.UserInfo.objects.all().values_list('id','user')    #取出id和user列，并生成一个列表
models.UserInfo.objects.get(id=1)
models.UserInfo.objects.get(user='yangmv')
```

### 2、增

```python
models.UserInfo.objects.create(user='yangmv',pwd='123456')
或者
obj = models.UserInfo(user='yangmv',pwd='123456')
obj.save()
```

或者
```python
dic = {'user':'yangmv','pwd':'123456'}
models.UserInfo.objects.create(**dic)
```
获取刚上传数据的id可使用
```python
n = models.UserInfo.objects.create(user='yangmv',pwd='123456')
n.id
```



### 3、删
```python
models.UserInfo.objects.filter(user='yangmv').delete()

```

###  4、改

```python
models.UserInfo.objects.filter(user='yangmv').update(pwd='520')
```
或者
```python
obj = models.UserInfo.objects.get(user='yangmv')
obj.pwd = '520'
obj.save()
```

### 5、常用方法
```python
#获取个数
#
models.Tb1.objects.filter(name='seven').count()
#大于，小于
#
models.Tb1.objects.filter(id__gt=1) # 获取id大于1的值
models.Tb1.objects.filter(id__lt=10) # 获取id小于10的值
models.Tb1.objects.filter(id__lt=10, id__gt=1) # 获取id大于1 且 小于10的值
# in
#
models.Tb1.objects.filter(id__in=[11, 22, 33]) # 获取id等于11、22、33的数据
models.Tb1.objects.exclude(id__in=[11, 22, 33]) # not in
# contains
#
models.Tb1.objects.filter(name__contains="ven")
models.Tb1.objects.filter(name__icontains="ven") # icontains大小写不敏感
models.Tb1.objects.exclude(name__icontains="ven")
# range
#
models.Tb1.objects.filter(id__range=[1, 2]) # 范围bettwen and
# 其他类似
#
# startswith，istartswith, endswith, iendswith,
# order by
#
models.Tb1.objects.filter(name='seven').order_by('id') # asc
models.Tb1.objects.filter(name='seven').order_by('-id') # desc
# limit 、offset
#
models.Tb1.objects.all()[10:20]
# group by
from django.db.models import Count, Min, Max, Sum
models.Tb1.objects.filter(c1=1).values('id').annotate(c=Count('num'))
# SELECT "app01_tb1"."id", COUNT("app01_tb1"."num") AS "c" FROM "app01_tb1" WHERE "app01_tb1"."c1" = 1 GROUP BY "app01_tb1"."id"

```
