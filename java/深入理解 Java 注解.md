# 深入理解 Java 注解

## 简介

### 注解的形式
Java中,注解是以 `@`字符开始的修饰符.如下:

```java
@Override
void mySuperMethod(){...}
```
注解可以包含命名或未命名的属性,并且这些属性有值.

```java
@Author(
   name = "Benjamin Franklin",
   date = "3/27/2003"
)
class MyClass() { ... }

```

如果只有一个名为 value 的属性，那么名称可以省略，如：

```java
@SuppressWarnings("unchecked")
void myMethod() { ... }
```

如果注解没有属性，则称为标记注解。如：`@Override`


### 什么是注解
从本质上来说,`注解是一种标签,其实质上可以视为一种特殊的注释,如果没有解析它的代码,它并不比普通注释强`


解析一个注释往往有两种形式:

- 编译器直接的扫描
- 运行期的反射


### 注解的作用

- 编译器信息 - 编译器可以使用注解来检测错误或抑制警告
- 编译时和部署时的处理 - 程序可以处理注解信息以生成代码，XML 文件等
- 运行时处理 - 可以在运行时检查某些注解并处理



### 注解的代价
- 显然,它是一种侵入式编程,那么,自然就存在着增加程序耦合度的问题
- 自定义注解的处理需要在运行时,通过反射技术来获取属性.如果注解所修饰的元素是非 public 成员,也可通过反射获取.这就违背了面性对象的封装性.




## 内置注解

JDK内置了以下注解:
- `@override`
- `@Deprecated`
- `@SuppressWarnnings`
- `@SafeVarargs`
- `@FunctionalInterface`


### @Override

`@Override`用于表明被修饰方法覆写了父类的方法


### @Deprecated
`@Deprecated`用于标明被修饰的类或成员,类方法已经废弃,过时,不建议使用.


### @SuppressWarnnings

`@SuppressWarnnings` 用于关闭对类.方法,成员编译时产生的特定警告


### @SafeVarargs

`@SafeVarargs` 的作用是：告诉编译器，在可变长参数中的泛型是类型安全的。可变长参数是使用数组存储的，而数组和泛型不能很好的混合使用。



### @FunctionalInterface

`@!FunctionalInterface`用于指示被修饰接口是函数式接口



















https://dunwu.github.io/javacore/#/basics/java-annotation