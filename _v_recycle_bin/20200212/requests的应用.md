## requests的应用

### Requests库的7个主要方法

| 方法               | 说明                                          |
| ------------------ | --------------------------------------------- |
| requests.request() | 构造一个请求，支撑以下各方法的基础方法        |
| requests.get()     | 获取HTML页面的主要方法，对应于HTTP的GET       |
| requests.head()    | 获取HTML网页头信息的方法，对应于HTTP的HEAD    |
| requests.post()    | 向HTML网页提交POST请求，对应于HTTP的POST      |
| requests.put()     | 向HTML网页提交PUT请求，对应于HTTP的PUT        |
| requests.patch()   | 向HTML网页提交局部修改请求，对应于HTTP的PATCH |
| requests.delete()  | 向HTML网页提交删除请求，对应于HTTP的DELETE    |



### 1.requests发送get请求

```python
import requests

requests.get('http://www.baidu.com/s',params = {'wd':'hello'})
requests.get('http://www.baidu.com/s',params = dict(wd ='python' ))
```



### 2.requests发送post请求

```python
#发送post请求，字典会在请求时自动转化为表单

import requests
url = 'http://127.0.0.1:8000/login'
r = requests.post(url,data=dict(username='daiyu',password='nicai1993'))
print(r.content.decode()) #获得登录后的页面
```



### 3.requests下载图片

```python
import requests
url = "图片地址"
r = requests.get(url)
with open('demo.jpg','wb') as f:
    f.write(r.content)
```



### 4.requests下载大文件

```python
import requests
url = "地址"
filename = url.split('/')[-1]
r = requests.get(url,stream = True)
with open('filename','wb') as f:
    for chunk in r.iter_content(chunk_size=512):
        if chunk:
            f.write(chunk)
```



### 5.requests请求响应信息

```python
import requests
resp = requests.get('http://www.baidu.com')

#获得响应的头信息
print(resp.headers)

#获得发送请求的头信息
print(resp.requests.headers)

#获得响应的内容
print(resp.text) #字符串格式
print(resp.content)  #bytes格式
print(resp.raw)  #原始响应内容
print(resp.json()) #json格式
```



### 6.requests超时时间

```python
import requests
r = requests.get(url='http://www.baidu.com',timeout = 0.5)

```



### 7.requests发送cookie信息

```python
import requests
url = 'http://127.0.0.1:8000/login'
cookies = dict(
	test = 'testing'
)
resp = requests.get(url,cookies)
print(resp)
```



### 8.重定向

```python
import requests

#禁止重定向
r = requests.get('http://www.baidu.com/',allow_redirects = False) 
#允许重定向
r = requests.get('http://www.baidu.com/',allow_redirects = True) 
```



### 9.requests文件上传

```python
import requests

url = ""
files = {
    'file':open('somefile.txt','rb')  #file键不可变
}
r = requests.post(url,files=files)
```



### 10.requests发送json数据

```python
import json
import requests

url = ""
user = {
    'username':'gavin'
}
requests.post(url,data=json.dumps(user))
requests.post(url,json=user)
```



### 11.requests 代理

```python
import requests

proxies = {
    'http':'http://101.236.35.98:8866',
	'https':'https://180.121.132.184:808'
}

r = requests.get('http://www.baidu.com',proxies = proxies)
print(r.text)
```



### 12.requests会话

```python
import requests

#创建一个会话对象
session = requests.Session()

#地址级别的会话可以跨请求保留
session.get('http://httpbin.org/cookies/set/sessioncookie/123456789')
r = session.get('http://httpbin.org/cookies')
print(resp.text)

# 函数级别的会话不会被跨请求保留
resp = session.get('http://httpbin.org/cookies',cookies= {'name':'xiaoming'})
print(resp.text)

resp = session.get('http://httpbin.org/cookies')
print(resp.text)


# 会话可以用来提供默认数据
session = requests.Session()
session.auth = ('gavin','123456') #验证信息
session.headers.update({'x-test':'true'})

#当发get请求时会带上验证信息、update内的内容及自定义的headers
r = session.get('http://httpbin.org/headers',headers={'x-test2':'true'})
print(r.request.headers)


#在with  前后文管理器中使用session，确保session关闭
with requests.Session as session:
    session.get('http://www.baidu.com')


```



### 13.预请求

```python
from requests import Request,Session

session = Session()
url = 'http://www.baidu.com'

#构建一个请求对象
req = Request('GET',url,headers=None)

#获得带有状态的预请求对象
prepped = session.prepare_request(req)
print(type(prepped))

#发送请求
with session.send(prepped,timeout = 0.5) as response:
    print(response.status_code)

```

