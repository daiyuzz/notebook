# django的模型层(一)

ORM将一个python的对象映射为数据库的一张关系表。它将SQL封装起来，程序员不需要再关心数据库的具体操作，只需要专注于自己本身代码和业务逻辑的实现。

于是，整个过程的实现过程就是：python代码，通过ORM转化成SQL语句，再通过pymysql去实际操作数据库。

![](assets/20190917103755719_1310703056.png =551x)

最典型的ORM就是SQLAlchemy了，如果你的web框架自身不带ORM系统，那么你可以安装使用它，SQLAlchemy使用者还是比较多的，本身功能也比较强大。

Django自带ORM系统，不需要额外安装别的ORM。当然，也可以安装并使用其他的ORM，比如SQLAlchemy，但是不建议这么做，因为Django系统非常庞大，集成完善，模型层与视图层、模板层结合的比较紧密，使用自带的ORM更方便更可靠，并且Django自带的ORM功能也非常强大，也不难学。

Django的ORM系统体现在框架内就是模型层。想要理解模型层的概念，关键在于理解用python代码的方式来定义数据库表的做法！一个python类，就是一个模型，代表数据库中的一张表！Django奉行Python优先的原则，一切基于python代码的交流，完全封装SQL内部细节。


## 模型和字段
一个模型（model）就是一个单独的、确定的数据信息源，包含了数据的字段和操作方法。通常，每个模型映射为一张数据库中的表。

基本的原则如下：
 - 每个模型在Django中的存在形式为一个Python类。
 - 每个模型都是`django.db.models.Model`的子类
 - 模型的每个字段（属性）代表数据表的某一列
 - Django将自动为你生成数据库访问API
 
### 简单示例

下面定义了一个‘人’，它具有`first_name`和`last_name`字段：
```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```
每一个字段都是一个类属性，每个类属性表示数据表中的一个列。

上面的代码相当于下面的原生SQL语句：
```python
CREATE TABLE myapp_person(
    "id" serial NOT NULL PEIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);
```
 注意：
 - 表名`myapp_person`是由Django自动生成的，默认格式为“项目名+下划线+小写类名”，你可以重写这个规则。
 - Django默认自动创建自增主键`id`，当然，你也可以自己指定主键。
 - 上面SQL语句基于`postgreSQL`语法。


通常，我们会将模型编写在其所属app下的models.py文件中，没有特别需求时，请坚持这个原则，不要给自己添加麻烦。

创建模型之后，在使用它之前，你需要先在settings文件中的`INSTALLED_APPS`处，注册models.py文件所在的`myapp`。看清楚了，是注册app，不是模型，也不是`models.py`。
```python
INSTALLED_APPS = [
#...
'myapp',
#...
]
```

当你每次对模型进行增、删、改时，请务必执行命令`python manage.py migrate`,让操作实际应用到数据库上。这里可以选择在执行migrate之前，先执行`python manage.py makemigrations`让修改动作保存到记录文件中，方便github等工具的使用。

### 模型字段fields
字段是模型最重要的内容之一，也是唯一必须的部分。字段在python中表现为一个类属性，体现了数据表中的一个列。请不要使用`clean`、`save`、`delete`等Django内置的模型API名字，防止命名冲突。下面是一个展示，注意字段的写法：
```python
from django.db import models

class Musician(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    instrument = models.CharField(max_length=100)

class Album(models.Model):
    artist = models.ForeignKey(Musician,on_delete=models.CASCADE)
    name = models.CharField(max_length=100)
    release_date = models.DateField()
    num_stars = models.integerField()
```

**字段命名约束**

Django不允许下面两种字段名：
- 与python关键字冲突。这会导致无法错误。例如：
    - class Example(models.Model): pass=models.IntegerField() # pass是python保留字！
- 字段名中不能出现两个以上下划线在一起，因为两个下划线是Django的查询语法。例如：
    - class Example(models.Model): foo__bar =models.IntegerField() # foo__bar有两个下划线在一起！

由于你可以自定义表名、列名，上面的规则可能被绕开，但是请养成良好的习惯。
SQL语言的join、where和select等保留字可以作为字段名，因为Django对他们进行了转义。


