# 记一次Fastdfs的安装


## 什么是FastDFS
FastDFS是一个开源的轻量级分布式文件系统。它解决了大数据量存储和负载均衡等问题。特别适合以中小文件（建议范围：4KB < file_size <500MB）为载体的在线服务，如相册网站、视频网站等等。在UC基于FastDFS开发向用户提供了：网盘，社区，广告和应用下载等业务的存储服务。

FastDFS是一款开源的轻量级分布式文件系统纯C实现，支持Linux、FreeBSD等UNIX系统类google FS，不是通用的文件系统，只能通过专有API访问，目前提供了C、Java和PHP API为互联网应用量身定做，解决大容量文件存储问题，追求高性能和高扩展性FastDFS可以看做是基于文件的key value pair存储系统，称作分布式文件存储服务更为合适。


FastDFS特性：

- 文件不分块存储，上传的文件和OS文件系统中的文件一一对应
- 支持相同内容的文件只保存一份，节约磁盘空间
- 下载文件支持HTTP协议，可以使用内置Web Server，也可以和其他Web Server配合使用
- 支持在线扩容
- 支持主从文件
- 存储服务器上可以保存文件属性（meta-data）V2.0网络通信采用libevent，支持大并发访问，整体性能更好


## FastDFS的相关概念
FastDFS服务端有三个角色：跟踪服务器(tracker server)、存储服务器(storage server)和客户端(client)

tracker server：跟踪服务器，主要做调度工作，起负载均衡的作用。在内存中记录集群中所有存储组和存储服务器的状态信息，是客户端和数据服务器交互的枢纽。相比GFS中的master更为精简，不记录文件索引信息，占用的内存量很少。

Tracker是FastDFS的协调者，负责管理所有的storage server和group，每个storage在启动后会连接Tracker，告知自己所属的group等信息，并保持周期性的心跳，tracker根据storage的心跳信息，建立group==>[storage server list]的映射表。

Tracker需要管理的元信息很少，会全部存储在内存中；另外tracker上的元信息都是由storage汇报的信息生成的，本身不需要持久化任何数据，这样使得tracker非常容易扩展，直接增加tracker机器即可扩展为tracker cluster来服务，cluster里每个tracker之间是完全对等的，所有的tracker都接受stroage的心跳信息，生成元数据信息来提供读写服务。

storage server：存储服务器（又称：存储节点或数据服务器），文件和文件属性（meta data）都保存到存储服务器上。Storage server直接利用OS的文件系统调用管理文件。

Storage server（后简称storage）以组（卷，group或volume）为单位组织，一个group内包含多台storage机器，数据互为备份，存储空间以group内容量最小的storage为准，所以建议group内的多个storage尽量配置相同，以免造成存储空间的浪费。

以group为单位组织存储能方便的进行应用隔离、负载均衡、副本数定制（group内storage server数量即为该group的副本数），比如将不同应用数据存到不同的group就能隔离应用数据，同时还可根据应用的访问特性来将应用分配到不同的group来做负载均衡；缺点是
group的容量受单机存储容量的限制，同时当group内有机器坏掉时，数据恢复只能依赖group内地其他机器，使得恢复时间会很长。


client：客户端，作为业务请求的发起方，通过专有接口，使用TCP/IP协议与跟踪器服务器或存储节点进行数据交互。FastDFS向使用者提供基本文件访问接口，比如upload、download、append、delete等，以客户端库的方式提供给用户使用。

另外两个概念：

group ：组， 也可称为卷。 同组内服务器上的文件是完全相同的 ，同一组内的storage server之间是对等的， 文件上传、 删除等操作可以在任意一台storage server上进行 。

meta data ：文件相关属性，键值对（ Key Value Pair） 方式，如：width=1024,heigth=768 。

![](assets/20191021170852355_2089058002.png =800x)

Tracker相当于FastDFS的大脑，不论是上传还是下载都是通过tracker来分配资源；客户端一般可以使用ngnix等静态服务器来调用或者做一部分的缓存；存储服务器内部分为卷（或者叫做组），卷于卷之间是平行的关系，可以根据资源的使用情况随时增加，卷内服务器文件相互同步备份，以达到容灾的目的。

## 上传机制
首先客户端请求Tracker服务获取到存储服务器的ip地址和端口，然后客户端根据返回的IP地址和端口号请求上传文件，存储服务器接收到请求后生产文件，并且将文件内容写入磁盘并返回给客户端file_id、路径信息、文件名等信息，客户端保存相关信息上传完毕。

![](assets/20191021171233659_1677687176.png =800x)

### 内部机制如下：
#### 1、选择tracker server
当集群中不止一个tracker server时，由于tracker之间是完全对等的关系，客户端在upload文件时可以任意选择一个trakcer。 选择存储的group 当tracker接收到upload file的请求时，会为该文件分配一个可以存储该文件的group，支持如下选择group的规则：
- 1、Round robin，所有的group间轮询
- 2、Specified group，指定某一个确定的group

。。。。。。。。。。。。。。。。。。。。。。

[博客链接](http://www.ityouknow.com/fastdfs/2018/01/06/distributed-file-system-fastdfs.html)


## 安装
### 1、下载
下载`libfastcommon`和`fastdfs`

### 2、安装
- 进入`libfastcommon`文件，执行`./make.sh`,然后执行`sudo ./make.sh install`
`libfastcommon.so`安装到了`usr/lib64`目录里，但是FastDFS主程序设置的lib目录为`/usr/lcoal/bin`，所以需要创建软连接
```python
ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so
ln -s /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so
ln -s /usr/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so
ln -s /usr/lib64/libfdfsclient.so /usr/lib/libfdfsclient.so
```
- 进入`fastdfs`文件，执行`./make.sh`，然后执行`sudo ./make.sh install`

### 3、配置
#### 1、配置跟踪服务器 tracker
1、`sudo cp /etc/fdfs/tracker.conf.sample /etc/fdfs/tracker.conf`
2、在`/home`目录下创建目录`fdfs/tracker`
3、编辑`/etc/fdfs/tracker.conf`配置文件，修改`base_path=/home/fdfs/tracker`

#### 2、配置存储服务器 storage
1、`sudo cp /etc/fdfs/storage.conf.sample /etc/fdfs.conf`
2、在`/home/fdfs`目录下新建`storage`
3、编辑`/etc/fdfs/storage.conf`配置文件，修改内容为`base_path=/home/fdfs/storage`
`store_path0=/home/fdfs/storage`
`store_server=自己ip地址:22122`


### 启动
`/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf start`
`/usr/bin/fdfs_storage /etc/fdfs/storage.conf start`

注：如果启动storage时出现`ERROR - file: shared_func.c, line: 1217, open file /home/dfs/storage/data/fdfs_storaged.pid fail, errno: 2, error info: No such file or directory`错误信息，
需要在`/home/fdfs/storage`目录下新建一个`data`文件夹

### 测试是否安装成功
1、`sudo cp /etc/fdfs/client.conf.sample /etc/fdfs/client.conf`
2、编辑`/etc/fdfs/client.conf`,修改内容如下：
```python
base_path=/home/fdfs/tracker
tracker_server = 自己服务器的ip地址:22122
```
3、上传文件测试
`/usr/bin/fdfs/fdfs_upload_file /etc/fdfs/client.conf` 要上传的图片文件

如果返回类似 `group1/M00/00/00/wKgf4l2tbA-AABUjAAnXQ2hTVPw001.jpg`的文件id说明图片已经上传成功

