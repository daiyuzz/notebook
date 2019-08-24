## 实战：爬取mzitu图片

#### meizitu.py

```python
import re
import os
import time
import threading
from multiprocessing import Pool,cpu_count

import requests
from bs4 import BeautifulSoup

HEADERS = {
    'X-Requested-With': 'XMLHttpRequest',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 '
                  '(KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36',
    'Referer': 'http://www.mzitu.com'
}

#下载图片保存路径
DIR_PATH = r"/home/dyx/Downloads/meizitu"


def get_urls():
    """
    获取mzitu网站下所有套图的 url
    :return:
    """
    page_urls = ['http://www.mzitu.com/page/{cnt}'.format(cnt=cnt) for cnt in range(1,187)]
    print('please wait for second .....')
    img_urls = []
    for page_url in page_urls:
        try:
            bs = BeautifulSoup(
                requests.get(page_url,headers = HEADERS,timeout=10).text,
                'lxml').find('ul',id = 'pins')
            result = re.findall(r"(?<=href=)\S+",str(bs))  #findall 匹配所有url 返回str（bs）中所有匹配正则的结果，返回形式为数组
            img_url = [url.replace('"',"") for url in result]
            img_urls.extend(img_url)
        except Exception as e:
            print(e)
    return set(img_urls)   #利用set去重urls


lock = threading.Lock()   #全局资源锁


def urls_crawler(url):
    """
    爬虫入口，主要爬取操作
    """
    try:
        r = requests.get(url,headers=HEADERS,timeout=10).text
        folder_name = BeautifulSoup(r,'lxml').find(
            'div',class_ = 'main-image').find('img')['alt'].replace("?"," ")
        with lock:
            if make_dir(folder_name):
                #套图里图片的数量
                max_count = BeautifulSoup(r,'lxml').find(
                    'div',class_='pagenavi').find_all('span')[-2].get_text()
                page_urls = [url+"/"+str(i) for i in range(1,int(max_count)+1)]
                img_urls = []  #进入套图后套图内urls

                for _,page_url in enumerate(page_urls):
                    time.sleep(0.01)
                    result = requests.get(page_url,headers=HEADERS,timeout=10).text
                    img_url=BeautifulSoup(result,'lxml').find(
                        'div',class_='main-image').find('p').find('a').find('img')['src']
                    img_urls.append(img_url)
                for cnt,url in enumerate(img_urls):
                    save_pic(url,cnt)
    except Exception as e:
        print(e)



def save_pic(pic_src,pic_cnt):
    """
    保存图片到本地

    """
    try:
        time.sleep(0.01)
        img = requests.get(pic_src,headers=HEADERS,timeout=10)
        img_name = "pic_cnt_{}.jpg".format(pic_cnt+1)
        with open (img_name,'ab') as f:
            f.write(img.content)
            print(img_name)
    except Exception as e:
        print(e)


def make_dir(folder_name):
    """
    创建文件夹并切换到该目录下
    """
    path = os.path.join(DIR_PATH,folder_name)
    #如果目录已经存在就不用再次爬取了，去重，提高效率。存在返回 False，否则返回True
    if not os.path.exists(path):
        os.makedirs(path)
        print(path)
        os.chdir(path)  #os.chdir() 方法用于改变当前工作目录到指定的路径
        return True
    print("Folders has existed!")
    return False


def delete_empty_dir(save_dir):
    """
    如果程序半路中断的话，可能存在已经新建好文件夹但是仍没有下载图片的情况
    但此时文件夹已经存在所以会忽略该套图的下载，此时要删除空文件夹
    """
    if os.path.exists(save_dir):
        if os.path.isdir(save_dir):
            for d in os.listdir(save_dir):
                path = os.path.join(save_dir,d) #组装下一级地址
                if os.path.isdir(path):
                    delete_empty_dir(path)  #递归删除空文件夹
        if not os.listdir(save_dir):
            os.rmdir(save_dir)
            print("remove the empty dir :{}".format(save_dir))
    else:
        print("Please start your performance !")


if __name__ == "__main__":
    urls = get_urls() #套图的起始url
    pool = Pool(processes=cpu_count())
    try:
        delete_empty_dir(DIR_PATH)
        pool.map(urls_crawler,urls)
    except Exception:
        time.sleep(30)
        delete_empty_dir(DIR_PATH)
        pool.map(urls_crawler,urls)

```





### 提取数据进行分析

#### analysis.py

```python
import os
import json
import jieba
import matplotlib.pyplot as plt
from wordcloud import WordCloud


#将所有文件夹名转化为 str类型
folder_name = " ".join(os.listdir(r"/home/dyx/Downloads/meizitu"))



#jieba分词
jieba.load_userdict(r"/home/dyx/Documents/items/spider/meizitu/data/jieba.txt") #添加自定义字典，（）内为字典路径
seg_list = jieba.lcut(folder_name,cut_all = False)

#利用字典统计词频
counter = dict()
for seg in seg_list:
    counter[seg] = counter.get(seg,1) + 1
print(counter)

#根据词频排序字典
counter_sort = sorted(
        counter.items(), key = lambda value:value[1],reverse=True
    )
print(counter_sort)

#解析成 json类型并写入文件
words = json.dumps(counter_sort,ensure_ascii=False)
with open(r"/home/dyx/Documents/items/spider/meizitu/data/words.json",'w+',encoding='utf-8') as f:
    f.write(words)


#生成词云
wordcloud = WordCloud(
    font_path=r"/home/dyx/Documents/items/spider/meizitu/font/msyh.ttf",
    max_words=600,height=200,
).generate_from_frequencies(counter)
plt.imshow(wordcloud)
plt.axis("off")
plt.show()
wordcloud.to_file("wordcloud.jpg")

```



