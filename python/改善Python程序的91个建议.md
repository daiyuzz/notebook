# 改善Python程序的91个建议



## 建议7:将常量集中到一个文件

在Python中应该如何使用常量呢?一般来说有以下两种方式:

- 通过命名风格来提醒使用者该变量代表的意义为常量,如常量名所有字母大写,用下划线连接各个单词.然而这种方式并没有实现真正意义上的常量,其对应的值仍然可变,这只是一个约定俗成的风格.

- 通过自定义的类实现常量功能.这要求符合"命名全为大写"和"值一旦绑定便不可再修改" 这两个条件.下面是一种比较常见的解决办法:

```python
import sys


class _const:
    class ConstError(TypeError):
        pass

    class ConstCaseError(ConstError):
        pass

    def __setattr__(self, key, value):
        if self.__dict__.__contains__(key):
            raise self.ConstError("Can't change const.%s" % key)
        if not key.isupper():
            raise self.ConstError("const key '%s' is not all uppercase" % key)
        self.__dict__[key] = value


sys.modules[__name__] = _const()

```
如果上面代码对应的模块名为const,使用的时候只需要`import const`,便可以直接定义常量了,如下代码:
```python
import const
const.COMPANY="IBM"

```
上面的代码中常量一旦赋值便不可以再更改,因此`const.COMPANY="SAP"` 会抛出 const.ConstError的异常,而常量名如果小写,如 `const.name="python"`,也会抛出const.ConstCaseError的异常.


无论采用那种方式实现常量,都提倡将常量集中到一个文件中,因为这样有利于维护,一旦需要修改常量的值,可以集中统一进行修改.采用第二种方式实现常量可以这么做:将存放常量的文件命名为 `constant.py`,并且在其中定义一些列常量



## 建议10:充分利用Lazy evaluation的特性

Lazy evaluation常别译为"延迟计算" "惰性计算".指的是仅仅在真正需要执行计算的时候才计算表达式的值.充分利用 Lazy evaluation的特性带来的好处主要体现在以下两个方面:

- 避免不必要的计算,带来性能上的提升.
在编程过程中,如果对于  or 条件表达式应该将值为真可能性较高的变量写在or前面,而 and 则应该推后.
- 节省空间,使得无限循环的数据结构成为可能



## 建议12:不推荐使用type来进行类型检查

## 建议13:尽量转为浮点类型后再做除法

## 建议14:警惕eval()的安全漏洞
Python中eval()函数将字符串str当成有效的表达式来求值并返回计算结果.其函数声明如下:
`eval(expression[,globals[,locals]])`
其中,参数globals为字典形式,locals为任何映射对象,他们分别表示全局和局部命名空间.

"eval is evil"(eval是邪恶的).这是一句广为人知的对eval的评价,它主要针对的是eval()的安全性.那么eval()存在什么样的安全漏洞呢?来看一个简单的例子:


## 建议15:使用enumerate()获取序列迭代的索引和值


## 建议16:分清==与is的使用场景
is表示对象标识符,而==表示的意思是相等,显然两者不是一个东西.
is的作用是用来检查对象的标识符是否一致,也就是比较两个对象在内存上是否拥有同一块内存空间,它并不适用于来判断两个字符串是否相等.x is y 仅当x和y是同一个对象的时候才返回true,
== 用来判断两个对象的值是否相等.

所以判断两个对象是否相等应该使用==而不是is


## 建议17:考虑兼容性,尽可能使用Unicode

## 建议18:构建合理的包层次来管理module
包中的模块可以通过"."访问符进行访问,即"包名.模块名".

## 建议19:有节制的使用from...import语句

## 建议20:优先使用absolute import来导入模块

## 建议22:使用with自动关闭资源

## 建议24:遵循异常处理的几点基本原则
python中常用的异常处理语法是try except else finally,他们可以有多种组合,如:
`try-except`
`try-except-else`
`try-finally`
`try-except-else-finally`


## 建议25:避免finally中可能发生的陷阱
无论try语句中是否有异常抛出,finally语句总是会被执行.由于这个特性,finally语句经常被用来做一些清理工作,比如打开一个文件,在抛出异常后在finally语句中对文件句柄进行关闭.

