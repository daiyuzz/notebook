# flask中获得请求的真实ip
网站有时候需要获取用户的ip，以防爬虫或者其他用途，Flask获取用户ip的方法是：
```python
from flask import request
ip = request.remote_addr
```
这种方法在调试的时候可以用，但是通过Nginx反向代理之后，这种方法获取的ip就会全部变为`127.0.0.1`,因为实际中，这种方式不可取。


因为nginx配置文件中有如下配置

```python
proxy_set_header Host $host:80; 
proxy_set_header X-Real-IP $remote_addr; 
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 

```
nginx会向后端发送一个headers，里面就有所需要的真实ip

因此通过：
```python
from flask import request
ip = request.headers['X-Forwarded-For']
```
就可以获取真实ip
