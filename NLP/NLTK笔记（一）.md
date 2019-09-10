nltk全称是natural  language toolkit，是一套基于python的自然语言处理工具。

###  一、NLTK的安装

```python
pip install nltk
```

在nltk中集成了语料与模型等的包管理器，通过在python解释器中执行

```python
import nltk
nltk.download()
```

便会弹出一个包管理界面，在管理器中可以下载预料，预训练的模型等。

选择all，下载全部。

linux下可会是个无界面，输入d，再输入all，下载全部。

### 二、对文本进进行简单分析

#### 1、Text类介绍

nltk.text.Text()类用于对文本进行初级的统计与分析，它接受一个词的列表作为参数。Text类提供了下列方法。

| 方法                                   | 作用                          |
| -------------------------------------- | ----------------------------- |
| Text(words)                            | 对象构造                      |
| concordance（word，width=79,lines=25） | 显示word出现的上下文          |
| common_contexts(words)                 | 显示出现words出现的相同模式   |
| similar（word）                        | 显示word相似的词              |
| collocations（num=20,window_size=2）   | 显示最常见的二词搭配          |
| count（word）                          | word出现的次数                |
| dispersion_plot（words）               | 绘制words中文档中出现的位置图 |
| vocab()                                | 返回文章去重的词典            |

nltk.text.TextCollection类是Text的集合，提供下列方法

| 方法                                     | 作用                               |
| ---------------------------------------- | ---------------------------------- |
| nltk.text.TextCollection([text1,text2,]) | 对象构造                           |
| idf(term)                                | 计算辞term在语料库中的逆文档频率   |
| tf(term,text)                            | 统计tern在text中的词频             |
| tf_idf（term,text）                      | 计算term在句子中的tf_idf，即tf*idf |



### 三、对文档用词进行分布统计

#### 1、FreqDist类介绍

这个类主要记录了每个词出现的次数，根据统计数据生成表格或绘图。其结构很简单，有一个有序词典进行实现。多以dict类型的方法在此类也是适用的。如keys()等。

| 方法                         | 作用                                             |
| ---------------------------- | ------------------------------------------------ |
| B()                          | 返回字典的长度                                   |
| plot(title,cumulative=False) | 绘制频率分布图，若cumu为True，则是累计频率分布图 |
| tabulate()                   | 生成频率分布的表格形式                           |
| most_common（）              | 返回出现次数最频繁的词与频度                     |
| hapaxes()                    | 返回只出现一次的词                               |



### 四、nltk自带的语料库

在nltk.corpus包下，提供了好几类标注好的语料库。见下表：

| 语料库    | 说明                                     |
| --------- | ---------------------------------------- |
| gutenberg | 一个有若千万部的小说语料库，多是古典作品 |
| webtext   | 收集的网络广告等内容                     |
| nps_chat  | 有上万条聊天消息语料库，及时聊天消息为主 |
| brown     | 一个百万级的英语语料库，按文体进行分类   |
| reuters   | 路透社语料库，上万篇新闻文文档           |
| inaugural | 演讲语料库，几十个文本，都是总统演说     |

#### 1、语料库处理

| 方法                       | 说明                       |
| -------------------------- | -------------------------- |
| fileids                    | 返回语料库中文件名列表     |
| fileids[categories]        | 返回指定类别的文件名列表   |
| raw(fid=[c1,c2])           | 返回指定文件名的文本字符串 |
| raw(catergories=[])        | 返回指定分类的原始文本     |
| sents(fid=[c1,c2])         | 返回指定文件名的语句列表   |
| sents(catergories=[c1,c2]) | 按分类返回语句列表         |
| words(filename)            | 返回指定文件名的单词列表   |
| words(categories=[])       | 返回指定分类的单词列表     |



### 五、文本与处理

NLP在获取语料之后，通常需要进行文本处理。英文的预处理包括：分词、去停词、提取词干等步骤。中文分词相对英文更复杂一些，也需要去停词但是没有提取词干的需要。

