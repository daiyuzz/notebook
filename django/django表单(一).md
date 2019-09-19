# django表单(一)

### 一、HTML表单的概述
Django开发的是动态Web服务，而非单纯提供静态页面。动态服务的本质在于和用户进行互动，接收用户的输入，根据输入的不同，返回不同的内容给用户。返回数据是我们服务器后端做的，而接收用户输入就需要靠HTML表单。表单`<form>...</form>`可以收集其内部标签中的用户输入，然后将数据发送到服务端。

一个HTML表单必须指定两样东西：
- 目的地：用户数据发送的目的URL
- 方式：发送数据所使用的HTTP方法

### 二、Django的form表单
通常情况下，我们需要自己手动在HTML页面中，编写form标签和其内的其它元素。但这费时费力，而且有可能写得不太恰当，数据验证也比较麻烦。有鉴于此，Django在内部集成了一个表单模块，专门帮助我们快速处理表单相关的内容。
**Django的表单模块给我们提供了下面三个主要功能：**
- 准备和重构数据用于页面渲染
- 为数据创建HTML表单元素
- 接受和处理用户从表单发送过来的数据

**Form相关的对象包括：**
- Widget：用来渲染成HTML元素的工具，如：forms.Textarea对应HTML的标签
- Field：Form对象中的一个字段，如EmailField表示Email字段，如果这个字段格式不是有效的email格式，就会产生错误。
- Form：一系列Field对象的集合，负责验证和显示HTML元素
- Form Media：用来渲染表单的CSS和JavaScript资源

**Form Objects**
Form对象封装了一系列Field和验证规则，Form类都必须直接或间接继承自`django.forms.Form`，定义Form有两种方式：

- 方法一：直接继承`Form`
```python
from django import forms
class ContactForm(forms.Form):
 subject = forms.CharField(max_length=100,label='主题')
 message = form.CharField(widget=forms.TextArea)
 sender = form.EmailField()
 cc_myself = forms.BooleanField(required=False)
```
- 方法二：结合`Model`，继承`django.forms.ModelForm`

```python
#models.py
class Contact(models.Model):
 title = models.CharField(max_length=30)
 content = models.CharField(max_length=20)

#form.py
class ConotactForm(ModelForm):
 class Meta:
 model = Contact
 field = ('title','content') #只显示model中指定的字段

```
编写Django的form表单，非常类似我们在模型系统里编写一个模型。在模型中，一个字段代表数据表的一列，而form表单中的一个字段代表`<form>`中的一个`<input>`元素。

## 使用表单

### 一、编写表单类
我们可以通过Django提供的Form类来自定生成上面的表单，不需要再手动在HTML中编写。

首先，在你当前的app内新建一个`forms.py`文件,然后输入下面的内容：
```python
from django import forms

class NameForm(forms.Form):
    your_name = forms.CharField(label='Your name',max_length=100)

```
要点：
- 提前导入forms模块
- 所有表单类都要继承forms.Form类
- 每个表单的字段都有自己的字段类型比如CharField，他们分别对应一种HTML语言中的`<form>`元素中的表单元素。这一点和Django模型系统设计非常相似。
- 例子中的label用于设置书名标签
- `max_length`限制最大长度为100。它能同时起两个作用，一是在浏览器页面限制用户输入不可超过100个字符，二是在后端服务器验证用户输入长度不可超过100.

**(警告：由于浏览器页面是可以被篡改、伪造、禁用、跳过的，所有的HTML手段的数据验证只能防止意外不能防止恶意行为，是没有安全保证的，破坏分子完全可以跳过浏览器的防御手段伪造发送请求！所以，在服务器后端，必须将前端当做“裸机”来对待，再次进行完全彻底的数据验证和安全防护！)**

每个Django每单的实例都有一个内置的`is_vaild()`方法，用来验证接受的数据是否合法。如果所有数据都合法，那么该方法将返回True，并将所有的表单数据转存到它的一个叫`clean_data`的属性中，该属性是一个字典类型的数据。

当我们将上面的表单渲染成真正的HTML元素，其内容如下：
```python
<label for="your_name">Your_name</label>
<input id="your_name" type="text" name="your_name" maxlength="100" required/>
```
***一定要注意，它不包含<form>标签本身以及提交按钮！！！**为什么要这样？方便你自己控制表单动作和CSS，JS以及其它类似bootstrap框架的嵌入！

### 二、视图处理
需要在视图中，实例化我们编写好的表单类。

```python
# views.py

from django.shortcuts import render
from django.http import HttpResponseRedirect

from .forms import NameForm

def get_name(request):
    # 如果form通过POST方法发送数据
    if request.method == 'POST':
        # 接受request.POST参数构造form类的实例
        form = NameForm(request.POST)
        # 验证数据是否合法
        if form.is_valid():
            # 处理form.cleaned_data中的数据
            # ...
            # 重定向到一个新的URL
            return HttpResponseRedirect('/thanks/')

    # 如果是通过GET方法请求数据，返回一个空的表单
    else:
        form = NameForm()

    return render(request, 'name.html', {'form': form})
```

