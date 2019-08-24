## json.dumps与json.dump，json.loads与json.load的区别

```python
json.dumps()用于将dict类型的数据转换为str，如在数据写入json文件时，未进行转换会报错；
```

```python
json.loads()用于将str类型转换为dict
```



```python
json.dump()用于将dict类型的数据转换为str，并写入到文件中

#以下两种方式等效
import json  
  
name_emb = {'a':'1111','b':'2222','c':'3333','d':'4444'}  
          
emb_filename = ('/home/cqh/faceData/emb_json.json')  
 
# solution 1
jsObj = json.dumps(name_emb)    
with open(emb_filename, "w") as f:  
    f.write(jsObj)  
    f.close()  
    
# solution 2   
json.dump(name_emb, open(emb_filename, "w"))
```



```pyhton
json.load()用于从json文件中读取数据
#
import json  
 
emb_filename = ('/home/cqh/faceData/emb_json.json')  
 
jsObj = json.load(open(emb_filename))    
 
print(jsObj)
print(type(jsObj))
 
for key in jsObj.keys():
    print('key: %s   value: %s' % (key,jsObj.get(key)))
```

