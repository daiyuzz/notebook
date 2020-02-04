# 浏览器内核和控制台

谷歌浏览器的控制台
- Elements : 查看结构样式,可以修改这些内容
- Console : 查看输出结果和报错信息,是JS调试的利器
- Sources : 查看项目源代码
- Network:查看当前网站所有资源的请求信息(包括和服务器传输的HTTP报文信息),加载时间等(根据加载时间进行项目优化)
- Application : 查看当前网站的数据存储和自有源文件(可以盗图)



# JS的组成和变量

## JavaScript中的变量和数据类型

Javascript是由三部分组成

- ECMAScript(ES):描述了该语言的语法和基本对象
- DOM:文档对象模型,描述处理网页内容的方法和接口
- BOM:浏览器对象模型,描述与浏览器进行交互的方法和接口

## JS做客户端语言
按照相关的JS语法,去操作页面中的元素,有时还要操作浏览器里面的一些功能

- ECMAScript3/5/6....:   JS的语法规范(变量,数据类型,操作语句等等)
- DOM(document object model):文档对象模型,提供一些JS的属性和方法,用来操作页面的DOM元素
- BOM(browser object model):浏览器对象模型,提供一些JS的属性和方法,用来操做浏览器

# 变量和数据类型

变量:
    多种定义方式:var / let / const / function / import / class
    严谨的命名规范:区分大小写 / 驼峰命名 / 保留关键字

数据类型:
    基本数据类型(值类型):数字 number ,字符串string, 布尔boolean, null, undefined
    引用数据类型:object(数组, 对象, 正则,,,), function
    Symbol:唯一值

# JS运算符

- 比较运算符
    - !== 不等值或不等型
- 逻辑运算符
    - && 逻辑与
    - || 逻辑或
    - ! 逻辑非
- 类型运算符
    - typeof    返回变量类型
    - instanceof    返回true,如果对象是对象类型的实例
- 位运算符(处理32位数,该运算中的任何数值运算都会被转化为32位的数,结果会被转换为Javascript数)
    - &    与
    - |    或
    - ~    非
    - ^ 异或 
    - <<     零填充左位移
    - `>>`    有符号右位移
    - `>>>`    零填充右位移

# JS赋值

- 赋值运算符
    - <<=    x<<=y    x = x<<y
    - `>>==`    x<<=y    x = x<<y
    - `>>>=`    x>>>=y    x = x>>>=y

# JS函数

## 函数语法
javascript通过`function`关键字进行定义,其后是函数名和括号().
函数名可包含字母,数字,下划线和美元符号
括号可包括由逗号分隔的参数
由函数执行的代码被放置在花括号中:{}

## 函数调用
函数中的代码在其他代码调用该函数时执行:
- 当事件发生时(当用户点击按钮时)
- 当Javascript代码调用时
- 自动的(自调用)

## ()运算符调用函数
访问没有()的函数将返回函数定义:

```javascript
function toCelsius(fahrenheit){
    return (5/9)*(fahrenheit-32);
}

document.getElementById("demo").innerHTML = toCelsius;
```

# JS 对象
Javascript对象中 名称:值被称为属性

## 对象方法
对象也可以有方法
方法是在对象上执行的动作
方法以函数定义被存储在属性中

属性:fullName  属性值:function(){return this.firstName + ""+this.lastName;}


## this 关键词
在函数定义中,`this`引用该函数的"拥有者"
上面的例子中,`this`指的是"拥有" fullName函数的 person对象

## 对象定义
我们定义(创建)了一个 Javascript对象:

```
var person = {firstName:"Bill",lastName:"Gates",age:62,eyeColor:"blue"};
```

## 访问对象属性
可以以两种方式访问属性:

```
objectName.propertyName
```
或者

```
objectName["propertyName"]
```

# JS事件

## HTML 事件
HTML 事件可以是浏览器或用户做的某些事情

下面是 HTML 事件的一些例子:
- HTML网页完成加载
- HTML输入字段被修改
- HTML按钮被点击

通常,当事件发生时,用户会希望做某件事
Javascript 允许您在事件被侦测到时执行代码

通过 Javascript 代码,HTML允许您向 HTML 元素添加事件处理程序

使用单引号:
```
<element event='一些 Javascript'>
```
使用双引号:

```
<element event="一些 Javascript">
```
在下面的例子中,`onclick`属性(及代码)被添加到`<button>`元素:

```Javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <button onclick="document.getElementById('demo').innerHTML=Date()">现在时间是?</button>
<p id="demo"></p>
    
</body>
</html>


```
Javascript 代码通常有很多行.事件属性调用函数更为常见:

```Javascript

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <button onclick="displayDate()">现在时间是?</button>
<p id="demo"></p>
<script>
    function displayDate(){
        document.getElementById("demo").innerHTML=Date()
    }
</script>
    
</body>
</html>

```

## 常见的HTML事件
下面是一些常见的 HTML 事件:

|     事件     |         描述         |
| ----------- | ------------------- |
| onchange    | HTML元素已被改变      |
| onclick     | 用户点击了HTML元素    |
| onmouseover | 把鼠标移动到HTML元素上 |
| onmouseout  | 把鼠标移开HTML元素    |
| onkeydown   | 用户按下键盘按键       |
| onload      | 浏览器已经完成页面加载  |

## Javascript 能够做什么?
事件处理程序可用于处理, 验证用户输入, 用户动作和浏览器动作:
- 每当页面加载时应该做的事情
- 每当页面被关闭时应该做的事情
- 每当用户点击按钮时应该被执行的动作
- 当用户输入数据时应该被验证的内容

让 Javascript  处理事件的不同方法有很多:
- HTML 事件属性可执行 Javascript 代码
- HTML 事件属性能够调用 Javascript函数
- 您能够向HTML元素分配自己的事件处理函数
- 您能够阻止事件被发送或被处理


# JS数字方法

## Number 方法和属性
原始值(比如3.14或2020),是无法拥有属性和方法(因为它们不是对象)
但是通过 Javascript ,方法和属性也可以用于原始值,因为 Javascript 在执行方法和属性时将原始值视作对象.

## toString()方法
`toString()`以字符串返回数值
所有数字方法可用于任意类型的数字(字面量, 变量或表达式):

```Javascript
var x = 234;
x.toString();    //从变量x返回234
(234).toString(); //从文本234中返回234
(200+23).toString();    //从表达式200+34返回234
```

## toExponential) 方法
`toExponential()`返回字符串,它包含已被四舍五入并使用指数计数法的数字.
参数定义小数点后面的字符数:

```Javascript
var x = 9.656
x.toExponential(2);    //返回 9.66e+0
x.toExponential(4);    //返回 9.6560e+0
x.toExponential(6);

```

## toFixed()方法
`toFixed()`返回字符串值,它包含了指定位数小数的数字

```Javascript
var x = 9.656;
x.toFixed(0);           // 返回 10
x.toFixed(2);           // 返回 9.66
x.toFixed(4);           // 返回 9.6560
x.toFixed(6);           // 返回 9.656000
```
`toFixed(2)`非常适合处理金钱

## toPrecision() 方法

`toPrecision()`返回字符串值,它包含了指定长度的数字:
```Javascript
var x = 9.656;
x.toPrecision();        // 返回 9.656
x.toPrecision(2);       // 返回 9.7
x.toPrecision(4);       // 返回 9.656
x.toPrecision(6);       // 返回 9.65600
```

## valueOf() 方法
`valueOf()`以数值返回数值

```Javascript
var x = 123;
x.valueOf();            // 从变量 x 返回 123
(123).valueOf();        // 从文本 123 返回 123
(100 + 23).valueOf();   // 从表达式 100 + 23 返回 123
```

## 把变量转化成数值
这三种 Javascript 方法可用于将变量转化为数字:
- Number() 方法
- parseInt() 方法
- parseFloat() 方法
这些方法并非数字方法,而是全局 Javascript方法

## 全局方法
Javascript全局方法可用于所有 Javascript 数据类型

这些是在处理数字时最相关的方法:

|     方法      |          描述          |
| ------------ | --------------------- |
| Number()     | 返回数字,由其参数转换而来 |
| parseFloat() | 解析其参数并返回浮点数    |
| parseInt()   | 解析其参数并返回整数     |

## Number() 方法

`Number()`可用于把Javascript变量转换为数值

```Javascript
x = true;
Number(x);        // 返回 1
x = false;     
Number(x);        // 返回 0
x = new Date();
Number(x);        // 返回 1404568027739
x = "10"
Number(x);        // 返回 10
x = "10 20"
Number(x);        // 返回 NaN
```

## 用于日期的 Number() 方法

`Number()`还可以把日期转换为数字
`Number(new Date("2019-04-15")); //返回 1506729600000` 