# scrapy中使用cookie的三种方式

## settings
settings文件中给`Coookies_enable=False`解注释
settings的headers配置cookie
这种方法最简单,同时cookie可以直接粘贴浏览器的



## DownloadMiddleware
settings中给downloadmiddleware解注释
在`pipeline.py`文件中修改`downloadmiddleware`这个类,修改`process_request`,添加`request.cookies={}`即可.
cookie需要是字典格式



## 重写`start_request`方法
```python
def satrt_request(self):
    yied scrapy.Request(url,dont_filter=True,cookies={自己的cookie})
```
这里cookie需要字典格式
