request对象中有一个user属性，如果登陆成功，且把user对象保存在会话中，user属性的值是登录成功后的对象。
如果没有登录，==user属性的值是AnonymousUser对象==





## 关于shell的操作

#### 1.打开shell

python manage.py shell

#### 2.把需要查询的类导入

```python
import app.models import *
```

*表示全部导入

#### 3.查看类中的全部内容

类名.objects.all()

#### 4.可以用遍历来查询

pizzas = Pizza.objects.all()

for pizza in pizzas:

　　print(pizza.id, pizza)

可以得到具体实例的ID

#### 5.可以想看具体id下的属性的值

p = Pizza.models.get(id=1)

p = Pizza.models.get(pk=1)

#### 6.查看与他外键相关联的数据

p.topping_set.all() (topping 为类名, 为Pizaa类的外键)

对象名.关联的类名小写_set.all()

E.g:

　　grade = Grades.objects.get(pk=1)

　　studentList = grade.students_set.all()

就可以拿到Grade类里面id=1的全部学生的信息