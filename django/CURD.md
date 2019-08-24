## CRUD

### 创建 Create

方法一:
task = Task(text='')
task.save()

方法二:
Task.objects.create(text="")

了解:
data = {"text":"11点下课了"}
Task.objects.create(**data)

## Retrieve

查询所有:
Task.objects.all()

条件查询:
Task.ojects.filter(id__gt=2)
__gt: 大于
__lt: 小于
__gte:大于等于
__lte:小于等于
__contains: 包含
__regex:使用正则表达式过滤

## Update
对某个对象进行修改
task = Task.objects.get(pk=1)
task.text = "新的值"
task.completed = not completed
task.save()

## Delete

task = Task.objects.get(pk=1)
task.delete()


tasks = Task.objects.filter(id_gt=20)
tasks.delete()




















