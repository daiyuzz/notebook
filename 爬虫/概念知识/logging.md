## logging

```python
scrapy提供了log功能，可以通过scrapy.log模块使用

log服务必须通过显示调用scrapy.log.start()开启

```



### log levels

```python
Scrapy提供5层logging级别：
1.CRITICAL -严重错误（critical）
2.ERROR - 一般错误（regular error）
3.WARNING - 警告信息（warning messages）
4.INFO - 一般信息（informational messages）
5.DEBUG - 调试信息（debugging messages）
```



### 如何记录信息（log messages）

```python
from scrapy import log
log.msg("This is a warning",level = log.WARNING)
```



