# django表单(五)

## 模型表单ModelForm

如果你正在构建一个数据库驱动的应用，那么你可能会有与Django的模型紧密映射的表单。比如，你有个BlogComment模型，并且你还想创建一个表单让大家提交评论到这个模型中。在这种情况下，写一个forms.Form类，然后在表单类中定义字段，这种一般创建表单的做法是冗余的，因为你已经在ORM模型model中定义了字段的属性和功能，完全没必要重新写一遍字段。

### 一、核心用法
基于这个原因，Django提供了一个辅助类帮助我们利用Django的ORM模型model创建Form。

像下面这样：
```python
from django.forms import ModelForm
from myapp.models import Article

# 创建表单类
class ArticleForm(ModelForm):
    class Meta:
        model = Article
        fields = ['pub_date','headline','content','reporter']


# 创建一个表单，用于添加文章
form = Article（）

# 创建表单修改已有的文章
article = Article.objects.get(pk=1)
form = ArticleForm(instance=article)

```

用法的核心是：
    1、首先从django.forms导入ModelForm；
    2、编写一个自己的类，继承ModelForm；
    3、在新类里，设置元类Meta；
    4、在Meta中，设置model属性为你要关联的ORM模型，这里是Article；
    5、在Meta中，设置fields属性为你要在表单中使用的字段列表；
    6、列表里的值，应该是ORM模型model中的字段名；

上面的例子中，因为model和form比较简单，字段数量少，看不出这么做的威力和效率。但如果是那种大型项目，每个模型的字段数量几十上百，这么做的收益将非常巨大，而且后面还有一招提高效率的大杀器，也就是一步保存数据的操作。

### 二、字段类型
生成的Form类中将具有和指定的模型字段对应的表单字段，顺序为fields属性列表中指定的顺序。

每个模型字段有一个对应的默认表单字段。比如，模型中的CharField表现成表单中的CharField。模型中的ManyToManyField字段会表现成MultipleChoiceField字段。下面是完整的映射列表：
| 模型字段    |表单字段     |
| --- | --- |
|   AutoField  |  在Form类中无法使用 |
| BigAutoField    |  在Form类中无法使用 |
|  BigIntegerField   |  IntegerField，最小-9223372036854775808，最大9223372036854775807.|
|   BooleanField  |  BooleanField   |
|CharField     |   CharField，同样的最大长度限制。如果model设置了null=True，Form将使用empty_value|
|  CommaSeparatedIntegerField   |    CharField |
|   DateField  |   DateField  |
|    DateTimeField |    DateTimeField |
| DecimalField    |   DecimalField  |
|   EmailField  |   EmailField  |
|  FileField   |   FileField  |
|  FilePathField   |   FilePathField  |
|   FloatField  |   FloatField  |
|   ForeignKey  |   ModelChoiceField  |
|  ImageField   |   ImageField  |
| IntegerField    |  IntegerField   |
|   IPAddressField  |   IPAddressField  |
|  GenericIPAddressField   | GenericIPAddressField    |
| ManyToManyField    |   ModelMultipleChoiceField  |
|  NullBooleanField   |  NullBooleanField   |
|   PositiveIntegerField  | IntegerField    |
|   PositiveSmallIntegerField  |  IntegerField   |
|  SlugField   |    SlugField |
|  SmallIntegerField   |  IntegerField  |
|    TextField | CharField，并带有widget=forms.Textarea参数|
|  TimeField   |  TimeField   |
| URLField    |  URLField   |


可以看出，Django在设计model字段和表单字段时存在大量的相似和重复之处。

ManyToManyField和ForeignKey字段类型属于特殊情况：
- ForeignKey别映射成为表单的django.forms.ModelChoiceField。它的选项是一个模型的QuerySet，也就是可以选择的对象的列表，但是只能选一个。

- ManyToManyField被映射称为表单类的django.forms.ModelMultipleChoiceField,它的选项也是一个模型的QuerySet，也就是可以选择的对象的列表，但是可以同时选择多个，多对多。

同时，在表单属性设置上，有下面的映射关系：
- 如果模型字段设置blank=True，那么表单字段的required设置为False。 否则，required=True。
- 表单字段的label属性根据模型字段的verbose_name属性设置，并将第一个字母大写。
- 如果模型的某个字段设置了editable=False属性，那么它表单类中将不会出现该字段。道理很简单，都不能编辑了，还放在表单里提交什么？
- 表单字段的help_text设置为模型字段的help_text。
- 如果模型字段设置了choices参数，那么表单字段的widget属性将设置成Select框，其选项来自模型字段的choices。选单中通常会包含一个空选项，并且作为默认选择。如果该字段是必选的，它会强制用户选择一个选项。 如果模型字段具有default参数，则不会添加空选项到选单中。

### 三、完整示例

```python
from django.db import models
from django.forms import ModelForm

TITLE_CHOICES = (
    ('MR', 'Mr.'),
    ('MRS', 'Mrs.'),
    ('MS', 'Ms.'),
)

class Author(model.Model):
    name = models.CharField(max_length=100)
    title = models.CharField(max_length=3,choices=TITLE_CHOICES)
    birth_date = models.DateField(blank=True,null=True)

    def __str__(self):
        return self.name

class Book(models.Model):
    name = models.CharField(max_length=100)
    authors = models.ManyToManyField(Author)

class AuthorForm(ModelForm):
    class Meta:
        model = Author
        fields=['name','title','birth_date']

class BookForm(ModelForm):
    class Meta:
        model = Book
        fields=['name','authors']
```
上面的ModelForm子类基本等同于下面的定义方式（唯一的区别是save()方法）

```python
from django import forms

class AuthorForm(forms.Form):
    name = forms.CharField(max_length=100)
    title = forms.CharField(max_length=3,widget=forms.Select(choices=TITLE_CHOICES),)
    birth_date = forms.DateField(required=False)

class BookForm(forms.Form):
    name = forms.CharField(max_length=100)
    authors = forms.ModelMultipleChoiceField(queryset=Author.objects.all())
```

### 四、ModelForm的验证

验证ModelForm主要分两步：
- 验证表单
- 验证模型实例

与普通的表单验证类似，模型表单的验证也是调用`is_valid()`方法或访问errors属性。模型的验证`（Model.full_clean()）`紧跟在表单的clean()方法调用之后。通常情况下，我们使用Django内置的验证器就好了。如果需要，可以重写模型表单的clean()来提供额外的验证，方法和普通的表单一样。