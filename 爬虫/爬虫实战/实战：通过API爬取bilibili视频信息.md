## 实战：通过API爬取bilibili视频信息

```python
#__init__.py

import logging

formatter = logging.Formatter(
    '%(asctime)s - %(name)s - %(levelname)s - %(message)s')
logger = logging.getLogger('monitor')
logger.setLevel(logging.INFO)

fh = logging.FileHandler('monitor.log')
fh.setLevel(logging.INFO)

fh.setFormatter(formatter)
logger.addHandler(fh)


connect = {
    "host": "localhost",
    "port": 3306,
    "user":"root",
    "passwd": "dai1993",
    "db": "bili",
    "charset": "utf8"
}

headers = {
    'X-Requested-With': 'XMLHttpRequest',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36'
                  '(KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36'
}

cols = [
    "v_aid", "v_view", "v_danmaku", "v_reply",
    "v_favorite", "v_coin", "v_share", "v_name"]

```



```python
#bili_video_info.py

#!/usr/bin/env python

# -*- coding: utf-8 -*-

import threading
import time
from concurrent import futures

import pymysql
import requests

from bili import headers, connect, logger

conn = pymysql.connect(**connect)
cur = conn.cursor()


total = 1
result = []
lock = threading.Lock()

#通过b站视频的api获得所需的除视频呢名称以外其它信息；

def run(url):
    # 启动爬虫
    global total
    req = requests.get(url, headers=headers, timeout=6).json()
    time.sleep(0.5)     # 延迟，避免太快 ip 被封
    try:
        data = req['data']
        if data['view'] != "--" and data['aid'] != 0:
            video = (
                data['aid'],        # 视频编号
                data['view'],       # 播放量
                data['danmaku'],    # 弹幕数
                data['reply'],      # 评论数
                data['favorite'],   # 收藏数
                data['coin'],       # 硬币数
                data['share'],      # 分享数
                ""                  # 视频名称（暂时为空）
            )
            with lock:
                #加锁，防止竞争写资源
                result.append(video)
                if total % 100 == 0:
                    print(total)
                    logger.info(total)
                total += 1
    except:
        pass


def create_db():
    # 创建数据库
    global cur
    cur.execute("""create table if not exists bili_video
                   (v_aid int primary key,
                    v_view int,
                    v_danmaku int,
                    v_reply int,
                    v_favorite int,
                    v_coin int,
                    v_share int,
                    v_name text
                        )""")


def save_db():
    # 将数据保存至本地
    global result, cur, conn, total
    sql = "insert into bili_video values(%s, %s, %s, %s, %s, %s, %s, %s);"
    for row in result:
        try:
            cur.execute(sql, row)
        except:
            conn.rollback()
    conn.commit()
    result = []


if __name__ == "__main__":
    create_db()
    print("启动爬虫，开始爬取数据")
    
    #迭代爬取
    for i in range(1, 2015):  #逐个爬取api
        begin = 10000 * i
        urls = ["http://api.bilibili.com/archive_stat/stat?aid={}".format(j)
                for j in range(begin, begin + 10000)]
        with futures.ThreadPoolExecutor(64) as executor:   #异步并发
            executor.map(run, urls)
        save_db()
    print("爬虫结束，共为您爬取到 {} 条数据".format(total))
    conn.close()


```



```python
#bili_video_name.py

#爬取video的name值
# -*- coding: utf-8 -*-

import threading

import pymysql
import requests
import  pyquery


from __init__ import headers,connect,cols

conn = pymysql.connect(**connect)
cur = conn.cursor()

total = 1
lock = threading.Lock()

def get_video_aid(col):
    global cur
    sql = "select v_aid from bili_video order by {} desc limit 100".format(col)
    cur.execute(sql)
    for _aid in cur.fetchall():
        print(_aid)
        yield _aid[0]

def get_video_name(aids):
    url = 'https://www.bilibili.com/video/av{}'
    for i in aids:
        try:
            req = requests.get(url.format(i),headers=headers).text
            q = pyquery.PyQuery(req)
            print({i:q("h1[title]").text()})
            yield {i:q("h1[title]").text()}
        except:
            print('pass')



def update_db_video_name(names):
    global cur,conn
    sql = "update bili_video set v_name = %s where v_aid = %s "
    for row in names:
        for v_aid,v_name in row.items():
            try:
                cur.execute(sql,(v_name,v_aid))
                print("已存入")
            except:
                conn.rollback()
                print("已回滚")
        conn.commit()



if __name__ =="__main__":
    for _col in cols[1:-1]:
        video_aids = get_video_aid(_col)
        _names = get_video_name(video_aids)
        update_db_video_name(_names)
        print(_col,"DONE!!!")
    conn.close()








```

