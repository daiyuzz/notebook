### uwsgi 和 nginx部署flask



uwsgi配置

```python
[uwsgi]
master = true
process = 4
chdir = %v
http-socket = :5010
disable-logging = False
wsgi-file = %v/speech_recognition_api.py
callable = app
enable-threads = true
buffer-size=32768
daemonize = %v/uwsgi.log

```



nginx配置

nginx -v 

nginx version: nginx/1.14.0 (Ubuntu)

在 /etc/nginx/nginx.conf这个配置里有一句

```python
include /etc/nginx/sites-enabled/*;

```

这句的意思是，导入所有`/etc/nginx/sites-enabled`目录下面的配置文件。
我们打开里面的`sites-enabled/default`这个文件，这个文件其实是引用`sites-available/default`





