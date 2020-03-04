# JS函数

## JS函数定义

Hoisting 是 JavaScript 将申明移到当前作用域顶端的默认行为
Hoisting 应用于变量声明和函数声明
正因如此,JavaScript 函数能够在声明之前被调用

```JavaScript
myFunction(5);

function myFunction(y){
        return y*y
    }
```
使用表达式定义的函数不会被提升


### 自调函数
函数表达式可以作为"自调用"
自调用表达式是自动被调用(开始)的,在不进行调用的情况下.
函数表达式不会自动执行,假如表达式后面跟着`()`
您无法对函数声明进行自调用
需要在函数周围添加括号,以指示它是一个函数表达式

### 箭头函数
箭头函数允许使用简短的语法来编写函数表达式.
不需要function 关键字,return 关键字和花括号

```javaScript
//ES5
var x = function(x,y){
    return x*y;
    }

//ES6
const x = (x,y) => x*y;
```
箭头函数没有自己的 this.他们不适合定义对象方法.
箭头函数未被提升.他们必须在使用前进行定义
使用 const 比使用 var 更安全,因为函数表达式始终是常量值
如果函数是单个语句,则只能省略 return 关键字和大括号

```
const x = (x,y) => {return x*y};
```

## JS函数Call

### JavaScript call()方法
call() 方法是预定义的 JavaScript 方法
它可以用来调用所有者对象作为参数的方法
通过`call()`,您能够使用属于另一个对象的方法
本例利用 person 的 fullName 方法,并用于 person1:
```JavaScript
var person = {
    fullName: function() {
        return this.firstName + " " + this.lastName;
    }
}
var person1 = {
    firstName:"Bill",
    lastName: "Gates",
}
var person2 = {
    firstName:"Steve",
    lastName: "Jobs",
}
person.fullName.call(person1);  // 将返回 "Bill Gates"

```



## JS函数 Apply

### 方法重用
通过`apply()`方法,您能够编写用于不同对象的方法.

### JavaScript apply()方法
`apply()`方法与`call()`方法非常相似:
在本例中,`person`的`fullName`方法被应用到`person1`:

```JavaScript
var person = {
    fullName: function() {
        return this.firstName + " " + this.lastName;
    }
}
var person1 = {
    firstName: "Bill",
    lastName: "Gates",
}
person.fullName.apply(person1);  // 将返回 "Bill Gates"
```

### call() 和 apply()之间的区别
不同之处:
`call()`方法分别接收参数
`apply()`方法接收数组形式的参数
如果要使用数组而不是参数列表,则`apply()`方法非常方便

### 带参数的 apply()方法
`apply()`方法接受数组中的参数
```JavaScript
var person = {
  fullName: function(city, country) {
    return this.firstName + " " + this.lastName + "," + city + "," + country;
  }
}
var person1 = {
  firstName:"John",
  lastName: "Doe"
}
person.fullName.apply(person1, ["Oslo", "Norway"]);
```


## JS闭包
JavaScript 变量属于本地或全局作用域
全局变量能够通过闭包实现局部(私有)




