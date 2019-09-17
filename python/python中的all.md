### python中的__all__

#### 1、背景

在学习CRF实体词识别的时候发现了有使用`__all__`,如下所示：

```python
__all__ = ["pre_process", "train", "recognize"]
```

#### 2、作用

`__all__`是一个字符串list，用来定义模板中对于`from xxx import *`时要对外导出的符号，即要暴露的接口，但它只对`import *`起作用，对于`from xxx import xxx`不起作用。



