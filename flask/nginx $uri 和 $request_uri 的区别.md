

`$uri` 指的是请求的文件和路径，不包含”?”或者”#”之类的东西

`$request_uri` 则指的是请求的整个字符串，包含了后面请求的东西

例如： 
``$uri： www.baidu.com/document``
``$request_uri： www.baidu.com/document?x=1``

## http 和 http-socket的使用上有一些区别:

- http: 自己会产生一个http进程(可以认为与nginx同一层)负责路由http请求给worker, http进程和worker之间使用的是uwsgi协议
- http-socket: 不会产生http进程, 一般用于在前端webserver不支持uwsgi而仅支持http时使用, 他产生的worker使用的是http协议
- 因此, http 一般是作为独立部署的选项; http-socket 在前端webserver不支持uwsgi时使用,
- 如果前端webserver支持uwsgi, 则直接使用socket即可(tcp or unix)

```
提示：
当你有前端 web 服务器时不要使用 –http 选项，使用 –http-socket。
如果前端webserver支持uwsgi，请使用uwsgi协议，使用  socket。因为据说uwsgi协议的效率最高。
```

## Nginx  和  uWSGI

```
location / {
    include uwsgi_params;
    uwsgi_pass 127.0.0.1:3031;
}
```

这个意思是说 “把每个请求传递到服务器绑定的端口 3031，并且使用 uwsgi 协议通信”。现在我们可以 spawn 一个 uWSGI 进程来天然地以 uwsgi 协议通信：

```
uwsgi --socket 127.0.0.1:3031 --wsgi-file foobar.py --master --processes 4 --threads 2 --stats 127.0.0.1:9191

```

使用配置文件  foobar.ini

```
[uwsgi] 
wsgi-file foobar.py
 
master = true   
processes = 4
stats 127.0.0.1:9191
 
socket = 127.0.0.1:3031 
chmod-socket = 660   
vacuum = true 
 
die-on-term = true
```

``uwsgi --ini foobar.ini``

```
# foobar.py
import json
def application(env, start_response):
    start_response('200 OK', [('Content-Type','application/json')])
    data = {"msg":"Hello!"}
    return [json.dumps(data)]
```



