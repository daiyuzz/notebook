# docker修改存放镜像和容器的存放路径

docker info：查看docker的存储相关信息

停止docker服务
```service docker stop```

启动docker服务
```service docker start```

如果docker是1.12或以上版本，可以修改（或新建）daemon.json文件。修改后需重启docker服务生效。

vim /etc/docker/daemon.json

```python
{

"graph": "/new-path/docker"

}

```