要点是：
- 对于GET方法请求页面时，返回空的表单，让用户可以填入数据；
- 对于POST方法，接收表单数据，并验证；
- 如果数据合法，按照正常业务逻辑继续执行下去；
- 如果不合法，返回一个包含先前数据的表单给前端页面，方便用户修改。

通过表单`is_bound`属性可以获知一个表单已经绑定数据，还是一个空表。

### 三、模板处理
在Django的模板中，我们只需要按下面处理，就可以得到完整的HTML页面：

```python
<form action="/your-name/" method="post">
    {% csrf_token %}
    {{ form }}
    <input type="submit" value="Submit" />
</form>
```
要点：
- `<form>...</form>`标签要自己写；
- 使用POST的方法时，必须添加{% csrf_token %}标签，用于处理csrf安全机制；
- {{ form }}代表Django为你生成其它所有的form标签元素，也就是我们上面做的事情；
- 提交按钮需要手动添加！

提示：默认情况下，Django支持HTML5的表单验证功能，比如邮箱地址验证、必填项目验证等等。


### 四、高级技巧
上面的例子中，只有一个用户名输入框，太简单了，实际上有更多的表单元素。看下面的例子：
```python
from django import forms

class ContactForm(forms.Form):
    subject = forms.CharField(max_length=100)
    message = forms.CharField(widget=forms.Textarea)
    sender = forms.EmailField()
    cc_myself = forms.BooleanField(required=False)

```
这个例子就有4个框组了。实际上Django的表单模块为我们内置了许多表单字段，如下所示：
- BooleanField
- CharField
- ChoiceField
- TypedChoiceField
- DateField
- DateTimeField
- DecimalField
- DurationField
- EmailField
- FileField
- FilePathField
- FloatField
- ImageField
- IntegerField
- GenericIPAddressField
- MultipleChoiceField
- TypedMultipleChoiceField
- NullBooleanField
- RegexField
- SlugField
- TimeField
- URLField
- UUIDField
- ComboField
- MultiValueField
- SplitDateTimeField
- ModelChoiceField
- ModelMultipleChoiceField

每一个表单字段类型都对应一种Widget类，每一种Widget类都对应了HMTL语言中的一种input元素类型，比如`<input type="text">`。需要在HTML中实际使用什么类型的input，就需要在Django的表单字段中选择相应的field。比如要一个`<input type="text">`，可以选择一个CharField。

一旦你的表单接收数据并验证通过了，那么就可以从form.cleaned_data字典中读取所有的表单数据，下面是一个例子：

```python
# views.py

from django.core.mail import send_mail

if form.is_valid():
    subject = form.cleaned_data['subject']
    message = form.cleaned_data['message']
    sender = form.cleaned_data['sender']
    cc_myself = form.cleaned_data['cc_myself']

    recipients = ['info@example.com']
    if cc_myself:
        recipients.append(sender)

    send_mail(subject, message, sender, recipients)
    return HttpResponseRedirect('/thanks/')
```


### 五、使用表单模板

#### 1、表单渲染格式
前面我们通过{{ form }}模板语言，简单地将表单渲染到HTML页面中了，实际上，有更多的方式：

- {{ form.as_table }} 将表单渲染成一个表格元素，每个输入框作为一个`<tr>`标签
- {{ form.as_p }} 将表单的每个输入框包裹在一个`<p>`标签内 tags
- {{ form.as_ul }} 将表单渲染成一个列表元素，每个输入框作为一个`<li>`标签

注意：你要自己手动编写`<table>`和`<ul>`标签。

下面是将上面的ContactForm作为{{ form.as_p }}的例子：
```python
<p><label for="id_subject">Subject:</label>
    <input id="id_subject" type="text" name="subject" maxlength="100" required /></p>
<p><label for="id_message">Message:</label>
    <textarea name="message" id="id_message" required></textarea></p>
<p><label for="id_sender">Sender:</label>
    <input type="email" name="sender" id="id_sender" required /></p>
<p><label for="id_cc_myself">Cc myself:</label>
    <input type="checkbox" name="cc_myself" id="id_cc_myself" /></p>
    
```
注意：Django自动为每个input元素设置了一个id名称，对应label的for参数。

#### 2、手动渲染表单字段
直接`{{ form }}`虽然好，啥都不用操心，但是往往并不是你想要的，比如你要使用CSS和JS，比如你要引入`Bootstarps`框架，这些都需要对表单内的input元素进行额外控制，那怎么办呢？手动渲染字段就可以了。

可以通过`{{ form.name_of_field }}`获取每一个字段，然后分别渲染，如下例所示：

```python
{{ form.non_field_errors }}
<div class="fieldWrapper">
    {{ form.subject.errors }}
    <label for="{{ form.subject.id_for_label }}">Email subject:</label>
    {{ form.subject }}
</div>
<div class="fieldWrapper">
    {{ form.message.errors }}
    <label for="{{ form.message.id_for_label }}">Your message:</label>
    {{ form.message }}
</div>
<div class="fieldWrapper">
    {{ form.sender.errors }}
    <label for="{{ form.sender.id_for_label }}">Your email address:</label>
    {{ form.sender }}
</div>
<div class="fieldWrapper">
    {{ form.cc_myself.errors }}
    <label for="{{ form.cc_myself.id_for_label }}">CC yourself?</label>
    {{ form.cc_myself }}
</div>
```

