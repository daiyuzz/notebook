## Feed exports

```python
实现爬虫最经常提到的需求就是能合适的保存爬取到的数据，或者说，生成一个带有爬虫数据的“输出文件”（通常叫“输出feed”），来供其他系统使用；

scrapy自带了Feed输出，并且支持多种序列化格式及存储方式

```



#### 序列化方式（serialization formats）

feed输出使用到了 item exports， 其自带支持的类型有：

JSON

JSON lines

CSV

XML

可以通过 FEED_EXPORTERS设置扩展支持



JSON

```python
FEED_FORMAT:json
使用的exporter：JsonItemExporter

```