### 常用字段类型
字段类型的作用：
- 决定数据库中对应列的数据类型（如：INTEGER、VARCHAR、TEXT）
- HTML中对应的表单标签的类型，例如`<input type='text'>`
- 在admin后台和自动生成的表单中最小的数据验证需求。

Django内置了许多字段类型，它们都位于`django.db.models`中，例如`models.CharField`。这些类型基本满足要求，如果还不够，你也可以自定义字段。

下表列出了常用Django内置的字段类型，但不包括关系字段类型(字段名采用驼峰命名法)：
| 类型         |说明     |
| --- | --- |
|  AutoField      |  一个自动增加的整数型字段，通常不需要编写它，Django会自动帮你添加字段：`id=models.AutoField(primary_key=True)`,这是一个自增字段，从1开始计数。如果你要自己设置主键，请务必将`promary_key=True`。Django在一个模型中只允许有一个自增字段，且该字段必须为主键！   |
|  BooleanField   | 布尔值类型。默认值是None。在HTML表单中体现为CheckboxInput标签。如果要接收null值，请使用NullBooleanField。    |
|  CharField   | 字符串类型。必须接收一个max_length参数，表示字符串长度不能超过该值。默认的表单标签是input text。最常用的filed，没有之一！    |
|DateField     |  `class DateField(auto_now=False, auto_now_add=False, **options)`日期类型。一个Python中的datetime.date的实例。在HTML中表现为TextInput标签。在admin后台中，Django会帮你自动添加一个JS的日历表和一个“Today”快捷方式，以及附加的日期合法性验证。两个重要参数：（参数互斥，不能共存） `auto_now`:每当对象被保存时将字段设为当前日期，常用于保存最后修改时间。`auto_now_add`：每当对象被创建时，设为当前日期，常用于保存创建日期(注意，它是不可修改的)。设置上面两个参数就相当于给field添加了`editable=False和blank=True`属性。如果想具有修改属性，请用default参数。例子：`pub_time = models.DateField(auto_now_add=True)`，自动添加发布时间。   |
|  EmailField   | 邮箱类型，默认max_length最大长度254位。使用这个字段的好处是，可以使用DJango内置的EmailValidator进行邮箱地址合法性验证。    |
|  FileField   | class FileField(upload_to=None, max_length=100, **options)上传文件类型，后面单独介绍。    |
| ImageField    |	图像类型，后面单独介绍。     |
|    IntegerField |   整数类型，最常用的字段之一。取值范围-2147483648到2147483647。在HTML中表现为NumberInput标签。  |
|   GenericIPAddressField  |  class GenericIPAddressField(protocol='both', unpack_ipv4=False, **options)[source],IPV4或者IPV6地址，字符串形式，例如192.0.2.30或者2a02:42fe::4在HTML中表现为TextInput标签。参数protocol默认值为‘both’，可选‘IPv4’或者‘IPv6’，表示你的IP地址类型。   |
|TextField     | 大量文本内容，在HTML中表现为Textarea标签，最常用的字段类型之一！如果你为它设置一个max_length参数，那么在前端页面中会受到输入字符数量限制，然而在模型和数据库层面却不受影响。只有CharField才能同时作用于两者。    |
|  URLField   |    一个用于保存URL地址的字符串类型，默认最大长度200。 |
|  UUIDField   | 	用于保存通用唯一识别码（Universally Unique Identifier）的字段。使用Python的UUID类。在PostgreSQL数据库中保存为uuid类型，其它数据库中为char(32)。这个字段是自增主键的最佳替代品，后面有例子展示。    |


#### 这里有如何上传文件和图片的方法：
##### 1、FileField：
```
class FileField(upload_to=None,max_length=100,**options)[source]
```
上传文件字段（不能设为主键）。默认情况下该字段在HTML中表现为一个ClearableFileInput标签。在数据库内，我们实际保存的是一个字符串类型，默认最大值100，可以通过max_length参数自定义。真实的文件是保存在服务器的文件系统内的。

重要参数`upload_to`用于设置上传地址的目录和文件名。如下例所示：
```python
class myModel(models.Model):
    # 文件被上传至`MEDIA_ROOT/uploads`目录，MEDIA_ROOT在settings中设置
    upload = models.FileField(upload_to='uploads/')
    # 或者，被传到`MEDIA_ROOT/uoloads/2019/09/17`目录，增加了一个时间划分
    upload = models.FileField(upload_to='uploads/%Y/%m/%d/')
```
**Django很人性化的帮我们实现了根据日期生成目录的方式**

