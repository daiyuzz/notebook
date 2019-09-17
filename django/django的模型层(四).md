# django的模型层(四)

## 多对多中间表详解

我们都知道对于ManyToMany字段，Django采用的是第三张中间表的方式。通过这第三张表，来关联ManyToMany的双方。下面我们根据一个具体的例子，详细解说中间表的使用。

### 一、默认中间表

首先，模型是这样的：
```python
class Person(models.Model):
    name = models.CharField(max_length=128)

    def __str__(self):
        return self.name
    
class Group(models.Model):
    name = models.CharField(max_length=128)
    members = models.ManyToManyField(Person)

    def __str__(self):
        return self.name

```
在Group模型中，通过members字段，以ManyToMany方式与Person模型建立了关系。

让我们到数据库内看一下实际的内容，Django为我们创建了三张数据表，其中的app1是应用名。

![](assets/20190917172542435_1444920613.png =596x)
然后我在数据库中添加了下面的Person对象：
