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