**`upload_to`参数也可以接收一个回调函数，该函数返回具体的路径字符串**,如下所示：
```python
def user_directory_path(instance,filename):
    #上传文件到MEDIA_ROOT/user_<id>/<filename>
    return 'user_{0}/{1}'.formate(instance.user.id,filename)

class MyModel(models.Model):
    upload = models.FileField(upload_to=user_directory_path)
```
例子中，`user_directory_path`这种回调函数，必须接收两个参数，然后返回一个Unix风格的路径字符串。参数 instance 代表一个定义了`FileField`的模型的实例，说白了就是当前数据记录。`filename`是原本的文件名。


##### 2、ImageField
```python
class ImageFeild(upload_to=None,height_field=None,width_field=None,max_length=100,**options)[source]
```
用于保存图像文件的字段，其基本用法和特性与FileField一样，只不过多了两个属性height和width。默认情况下，该字段在HTML中表现为一个ClearableFileInput标签。在数据库内，我们实际保存的是一个字符串类型，默认最大长度为100，可以通过max_length参数自定义。真实的图片是保存在服务器的文件系统内的。

使用Django的ImageFeild需要提前安装pillow模块。

使用FileField或者ImageField字段的步骤：
    1、在settings文件中，配置`MEDIA_ROOT`，作为你上传文件在服务器的基本路径(为了性能考虑，这些文件不会被存储在数据库中)。再配置个`MEDIA_URL`，作为公用URL，指向上传文件的基本路径。请确保web服务器的用户账号对该目录具有写的权限。
    2、添加FileField或者ImageField字段到你的模型中，定义好`upload_to`参数，文件最终会放在`MEDIA_ROOT`目录的`upload_to`子目录中。
    3、所有真正被保存到数据库中的，只是指向你上传文件路径的字符串而已。可以通过url属性，在Django的模板中方便访问这些文件。例如，假设你有一个ImageField字段，名叫`mug_shot`，那么可以在Django模板的HTML文件中，可以使用`{{object.mug_short.url}}`来获取该文件。其中object用你具体的对象名称代替。
    4、可以通过`name`和`size`属性，获取文件的名称和大小信息。
##### 安全建议
无论你如何保存上传的文件，一定要注意他们的内容和格式，避免安全漏洞！务必对所有上传文件进行安全检查，确保它们不出问题！如果你不加检查就盲目的让任何人上传文件到你的服务器文档根目录内，比如上传一个CGI或PHP脚本，很可能就会被访问的用户执行，这具有致命的危害。

##### 3、FilePathField
```python
class FilePathField(path=None, match=None, recursive=False, max_length=100, **options)[source]
```

一种用来保存文件路径信息的字段。在数据表内以字符串的形式存在，默认最大长度100，可以通过max_length参数设置。

它包含有下面的一些参数：

- path：必须指定的参数。表示一个系统绝对路径。

- match:可选参数，一个正则表达式，用于过滤文件名。只匹配基本文件名，不匹配路径。例如foo.*\.txt$，只匹配文件名foo23.txt，不匹配bar.txt与foo23.png。

- recursive:可选参数，只能是True或者False。默认为False。决定是否包含子目录，也就是是否递归的意思。

- allow_files:可选参数，只能是True或者False。默认为True。决定是否应该将文件名包括在内。它和allow_folders其中，必须有一个为True。

- allow_folders： 可选参数，只能是True或者False。默认为False。决定是否应该将目录名包括在内。

比如：
```
FilePathField(path="/home/images", match="foo.*", recursive=True)
```
它只匹配/home/images/foo.png，但不匹配/home/images/foo/bar.png，因为默认情况，只匹配文件名，而不管路径是怎么样的。

##### 4、UUIDField
数据库无法自己生成uuid，因此需要如下使用default参数：

```python
import uuid
from django.db import models

class MyUUIDModel(models.Model):
    id = models.UUIDField(primary_key=True,default=uuid.uuid4,editable=False)

```
