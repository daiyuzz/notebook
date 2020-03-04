# JS表单

## Javascript 表单验证
HTML 表单验证能够通过 Javascript 来完成
如果某个表单字段(fname)是空的,那么该函数会发出一条警告信息,并且返回false,以防止表单被提交出去:

```Javascript
function validateForm(){
    var x = document.forms['myForm']['fname'].value;
    if(x == ""){
    alert("必须填写姓名");
    return false;
    }
}
```
该函数能够在表单提交时被调用:

```html
<form name="myForm" action="/action_page_post.php" onsubmit="return validateForm()" method="post">
姓名：<input type="text" name="fname">
<input type="submit" value="Submit">
</form>
```

## JavaScript 能够验证数字输入
Javascript 常用于验证数字输入

## 自动 HTML 表单验证
HTML 表单验证能够被浏览器自动执行:
如果表单字段(fname)是空的,required 属性防止表单被提交

```Javascript
<form action="/action_page_post.php" method="post">
   <input type="text" name="fname" required>
   <input type="submit" value="Submit">
</form>
```

## 数据验证
数据验证指的是确保干净, 正确和有用的用户输入的过程

典型的验证任务是:
- 用户是否已经填写所有必须的字段?
- 用户是否已输入有效的日期?
- 用户是否在数字字段中输入文本?

服务器端验证是由web 服务器执行的,在输入被送往服务器之后
客户端验证是由web浏览器执行的,在输入被送往 web 服务器之前

## HTML 约束验证
HTML 约束验证基于:
- 约束验证 HTML 输入属性
- 约束验证 CSS 伪选择器
- 约束验证 DOM 属性和方法

## 约束验证 HTML 输入属性

|   属性    |          描述           |
| -------- | ---------------------- |
| disabled | 规定 input 元素应该被禁止 |
| max      | 规定 input 元素的最大值   |
| min      | 规定 input 元素的最小值   |
| required | 规定输入字段需要某个元素   |
| type     | 规定 input 元素的类型    |
| pattern  | 规定 input 元素的值模式   |

## 约束验证 CSS 伪选择器

|   选择器   |                描述                 |
| --------- | ----------------------------------- |
| :disabled | 选择设置了 "disabled"属性的 input 元素 |
| :invalid  | 选择了带有无效值的 input 元素          |
| :optional | 选择未设置"required"属性的 input 元素  |
| :required | 选择设置了"required"属性的 input 元素  |
| :valid    | 选择带有有效值的 input 元素            |



# JS验证 API

## 约束验证 DOM方法
|         属性         |                 描述                  |
| ------------------- | ------------------------------------- |
| checkValidity()     | 返回 true,如果 input 元素包含有效数据     |
| setCustomValidity() | 设置 input 元素的validationMessage 属性 |


## 约束验证 DOM 属性

|        属性        |                 描述                  |
| ----------------- | ------------------------------------- |
| validity          | 包含与input元素的合法性相关的布尔属性      |
| validationMessage | 包含当 validity 为false时浏览器显示的消息 |
| willValidate      | 指示是否验证 input 元素                 |

## 合法性属性
input 元素的 `validity`属性包含了与数据合法性相关的一系列属性:

|       属性       |                               描述                                |
| --------------- | ----------------------------------------------------------------- |
| customError     | 设置为true,如果设置自定义的合法性消息                                  |
| patternMismatch | 设置为true,如果元素值不匹配其 pattern属性                             |
| rangeOverflow   | 设置为true,如果元素值大于其max属性                                    |
| rangeMismatch   | 设置为true,如果元素值小于其min属性                                    |
| stepMismatch    | 当字段拥有step属性,且输入的value值不符合设定的时间间隔,该属性值为true      |
| tooLong         | 设置为 true，如果元素值超过了其 maxLength 属性。                       |
| typeMismatch    | 当字段的 type 是 email 或者 url 但输入的值不是正确的类型时，属性值为 true |
| valueMissing    | 设置为 true，如果元素（包含 required）没有值                           |
| valid           | 设置为 true，如果元素值是有效的。                                     |



实例:

```JavaScript
<input id="id1" type="number" max="10">
<button onclick="myFunction()">OK</button>
<p id="demo"></p>

<script>
    function myFunction(){
        var txt = "";
        if(document.getElementById("id1").validity.rangeOverflow){
            txt = "值太大";
        }
        document.getElementById("demo").innerHTML = txt;
    }
</script>
```