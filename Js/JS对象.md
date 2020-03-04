# JS对象

## JS对象定义

在JavaScript中,对象是王.如果理解了对象,就理解了 JavaScript

在 JavaScript 中，几乎“所有事物”都是对象。

- 布尔是对象（如果用 new 关键词定义）
- 数字是对象（如果用 new 关键词定义）
- 字符串是对象（如果用 new 关键词定义）
- 日期永远都是对象
- 算术永远都是对象
- 正则表达式永远都是对象
- 数组永远都是对象
- 函数永远都是对象
- 对象永远都是对象
所有 JavaScript值,除了原始值,都是对象.

### JavaScript 原始值
原始值指的是没有属性或方法的值

原始数据类型指的是拥有原始值的数据

JavaScript定义了5种原始数据类型:
- string
- number
- boolean
- null
- undefined
原始值是一成不变的


## JavaScript对象属性

### JS属性
属性指的是与 JavaScript 对象相关的值
JavaScript对象是无序属性的集合
属性通常可以被修改,添加和删除,但是某些属性是只读的.

### JavaScript for...in 循环
JavaScript `for...in`语句遍历对象的属性

```JavaScript
for(variable in object){
    //...
    }

```

### 添加新属性
您可以通过简单的赋值，向已存在的对象添加新属性。

假设 person 对象已存在 - 那么您可以为其添加新属性

```
person.nationality = "English"
```

### 删除属性
`delete`关键词从对象中删除属性

```
var person = {firstName:"Bill",lastName:"Gates",age:62,eyeColor:"blue"};
delete person.age;
```

`delete`关键字会同时删除属性的值和属性本身
`delete`操作符被设计用于对象属性.它对变量或函数没有影响
`delete`操作符不应被用于预定义的 JavaScript对象属性.这样做会使应用程序崩溃.

### 属性值
值是属性的特征之一
其他特征包括:可列举,可配置,可写

### 原型属性
JavaScript 对象继承了它们的原型的属性
`delete`关键词不会删除被继承的属性,但是如果您删除了某个原型属性,则将影响到所有从原型继承的对象


## JS对象访问器

### JavaScript 访问器(Getter和Setter)
Getter 和 Setter 允许您定义对象访问器

### JavaScript Getter(get关键字)
本例使用 `lang`属性来获取`language`属性的值

```JavaScript
// 创建对象：
var person = {
  firstName: "Bill",
  lastName : "Gates",
  language : "en",
  get lang() {
    return this.language;
  }
};

// 使用 getter 来显示来自对象的数据：
document.getElementById("demo").innerHTML = person.lang;
```

### JavaScript Setter(set 关键字)
本例使用 `lang`属性来设置 `language`属性的值

```JavaScript
var person = {
  firstName: "Bill",
  lastName : "Gates",
  language : "",
  set lang(lang) {
    this.language = lang;
  }
};

// 使用 setter 来设置对象属性：
person.lang = "en";

// 显示来自对象的数据：
document.getElementById("demo").innerHTML = person.language;

```

### 数据质量
使用 getter 和 setter 时,JavaScript 可以确保更好的数据质量


## JS对象原型

###　向对象添加属性和方法

使用 prototype 属性

JavaScript prototype 属性允许您为对象构造器添加新属性:

```JavaScript
function Person(first, last, age, eyecolor) {
    this.firstName = first;
    this.lastName = last;
    this.age = age;
    this.eyeColor = eyecolor;
}
Person.prototype.nationality = "English";
```

JavaScript prototype 属性允许您为对象构造器添加新方法:

```JavaScript
function Person(first, last, age, eyecolor) {
    this.firstName = first;
    this.lastName = last;
    this.age = age;
    this.eyeColor = eyecolor;
}
Person.prototype.name = function() {
    return this.firstName + " " + this.lastName;
};
```


