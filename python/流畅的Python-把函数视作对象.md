# 流畅的Python-把函数视作对象


## 一等函数
在Python中，函数是一等对象。编程语言理论家把“一等对象”定义为满足以下条件的程序实体：
- 在运行时创建
- 能赋值给变量或数据结构中的元素
- 能作为参数传递给函数
- 能作为函数的返回结果



### 可调用对象
除了用户自定义的函数，调用运算符（即（））还可以应用到其他对象上。如果需要判断对象能否调用，可以使用内置的 callable()函数。
python中有各种各样可调用的类型，因此判断对象能否调用，最安全的方法是使用内置的 callable()函数：

```python
abs,str,13

[callable(obj) for obj in (abs,str,13)]
# [True,True,False]
```


### 用户定义的可调用类型
不仅 Python 函数是真正的对象，任何Python对象都可以表现的像函数。因此，只需要实现实例方法 `__call__`


```python
import random


class BingoCage:
    def __init__(self, items):
        self._items = list(items)
        random.shuffle(self._items)

    def pick(self):
        try:
            return self._items.pop()
        except IndexError:
            raise LookupError('pick from empty BingoCage')

    def __call__(self, *args, **kwargs):
        return self.pick()


bingo = BingoCage(range(3))
bingo.pick()
# 1
bingo()
# 0
callable(bingo)
# true

```
- `__init__`接受任何可迭代对象；在本地构建一个副本，防止列表参数的意外副作用
- `bingo.pick()` 的快捷方式是 `bingo()`
- `bingo`实例可以作为函数调用，而且内置的 callable(.....)函数判定它是可调用的对象。

实现`__call__`方法的类是创建函数类对象的简便方式，此时必须在内部维护一个状态，让它在调用之间可用。例如 BingoCage中剩余元素。装饰器就是这样
装饰器必须是函数，而且有时还要在多次调用之间 记住 某些事

创建保有内部状态的函数，还有一种截然不同的方式--使用闭包。


下面讨论把函数视作对象处理的另一方面：运行时内省


### 函数内省
用户定义的函数的属性
| 名称       | 类型    |说明     |
| --- | --- | --- |
|  `__annotations__`   |   dict  |    参数和返回值的注解 |
|   `__call__`  |   method-wrapper  | 实现（）运算符；即可调用对象协议    |
|   `__closure__`  |  tuple   |  函数闭包，即自由变量的绑定（通常是None）   |
|   `__code__`  |  code   |   编译成字节码的函数元数据和函数定义体  |
|   `__defaults__`  |   tuple  |   形式参数的默认值  |
|    `__get__` |  methood-wrapper   |  实现只读描述符协议   |
|  `__globals__`   |  dict   |   函数所在模块中的全局变量  |
|   `__kwdefaults__`  |  dict   |   仅限关键字形式参数的默认值  |
|   `__name__`  |  str   |   函数名  |
|    `__qualname__` |  str   |   函数的限定名称，如random.choice  |




下面我们讨论Python为声明函数形参和传入实参所提供的强大句法

### 从定位参数到仅限关键字参数

python 最好的特性之一就是提供了极为灵活的参数处理机制，而且 python3 进一步提供了仅限关键字参数。与之亲密相关的是，调用函数时使用 * 和 ** ‘展开’ 可迭代对象，映射到单个参数

```python
def tag(name, *content, cls=None, **attrs):
    if cls is not None:
        attrs['class'] = cls
    if attrs:
        attr_str = ''.join('%s="%s"' % (attr, value)
                           for attr, value in sorted(attrs.items()))

    else:
        attr_str = ''
    if content:
        return '\n'.join('<%s %s>%s</%s>' % (name, attr_str, c, name) for c in content)
    else:
        return '<%s%s/>' % (name, attr_str)


1、tag('br')
# '<br />'
2、tag('p','hello')
# '<p>hello</p>'\
3、tag('p','hello','world')
# <p>hello</p>
#<p>world</p>
4、tag('p', 'hello', id=33)
#'<p id="33">hello</p>'
5、tag('p','hello','world',cls='sidebar')
# '<p class="sidebar">hello</p>'
# '<p class="sidebar">world</p>'
6、tag(content='testing', name='img')
# '<img content="testing"/>'
my_tag = {'name': 'img', 'title': 'Sunset Boulevard', 'src': 'sunset.jpg', 'cls': 'framed'}
7、tag(**my_tag)
'<img class="framed" src="sunset.jpg" title="Sunset Boulevard"/>'
```

- 1、传入单个定位参数，生成一个指定名称的空标签
- 2、第一个参数后面的任意个参数会被 *content 捕获，存入一个元组。
- 4、tag 函数签名中没有明确指定名称的关键字会被 `**attrs`捕获，存入一个字典
- 5、cls参数只能作为关键字参数传入
- 6、调用tag函数时，即使第一个定位参数也能作为关键字参数传入
- 7、在 my_tag 前面加上 ** ,字典中的所有元素作为单个参数传入，同名键会绑定到对应的具名函数上，余下的则被 `**attrs`捕获。


定义函数时若想指定仅限关键字参数，要把它们放到前面有 * 的参数后面。如果不想支持数量不定的定位参数，但是想支持仅限关键字参数，在签名中放一个 * ，如下所示：

```python
def f(a,*,b):
    return a,b

f(1,b=2)
# (1,2)

```


## 使用一等函数实现设计模式


### 案例分析：重构“策略”模式




## 函数的装饰器和闭包

### 装饰器基础知识
装饰器是可调用对象，其参数是另一个函数（被装饰的函数）。装饰器可能会处理被装饰的函数，然后把它返回，或者将其替换成另一个函数或可调用对象。


装饰器的一大特性是能把被装饰的函数替换成其他函数。第二个特性是，装饰器在加载模块时立即执行

### Python何时执行装饰器
装饰器的一个关键特性是，他们在被装饰的函数定义之后立即运行。