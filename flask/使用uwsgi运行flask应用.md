



# 安装uwsgi

``pip install uwsgi``

如果安装uwsgi时出错，请查看是否安装了python-dev，如果没有安装，请先执行

``apt install build-essential python-dev``命令进行安装。

更多信息，请参考官方文档：

http://uwsgi-docs-cn.readthedocs.io/zh_CN/latest/WSGIquickstart.html

http://uwsgi-docs.readthedocs.io/en/latest/

## 直接用命令行运行

首先，需要写一个wsgi.py，在wsgi.py中实例化app

然后，进入wsgi.py所在的目录，执行如下命令即可：

uwsgi --socket 0.0.0.0:5000 --protocol=http -w wsgi:app

## 使用配置文件运行

创建如下一个配置文件,假设配置文件的名字是survey.ini

```
[uwsgi]
module = wsgi:app

master = true  
processes = 4

socket = myproject.sock  
chmod-socket = 660  
vacuum = true

die-on-term = true
```

使用uwsgi  survey.ini启动app

注意：这里使用的soket

## 在Nginx配置网关
```
server {
    listen 80;
    server_name 127.0.0.1;
    location /api {
        include uwsgi_params;
        #proxy_pass http://127.0.0.1:5000;
        uwsgi_pass unix:/home/gavin/sites/iSurvey/backend/survey.sock;
        #uwsgi_pass 127.0.0.1:3333;
    }

    location / {
        root  /home/gavin/sites/iSurvey/frontend/;
        index index.html  index.htm;
   }
}
```






