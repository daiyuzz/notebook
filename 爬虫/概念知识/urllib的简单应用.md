

## 使用urllib发送get请求

```python
import urllib.request

with urllib.request.urlopen("http://www.baidu.com") as response:
    html = response.read()
    print(html.decode()) #获得html页面内容
    print(response.code) #获得响应码 200
    print(response.headers) #获得头信息
    
```



## 使用urllib添加信息头

```python
import urllib.request

url = "http://www.douban.com"
headers = {
    "User-Agen":"Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:61.0) "
                 "Gecko/20100101 Firefox/61.0“
}

#创建一个请求对象
req = urllib.request.Request(url,headers=headers)

#发送请求对象
with urllib.request.urlopen(req) as response:
    html = response.read()
    
with open("douban.html",'wb') as f:
    f.write(html)
```



## 使用urllib发送post请求

```python
import urllib.request
import urllib.parse

url = "http://127.0.0.1:8000/login"
valuses = {
    "username":"daiyu",
    "password":"nicai1993"
}

#对数据进行编码
data = urllib.parse.urlencode(values).encode('utf-8')

#创建一个对象
req = urllib.request.Request(url,data=data,method="POST")
with urllib.request.urlopen(req) as response:
    html = response.read()

with open("login.html","wb") as f:
    f.write(html)
```



## 如何获取网站cookie信息

```python
from http import cookiejar
import urllib.request

#创建一个cookie对象
cookie = cookiejar.MozillaCookieJar("cookie.txt")

#创建一个cookie处理器
cookie_process = urllib.request.HTTPCookieProcessor(cookie)

#创建一个opener
opener = urllib.request.build_opener(cookie_process)

with opener.open("http://www.baidu.com") as response:
    print(response)
print(cookie)

#保存
cookie.save()

#加载
cooki.load("cookie.txt")

```



## 模拟登录过程

```python
import urllib.request
from http import cookiejar
import urllib.parse

url = "http://127.0.0.1:8000/login"

values = {
    "csrfmiddlewaretoken":"",
    "username":"daiyu",
    "password":"nicai1993"
}

cookie = cookiejar.CookieJar()
cookie_process = urllib.request.HTTPCookieProcessor(cookie)
opener = urllib.request.build_opener(cookie_process)
with opener.open(url) as response:
    for item in cookie:
        values['csrfmiddlewaretoken'] = item.value
        print(item.value)
        print(item.name)
        print(dir(item))
       
data = urllib.parse.urlencode(values).encode("utf-8")
req = urllib.request.Request(url,data= data)
response = opener.open(req)
with open("admin.html","wb") as f:
    f.write(response.read())
print(response.read().decode('utf-8'))
    

```



## 设置代理

```python
from urllib.error import URLError
from urllib,request import ProxyHandler,build_opener

proxy_handler = ProxyHandler({
    'http':'http://127.0.0.0:9743',
    'https':'https://127.0.0.1:9743'
})
opener = build_opener(proxy_handler)
try:
    response = opener.open('https://www.baidu.com')
    print(response.read().decode('utf-8'))
except URLError as e:
    print(e.reason)

```



## 处理异常

urllib的error模块定义了由request模块产生的异常。如果出现问题，request模块便会抛出error模块中定义的异常。

### 1.URLError

由request模块产生的异常都可以通过捕获这个类来处理

### 2.HTTPError

专门处理HTTP请求错误



## 解析链接

### 1.urlparse()

```python
from urllib.parse import urlparse
result = urlparse('http://www.baidu.com/index.html;user?id=5#comment')
print(type(result),result)
```

返回结果如下：

```python
<class ’ urllib.parse.ParseResult ’>
ParseResult(scheme=’ http ’, netloc= p 刷w. baidu. com ', path=' /index. html ’, params='user', query='id=S',
fragment='comment ' )
```

返回结果是一个ParseResult类型的对象，它包含6个部分，分别是`scheme`、`netloc`、`path`、`params`、`query`、`fragment`

可以得到一个标准的链接格式，具体如下：

```python
scheme://netloc/path;params?query#fragment
```

### 2.urlunparse()

有了 urlparse(),相应地就有了它的对立方法 `urlunparse()`.它接受的参数是一个可迭代对象，但是它的长度必须是6，否则会抛出参数数量不足或者过多的问题。

```python
from urllib.parse import urlunparse

data = ['http','www.baidu.com','index.html','user','a=6','comment']
print(urlunparse(data))
```

```python
http://www.baidu.com/index.html;user?a=6#comment
```



### 3.urlsplit()

这个方法和urlparse()方法非常相似，只不过他不再单独解析 params 这一部分，只返回5个结果

```python
from urllib.parse import urlsplit

result = urlsplit('http://www.baidu.com/index.html;user?id=5#comment')
print(result)
```

```python
SplitResult(scheme='http',netloc='www.bai.com',path='/index.html;user',query='id=5',fragment='comment')
```



### 4.urlunsplit()

与urlunparse()类似，唯一区别长度必须是5

```python
from urllib.parse import urlunsplit
data ＝［’ http ’，．州w.baidu.com ’，’ index.html ＇ ， ’ a=6 ＇，’ comment ’］
print(urlunsplit(data))
运行结果如下：
http://www.baidu.com/index.html?a=6#comment
```



### 5.urljoin()

生成链接还有另外一种方法，那就是 urljoin()方法。我们提供一个 base_url(基础链接)作为第一个参数，将新的链接作为第二个参数，该方法会分析 base_url 的scheme 、netloc 和 path 这三个内容并并对新连接缺失的部分进行补充。



### 6.urlencode()

```python
from urllib.parse import urlencode

params = {
    'name':'geremy',
    'age':22
}
base_url = 'http://www.baidu.com?'
url = base_url + urlencode(params)
print(url)
```

```python
http://www.baidu.com?name=germey&age=22
```



### 7.parse_qs()

有了序列化，必然就有反序列化。如果我们有一串 GET 请求参数，利用 parse_qs（）方法，就可以将它转回字典，示例如下：

```python
from urllib.parse import parse_qs

query = 'name=germey&age=22'
print(parse_ts(query))
```

```python
{'name':['germey'],'age':['22']}
```



### 8.parse_qsl()

另外，还有一个 parse_qsl()方法，它用于将参数转化为元祖组成的列表，示例如下：

```python
from urllib.parse import parse_qsl

query = 'name=germey&age=22'
print(parse_qsl(query))
```

```python
[('name','germey'),('age','22')]
```



### 9.quote()

该方法可以将内容转化为 URL 编码的格式。URL 中带有中文参数时，有可能会导致乱码的问题，此时用这个方法可以将中文字符转化为 URL 编码，示例如下：

```python
from urllib.parse import quote

keyword = '壁纸'
url = 'https://www.baidu.com/s?wd=' + quote(keyword)
print(url)
```

```python
https://www.baidu.com/s?wd＝航班的%81%E7%BA%B8
```



### 10.unquote()

有了 quote()方法，当然还有 unquote()方法，它可以进行 URL解码，示例如下：

```python
from urllib.parse import unquote

url = 'http s ：／／www.baidu.com/s?wd=%En 5%A3%81%E7%BA%B8'
print(unquote(url))
```

```python
https://www.baidu.com/s?wd＝壁纸
```







