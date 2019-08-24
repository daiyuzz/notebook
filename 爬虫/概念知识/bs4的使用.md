## bs4基本使用

```python
from bs4 import BeautifulSoup

html = """
<html>
    <head>
        <title> bs4 demo </title>
    </head>
    <body>
        <div class='red' id='id_demo'> <p> <span>Hello</span>  world </p> 
        <a> 新闻 </a>

        </div>
        
        <div class="blue" > bbbbbb </div>
    
    </body>
</html>
"""

soup = BeautifulSoup(html)

#1.按tag查找
soup.get_text()
soup.title.get_text()

#2.find
soup.find(name='a')  #查找a标签
soup.find(name='body').find(name='a') #查找a标签

#3.获取元素文本
soup.div.p.get_text()
soup.div.p.sqan.string

#4.获取属性
soup.div.attrs
soup.div.get('id')

#5.获取属性
soup.find_all(name = 'div',attrs {},recursive=True,text='',class_='',limit=2)

#-name:tag name
#-attrs : tag属性
#-recursive :是否递归,如果设置为True，查找相应标签参数的所有子标签，设置为False，就只查找文档一级标签，默认为True
#-text:按tag显示的内容查找
#-limit ：限制找到结果的个数

#find中limit限制为1
soup.find(name = 'div',attrs = {},recursive=True,text='',class_='')








```



#### Beautiful对象

1.BeautifulSoup对象

2.标签Tag对象

3.NavigableString对象

​	用来表示标签里的文字，不是标签

4.Comment对象

​	用来查找HTML文档的注释标签