但使用finally时,也要特别小心一些陷阱.

## 建议27:连接字符串应优先使用join而不是+
使用+时,字符串的连接时间复杂度近似为O(n^2)
![](assets/20191215185943071_452702237.png =800x)

使用join()方法连接字符串的时间复杂度为O(n)
所以,字符串连接,特别是大规模字符串处理,应该尽量优先选择join而不是+


## 建议28:格式化字符串时尽量使用.format方式而不是% 


## 建议29:区别对待可变对象和不可变对象

## 建议31:记住函数传参既不是传值也不是传引用



## 建议39:使用Counter进行计数统计
### 使用dict
```python
some_data = ['a', '2', 2, 4, 5, '2', 'b', 4, 7, 'a', 5, 'd', 'a', 'z']
count_frq = dict()
for item in some_data:
    if item in count_frq:
        count_frq[item] += 1
    else:
        count_frq[item] = 1

print(count_frq)
#{'a': 3, '2': 2, 2: 1, 4: 2, 5: 2, 'b': 1, 7: 1, 'd': 1, 'z': 1}
```

### 使用defaultdict
```python
# 使用defaultdict

from collections import defaultdict

some_data = ['a', '2', 2, 4, 5, '2', 'b', 4, 7, 'a', 5, 'd', 'a', 'z']

count_frq = defaultdict(int)
for item in some_data:
    count_frq[item] += 1

print(count_frq)

# defaultdict(<class 'int'>, {'a': 3, '2': 2, 2: 1, 4: 2, 5: 2, 'b': 1, 7: 1, 'd': 1, 'z': 1})

```

### 使用set和list
```python
some_data = ['a', '2', 2, 4, 5, '2', 'b', 4, 7, 'a', 5, 'd', 'a', 'z']
count_set = set(some_data)
count_list = []
for item in count_set:
    count_list.append((item, some_data.count(item)))


# [(2, 1), (4, 2), (5, 2), (7, 1), ('z', 1), ('2', 2), ('d', 1), ('b', 1), ('a', 3)]

```

### collections.Counter(推荐)
```python
from collections import Counter
some_data = ['a', '2', 2, 4, 5, '2', 'b', 4, 7, 'a', 5, 'd', 'a', 'z']
print(Counter(some_data))
//使用elements()方法来获取Counter中的值
print(list(Counter(some_data).elements()))
//利用most_common()方法可以找出前N个出现频率最高的元素以及他们对应的次数
print(Counter(some_data).most_common(2))
print(Counter(some_data)['a'])

#Counter({'a': 3, '2': 2, 4: 2, 5: 2, 2: 1, 'b': 1, 7: 1, 'd': 1, 'z': 1})
#['a', 'a', 'a', '2', '2', 2, 4, 4, 5, 5, 'b', 7, 'd', 'z']
#[('a', 3), ('2', 2)]
# 3
```


## 建议 51:用mixin模式让程序更加灵活

每个类都有一个`__base__`属性,它是一个元组,用来存放所有的基础类.与其他静态语言不同,python语言中的基类 在运行中可以动态改变.所以当我们向其中增加新的基类时,这个类就拥有了新的方法,也就是说所谓的混入(mixin)


## 建议 52:用发布订阅模式实现松耦合
发布订阅模式(publish/subscrible或pub/sub)是一种编程模式,消息的发送者不会发送消息给特定的接收者,而是将发布的消息分为不同的类别直接发布,并不关注订阅者是谁.
而订阅者可以对一个或多个类别感兴趣,且直接接收感兴趣的消息,并且不关注是哪个发布者发布的消息.

这种发布者和订阅者的解耦可以允许更好的可扩放性和更为动态的网络拓扑,故受到了大家的喜爱.



## 建议 53:用状态模式美化代码
所谓状态模式,就是当一个对象的内在状态改变时允许改变其行为,但这个对象看起来像是改变了其类.
状态模式主要用于控制一个对象状态的条件表达式过于复杂的情况,其可把状态的逻辑转移到表示不同状态的一系列类中,进而把复杂的判断逻辑简化.