其中的label标签甚至可以用label_tag()方法来生成，于是可以简写成下面的样子:

```python
<div class="fieldWrapper">
    {{ form.subject.errors }}
    {{ form.subject.label_tag }}
    {{ form.subject }}
</div>
```
这样子是不是更加灵活了呢？但是灵活的代价就是我们要写更多的代码，又偏向原生的HTML代码多了一点。

#### 3、渲染表单错误信息
注意上面的例子中，我们使用`{{ form.name_of_field.errors }}`模板语法，在表单里处理错误信息。对于每一个表单字段的错误，它其实会实际生成一个无序列表，参考下面的样子：
```
<ul class="errorlist">
    <li>Sender is required.</li>
</ul>
```
这个列表有个默认的CSS样式类errorlist，如果你想进一步定制这个样式，可以循环错误列表里的内容，然后单独设置样式：

```
{% if form.subject.errors %}
    <ol>
    {% for error in form.subject.errors %}
        <li><strong>{{ error|escape }}</strong></li>
    {% endfor %}
    </ol>
{% endif %}
```
一切非字段的错误信息，比如表单的错误，隐藏字段的错误都保存在`{{ form.non_field_errors }}`中，上面的例子，我们把它放在了表单的外围上面，它将被按下面的HTML和CSS格式渲染：
```
<ul class="errorlist nonfield">
    <li>Generic validation error</li>
</ul>
```

#### 4、循环表单的字段
如果你的表单字段有相同格式的HMTL表现，那么完全可以循环生成，不必要手动的编写每个字段，减少冗余和重复代码，只需要使用模板语言中的`{% for %}`循环，如下所示：

```python
{% for field in form %}
    <div class="fieldWrapper">
        {{ field.errors }}
        {{ field.label_tag }} {{ field }}
        {% if field.help_text %}
        <p class="help">{{ field.help_text|safe }}</p>
        {% endif %}
    </div>
{% endfor %}
```

下表是{{ field }}中非常有用的属性，这些都是Django内置的模板语言给我们提供的方便：

|           属性           |                          说明                          |
| ----------------------- | ----------------------------------------------------- |
| {{ field.label }}        | 字段对应的label信息                                      |
| {{ field.label_tag }}    | 自动生成字段的label标签，注意与`{{ field.label }}`的区别。    |
| {{ field.id_for_label }} | 自定义字段标签的id                                       |
| {{ field.value }}        | 当前字段的值，比如一个Email字段的值`someone@example.com `    |
| {{ field.html_name }}    | 指定字段生成的input标签中name属性的值                       |
| {{ field.help_text }}    | 字段的帮助信息                                           |
| {{ field.errors }}       | 包含错误信息的元素                                       |
| {{ field.is_hidden }}    | 用于判断当前字段是否为隐藏的字段，如果是，返回True             |
| {{ field.field }}        | 返回字段的参数列表。例如`{{ char_field.field.max_length }}` |


#### 5、不可见字段的特殊处理
很多时候，我们的表单中会有一些隐藏的不可见的字段，比如`honeypot`。我们需要让它在任何时候都仿佛不存在一般，比如有错误的时候，如果你在页面上显示了不可见字段的错误信息，那么用户会很迷惑，这是哪来的呢？所以，通常我们是不显示不可见字段的错误信息的。

Django提供了两种独立的方法，用于循环那些不可见的和可见的字段，`hidden_fields()`和`visible_fields()`。这里，我们可以稍微修改一下前面的例子：
```python
{# 循环那些不可见的字段 #}
{% for hidden in form.hidden_fields %}
{{ hidden }}
{% endfor %}
{# 循环可见的字段 #}
{% for field in form.visible_fields %}
    <div class="fieldWrapper">
        {{ field.errors }}
        {{ field.label_tag }} {{ field }}
    </div>
{% endfor %}
```

#### 6、重用表单模板
如果你在自己的HTML文件中，多次使用同一种表单模板，那么你完全可以把表单模板存成一个独立的HTML文件，然后在别的HTML文件中通过include模板语法将其包含进来，如下例所示：

```python
# 实际的页面文件中:
{% include "form_snippet.html" %}

-----------------------------------------------------

# 单独的表单模板文件form_snippet.html:
{% for field in form %}
    <div class="fieldWrapper">
        {{ field.errors }}
        {{ field.label_tag }} {{ field }}
    </div>
{% endfor %}
```
如果你的页面同时引用了好几个不同的表单模板，那么为了防止冲突，你可以使用with参数，给每个表单模板取个别名，如下所示：

```
{% include "form_snippet.html" with form=comment_form %}
```
在使用的时候就是：

```
{% for field in comment_form %}
......
```

如果你经常做这些重用的工作，建议你考虑自定义一个内联标签，这已经是Django最高级的用法了。