# property()函数

## 描述
property()函数的作用是在新式类中返回属性值

## 语法
`class property([fget[,fset[,fdel[,doc]]]])`

## 参数
- fget -- 获取属性值的函数
- fset -- 设置属性值的函数
- fdel -- 删除属性值函数
- doc -- 属性描述信息

## 返回值
返回新式类属性


## 实例
```python
# 定义一个可控属性值x

class C(object):
    def __init__(self):
        self._x = None
    
    def getx(self):
        return self._x
     
    def setx(self,value):
        self._x = value
    
    def delx(self):
        del self._x

    x = property(getx,setx,delx,"I'm the 'x' property.")
```
 