## 建议 54:理解built-1n objects


## 建议 55:`__init__()`不是构造方法
很多pythoner会有这样的误解,认为`__init__()`方法是类的构造方法.因为从表面上看它确实很像构造方法:当需要实例化一个对象的时候,使用`a=Class(args..)`便可以返回一个类的实例,其中args的参数与`__init__()`方法中申明的参数一样.可是事实真相是怎样的呢?我们通过例子说明.

```python
class A(object):
    def __new__(cls, *args, **kwargs):
        print(cls)
        print(args)
        print(kwargs)
        print("-------")
        instance = object.__new__(cls)
        print(instance)

    def __init__(self, a, b):
        print("init gets called")
        print("self is", self)
        self.a, self.b = a, b


a1 = A(1, 2)
print(a1.a)
print(a1.b)

```
输出如下:
```python
<class '__main__.A'>
(1, 2)
{}
-------
<__main__.A object at 0x7f8a07503518>
Traceback (most recent call last):
  File "/home/daiyu/Documents/python/改善Python程序的91个建议/建议 55:__init__()不是构造方法.py", line 17, in <module>
    print(a1.a)
AttributeError: 'NoneType' object has no attribute 'a'


```

我们原本期望的是能够正确的输出a和b的值,可是运行却抛出了异常.除了异常外还有来自对`__new__()`方法调用所产生的输出,可是我们明明没有直接调用`__new__()`方法,原因在哪里?实际上`__init__()`并不是真正意义上的构造方法,`__init__()`方法所做的工作实在类的对象创建好之后进行变量的初始化.`__new__()`方法才会真正创建实例,是类的构造方法.

这两个方法都是object类中的默认的方法,继承自object的新式类,如果不覆盖这两个方法将会默认调用object中对应的方法.上面的程序抛出异常是因为`__new__()`方法中并没有显示返回对象,因此实际上a1为None,当去访问实例属性a时抛出`AttributeError: 'NoneType' object has no attribute 'a'`的错误也就不难理解了


我们来看看`__new__()`方法和`__init__()`方法的定义.

- `object.__new__(cls[,args...]):`其中cls代表类,args为参数列表
- `object.__init__(self [,args]):`其中self代表实例对象,args为参数列表

这两个方法的不同点,总结如下:
- 根据python文档可知,`__new__()`方法是静态方法,而`__init__()`方法为实例方法
- `__new__()`方法一般需要返回类的对象,当返回类的对象时将会自动调用`__init__()`方法进行初始化,,如果没有对象返回,则`__init__()`方法不会被调用.`__init__()`方法不需要显示返回,默认为None,否则会在运行时抛出`TypeError`
- 当需要控制实例创建的时候使用`__new__()`方法,而控制实例初始化的时候使用`__init__()`方法
- 一般情况下不需要覆盖`__new__()`方法,但当子类继承自不 可变类型,如str,int,unicode或者tuple的时候,往往需要覆盖该方法
- 当需要覆盖`__new__()`和`__init__()`方法的时候这两个方法的参数必须保持一致,如果不一致将会导致异常.示例如下:
```python
class Test(object):
    def __new__(cls, x):
        return super(Test, cls).__new__(cls)

    def __init__(self, x, y):
        self.x = x
        self.y = y


Test(1,2)
```
前面我们提到,一般情况下覆盖`__init__()`方法就能满足大部分需求,那么在什么特殊情况下需要覆盖`__new__()`方法呢?有下面几种情况:  
- 当类继承(如str int unicode tuple或者forzenset等)不可变类型切默认的`__new__()`方法不能满足需求的时候
- 用来实现工厂模式或者单例模式或者进行元编程(元类编程中常常需要使用`__new__()`来控制对象创建)
- 作为用来初始化的`__init__()`方法在多继承的情况下,子类的`__init__()`方法如果不显示的调用父类的`__init__()`方法,则父类的`__init__()`方法不会被调用.

`__new__()`方法才是类的构造方法,而`__init__()`不是

















