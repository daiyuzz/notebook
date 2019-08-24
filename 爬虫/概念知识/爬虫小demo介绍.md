## 爬虫小demo介绍

### 页面获取

```python
import requests
import codecs


def fetch(url):
    """
    获取url页面对应的内容
    如果页面成功获取返回页面的内容，否则返回None
    """
    r =requests.get(url)
    if r.status_code is 200:
        return r.content
    return None


def main():
    result = fetch('http://www.poxiao.com')
    if result is not None:
        with open ('poxiao.html','wb') as f:
            f.write(result)
    return None



if __name__ =="__main__"
	main()
```



### 页面解析

```python
from bs4 import BeautifulSoup

with open("poxiao.html",'rb') as f:
    poxiao = BeautifulSoup(f,'lxml')
 
print(poxiao.title)
print(poxiao.title.get_text())

#找到div
content_div = poxiao.find(name='div',class_='content clear')

#从div中找到所有的li
li_list = content_div.findAll(name='li')

#获得每个li中的文本和超链接
for li in li_list:
    print(li.a.get('href'))
    print(li.get_text)

```



### 使用正则表达式获得所有链接

```python
import

with open('poxiao.html','rb') as f:
    html = f.read().decode('utf8')
    
pattern = re.compile(r'href="(/[a-z0-9-/]+(.html)?)"')
urls = pattern.findall(html)
for url in urls:
    print('http://www.poxiao.com'+url[0])
```





### 保存数据

```python
import pymysql
import pymysql.cursors

from bs4 import BeautifulSoup

with open('poxiao.html','rb') as f:
    poxiao = BeautifulSoup(f,'lxml')
    
content_div = poxiao.find(name='div,class_=content clear')

li_list = content_div.findAll(name='li')

connection = pymysql.connect(host = "localhost",
                             user = "spider",
                             password = 'dai1993',
                             db = 'spider',
                             charset = 'utf8bm4",
                             cursorclass = pymysql.cursors.DictCursor		
)

try:
    for li in li_list:
        href = li.a.get('href')
        title = li.get_text()
        
        with connection.cursor() as cursor:
            sql = "INSERT INTO 'movie'('title','link') VALUES(%s,%s)"
        connection.commit()
        with connectioin,cursor() as cursor:
            sql = "SELECT 'title','link' FORM 'movie' WHERE 'movie_id'=%s"
		cursor.excute(sql,(1,))
        redult = cursor.fetchone()
        print(result)
finally:
    connection.close()
```

