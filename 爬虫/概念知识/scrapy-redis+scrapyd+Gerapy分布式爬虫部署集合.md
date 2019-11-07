# scrapy-redis+scrapyd+Gerapy分布式爬虫部署集合

## scrapy-redis分布式配置

### 1、安装scrapy-redis
```
pip install scrapy-redis
```

### 2、搭建redis服务器
设置可远程连接

### 3、配置scrapy-redis
在`settings.py`文件中修改
将调度器的类和去重的类替换为scrapy-redis提供的类
```python
SCHEDULER = 'scrapy_redis.scheduler.Scheduler'
DUPEFILTER_CLASS = 'scrapy_redis.dupefilter.RFPDupeFilter'
```

redis链接配置
```
REDIS_URL = 'redis://user:password@ip:6379'
```

其他可以使用默认配置如，调度队列，持久化、重爬，管道

### 4、配置目标存储
可选择mongo或mysql

## 部署库scrapyd的安装
### 1、安装
scrapyd是进行部署爬虫用的，一般安装在服务器上
```
pip install scrapyd
```

### 2、配置
安装完之后需要新建一个配置文件 /etc/scrapyd/scrapyd.conf, scrapyd在运行时会读取此配置文件

首先执行命令：
```python
sudo mkdir /etc/scrapyd
sudo vi /etc/scrapyd/scrapyd.conf
```
然后再文件中写入如下内容
```python
[scrapyd]
eggs_dir    = eggs
logs_dir    = logs
items_dir   =
jobs_to_keep = 5
dbs_dir     = dbs
max_proc    = 0
max_proc_per_cpu = 10
finished_to_keep = 100
poll_interval = 5.0
bind_address = 0.0.0.0
http_port   = 6800
debug       = off
runner      = scrapyd.runner
application = scrapyd.app.application
launcher    = scrapyd.launcher.Launcher
webroot     = scrapyd.website.Root

[services]
schedule.json     = scrapyd.webservice.Schedule
cancel.json       = scrapyd.webservice.Cancel
addversion.json   = scrapyd.webservice.AddVersion
listprojects.json = scrapyd.webservice.ListProjects
listversions.json = scrapyd.webservice.ListVersions
listspiders.json  = scrapyd.webservice.ListSpiders
delproject.json   = scrapyd.webservice.DeleteProject
delversion.json   = scrapyd.webservice.DeleteVersion
listjobs.json     = scrapyd.webservice.ListJobs
daemonstatus.json = scrapyd.webservice.DaemonStatus
```


### 3、后台运行scrapyd
```
scrapyd > /dev/null &
```
注意：scrapyd也可以在虚拟环境下运行，爬虫部署后会自动在虚拟环境中

在浏览器的6800端口可以访问WEB UI，如下：
![](assets/20191018112417912_1166124377.png =800x)


## Gerapy分布式管理

### 1、安装
`pip install gerapy`

### 2、使用说明
利用gerapy命令创建一个项目

`gerapy init`

在当前目录下生成了一个gerapy的文件夹，进入gerapy文件夹，会发现一个空的projects文件夹，后面会用到

初始化：
`gerapy migrate`

这样会生成一个SQLLite数据库，用于保存各个主机的配置信息等。

启动Gerapy服务
`gerapy runserver host:port(端口默认是8000)`,
host一般是本地localhost

这样，我们就可以通过`http://host:8000`进入Gerapy管理页面

### 3、在主机管理页面添加安装scrapyd环境的机器（机器IP和scrapyd运行的端口，默认是6800），如下图

![](assets/20191018113147144_1901194169.png =800x)

### 4、上面创建gerapy项目的时候，或生成一个projects文件夹，这个文件夹就是用来存放你的scrapy项目的（直接将这个项目拷贝到这个文件夹下即可）

![](assets/20191018113455147_1819071237.png =800x)

### 5、点击上面的编辑，在线编辑项目，如果项目没有问题，可以点击部署进行打包和部署，在部署之前要打包项目（打包成一个egg文件），可以部署到多台主机，如下所示：

![](assets/20191018113755121_505183393.png =800x)

### 6、部署完毕后，可以返回主机管理页面进行任务调度。点击调度即可进入任务管理页面，可以查看当前主句的所有任务的运行状态。

![](assets/20191018114026182_553413848.png =800x)

通过点击运行，停止按钮来实现任务的启动和停止，同时可以展开任务条目查看日志详情
