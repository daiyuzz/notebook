## requests和Scrapy比较

相同点：

​	两者都可以进行页面请求和爬取，Python爬虫的两个重要技术路线

​	两者可用性都好，文档丰富，入门简单

​	两者都没有处理js、提交表单、对应验证码等功能功能（可扩展）

| requests                 | Scrapy                     |
| ------------------------ | -------------------------- |
| 页面级爬虫               | 网站级爬虫                 |
| 功能库                   | 框架                       |
| 并发性考虑不足，性能较差 | 并发性好，性能较高         |
| 重点在于页面下载         | 重点在于爬虫结构           |
| 定制灵活                 | 一般制定灵活，深度制定困难 |
| 上手十分简单             | 入门稍难                   |
|                          |                            |



非常小的需求，requests库

不太小的需求，Scrapy框架

定制程度很高的需求（不考虑规模），自搭框架，requests>Scrapy

