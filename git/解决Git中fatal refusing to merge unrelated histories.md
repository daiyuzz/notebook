# 解决Git中fatal refusing to merge unrelated histories

## 原因
是因为两个分支没有取得关联


## 解决
在操作命令后面加上`--allow-unrelated-histories`

例如:
- `git merge master --allow-unrelated-histories`
- `git pull origin master --allow-unrelated-histories`


