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