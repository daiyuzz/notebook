# JS HTML DOM

## HTML DOM简介
通过 HTML DOM, JavaScript能够访问和改变 HTML文档的所有元素

## HTML DOM(文档对象模型)

当网页被加载时,浏览器会创建页面的文档对象模型(Document Object Model)

HTML DOM模型被结构化为 对象树:

对象的 HTML DOM 树:
![](assets/20200205124738569_372532932.png =800x)

通过这个对象模型,JavaScript 获得创建动态 HTML 的所有力量
- JavaScript 能够改变页面中的所有HTML元素
- JavaScript能够改变页面中的所有的 HTML 属性
- JavaScript 能够改变页面中的CSS样式
- JavaScript 能删除已有的HTML元素和属性
- JavaScript 能够添加新的HTML元素和属性
- JavaScript 能够对页面中所已有的HTML事件作出反应
- JavaScript 能够在页面中创建新的 HTML 事件


### 什么是DOM?
DOM是一项W3C标准

DOM定义了访问文档的标准:
`W3C文档对象模型(DOM)是中立于平台和语言的接口,它允许程序和脚本动态的访问, 更新文档的内容给你, 结构和样式`

W3C DOM 标准被分为 3 个不同的部分:
- Core DOM - 所有文档类型的标准模型
- XML DOM - 文档的标准模型
- HTML DOM -HTML 文档的标准模型


### 什么是 HTML DOM?
HTML DOM是 HTML的标准对象模型和编程接口.它定义了:
- 作为对象的HTML元素
- 所有HTML元素的属性
- 访问所有HTML元素的方法
- 所有HTML元素的事件

换言之:HTML DOM是关于如何获取, 更改, 添加或删除HTML 元素的标准


## HTML DOM方法

HTML DOM方法是您能够(在HTML元素上)执行的动作
HTML DOM方法是您能够设置或改变的HTML元素的值

### getRlementById方法
访问HTML元素最常用的方法是使用元素的id.

### innerHTML属性
获取元素内容最简单的方法是使用`innerHTML`属性
`innerHTML`属性可用于获取或替换 HTML 元素的内容
`innerHTML`属性可用于获取或改变任何HTML元素,包括`<html>`和`<body>`


## DOM文档
HTML DOM文档对象是您的网页中所有其他对象的拥有者

### HTML DOM Document对象
文档对象代表您的网页
如果您希望访问HTML页面中的任何元素,那么您总是从访问document对象开始


### 查找 HTML 元素
|                  方法                  |        描述        |
| ------------------------------------- | ----------------- |
| document.getElementById(id)           | 通过元素id来查找元素 |
| document.getElementsByTagName(name)   | 通过标签名来查找元素 |
| document.getElementsByClassName(name) | 通过类名来查找元素   |

### 改变 HTML 元素

|                方法                 |        描述         |
| ----------------------------------- | ------------------ |
| element.innerHTML=new html content  | 改变元素的 innerHTML |
| element.attribute = new value       | 改变 HTML元素的属性值 |
| element.setAttribute(attribe,value) | 改变 HTML元素的属性值 |
| element.style.property = new style  | 改变 HTML元素的样式   |


### 添加和删除元素
|               方法               |    描述     |
| ------------------------------- | ----------- |
| document.createElement(element) | 创建HTML元素 |
| document.removeChild(element)   | 删除HTML元素 |
| document.appendChild(element)   | 添加HTML元素 |
| document.replaceChild(element)  | 替换HTML元素 |
| document.write(text)            | 写入HTML元素 |



### 添加事件处理程序
|                         方法                          |            描述            |
| ---------------------------------------------------- | ------------------------- |
| document.getElementById(id).onclick=function(){code} | 向onclick事件添加事件处理程序 |


### 查找HTML对象

[查找HTML对象](https://www.w3school.com.cn/js/js_htmldom_document.asp)

## DOM 元素
如何查找和访问 HTML 页面中的HTML元素


### 查找 HTML 元素
通常,通过JavaScript,您需要操作HTML元素

为了达成此目的,您需要首先找到这些元素.有好几种完成此任务的方法:

- 通过id查找HTML元素
- 通过标签名称查找HTML元素
- 通过类名查找HTML元素
- 通过CSS选择器查找HTML元素
- 通过HTML对象集合查找HTML元素


### 通过id查找HTML元素
DOM 中查找HTML 元素最简单的方法是,使用元素的id.
例如:

```JavaScript
var myElement = document.getElementById("info")
```

### 通过标签名查找HTML元素
本例查找所有`<p>`元素:
```JavaScript
var x = document.getElementsByTagName("p");
```

### 通过类名查找HTML元素
`getElementsByClassName()`
```JavaScript
var x = document.getElementsByClassName("intro")
```
  
### 通过CSS选择器查找 HTML元素
如果你需要查找匹配指定CSS选择器(id, 类名, 类型, 属性, 属性值等等)的所有HTML严肃,请使用
`querySelectorALL()`方法.

```JavaScript
var = document.querySelectorAll("p.intro")
```
  
###  通过HTML对象选择器查找HTML对象

本例查找 id="frm1"的form元素,在forms集合中,然后显示所有元素值

```JavaScript
var x =document.forms["frm1"];
var text = "";
for(i=0;i<x.length;i++){
    text += x.elements[i].value + "<br>";
}
document.getElementById("demo").innerHTML = text;
```

## DOM - 改变HTML

HTML DOM允许JavaScript 改变HTML元素的内容

### 改变HTML输出流

JavaScript能够创建动态的HTML内容:

在JavaScript中,`document.write()`可用于直接写入HTML输出流:

```JavaScript
<!DOCTYPE html>
<html>
<body>

<script>
document.write(Date());
</script>

</body>
</html>
```

千万不要再文档加载后使用`document.write()`这么做会覆盖文档

### 改变 HTML 内容
修改 HTML 文档内容最简单的方法是,使用`innerHTML`属性.
如需修改HTML元素内容,请使用此语法:

`document.getElementById(id).innerHTML=new text`
本例修改了`<p>`中的内容:

```JavaScript
<html>
<body>

<p id="p1">Hello World!</p>

<script>
document.getElementById("p1").innerHTML = "hello kitty!";
</script>

</body>
</html>
```

### 改变属性的值
如需修改HTML属性的值,请使用如下语法:

```JavaScript
document.getElementById(id).attribute = new value
```

本例修改了`<img>`元素的`src`属性的值

```JavaScript
<!DOCTYPE html>
<html>
<body>

<img id="myImage" src="smiley.gif">

<script>
document.getElementById("myImage").src = "landscape.jpg";
</script>

</body>
</html> 

```
