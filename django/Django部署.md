# Django部署

## Django+ uwsgi + nginx

### 一、项目设置
在项目的`settings.py`文件中，将部分配置设置如下
```python
DEBUGE = False
ALLOWED_HOSTS = ['*']
```
正式环境部署时需要关闭调试，将allowed_hosts设置为*,表示接受所有ip访问，或者填入你允许访问的ip。

### 二、uwsgi设置
在项目文件根目录下新建`uwsgi.ini`文件，配置如下：

```python
[uwsgi]
chdir = %v
socket = 127.0.0.1:8000
master = true
daemonize = %v/run.log
disable-logging = true
module = article_recommend.wsgi:application
buffer-size=32768
```
在uwsgi中`%v`表示当前项目所在目录，如果只用uwsgi进行配置的话，使用`http = 127.0.0.1:8000`,这里需要配合nginx，所以使用socket
module：要使用的WSGI模块，在你创建的项目包下有一个wsgi.py的文件，该模块包含一个WSGI application对象。


### 三、nginx配置
在 `/etc/nginx/conf.d`下新建一个xxx.conf配置文件，配置如下：
```python
   server{
       listen 80;
       server_name 192.168.31.226;
   
       location / {
       include uwsgi_params;
       uwsgi_pass 127.0.0.1:8000;
       uwsgi_read_timeout 120s;
       uwsgi_send_timeout 120s;
   
       }
  
      location /static {
          alias /home/daiyu/桌面/article_recommend/static;
      }
  }
```

server_name:域名，没有的话暂时填外网ip
location中指向uwsgi文件，还可以加加上超时时间
localtion /static 指定静态文件位置
 
使用 `/usr/sbin/nginx -t`测试写的nginx配置是否有问题
使用 `/usr/sbin/nginx -s reload`重启nginx