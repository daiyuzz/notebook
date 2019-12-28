# 将django数据库设置为mysql

## 安装pymysql数据库驱动
`pip install pymysql`


## 配置Django中的DATABASE
在`settings.py`中找到DATABASE

```python
DATABASES = {
    'default':{
    'ENGINE':'django.db.backends.mysql', #数据库引擎
    'NAME':'my_blog',
    'USER':'root',
    'PASSWORD':'nicai1993',
    'HOST':'localhost',
    'PORT':'3306',
    }
}

```
接着在项目下的`__init__.py`添加如下代码
```python
import pymysql
pymysql.install_as_MySQLdb()

```
然后在settings.py文件中设置`TIME_ZONE`为自己的时区
`TIME_ZONE = 'Asia/Shanghai'`

最后执行数据库迁移命令

```python
python manage.py makemigrations
python manage.py migrate

```