# redis常见问题整理

##  MISCONF Redis is configured to save RDB snapshots, but it is currently not able to persist on disk.

Redis被配置为保存数据快照，但它目前不能持久化到硬盘。用来修改集合数据的命令不能用。

原因：强制关闭Redis快照导致不能持久化

解决方案：
将 stop-writes-on-bgsave-error设置为no
```
127.0.0.1:6379> config set stop-writes-on-bgsave-error no
```