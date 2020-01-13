# Java 泛型


## 为什么需要泛型
JDK5引入了泛型机制

为什么需要泛型呢?回答这个问题之前,我们先来看一个示例:

```java

import sun.security.krb5.internal.ETypeInfo;

import java.util.ArrayList;
import java.util.List;

public class NoGenericsDemo {
    public static void main(String[] args) {
        List list = new ArrayList<>();
        list.add("abc");
        list.add(18);
        list.add(new double[]{1.0, 2.0});
        Object obj1 = list.get(0);
        Object obj2 = list.get(1);
        Object obj3 = list.get(2);
        System.out.println(obj1);
        System.out.println(obj2);
        System.out.println(obj3);


        int num1 = (int)list.get(0);
        int num2 = (int)list.get(1);
        int num3 = (int)list.get(2);
        System.out.println(num1);
        System.out.println(num2);
        System.out.println(num3);

    }
}

#abc
#18
#[D@60e53b93
#Exception in thread "main" java.lang.ClassCastException: java.lang.String cannot be cast to java.lang.Integer
#	at NoGenericsDemo.main(NoGenericsDemo.java:19)

```
```
示例说明:
在上面的示例中,List容器没有指定存储数据类型,这种情况下,可以像List添加任意类型数据,编译器不会做类型检查,而是默默的将所有数据转化为 object.
假设,我们最初希望 List 存储的是整形数据,假设,某个家伙不小心存入了其他数据类型,当你试图从容器中取整形数据的时候,由于 List 当成 Object 类型存储起来,你不得不使用类型强制转换.
在运行时,才会发现 List 中的数据存储不一致问题,这就为程序运行带来了很大的风险.
```

而泛型的出现,解决了类型安全问题.

泛型具有以下优点:
- 编译时的强类型检查

泛型要求在声明时指定实际数据类型,java编译器在编译时会针对泛型代码做强类型检查,并在代码违反类型安全时发出警告

- 避免了类型转换

未使用泛型:
```java
List list = new ArrayList();
List.add("hello");
String s = (String) list.get(0);
```

使用泛型
```java
List<String> list = new ArrayList<String>();
list.add("hello");
String s = list.get(0);

```

- 泛型编程可以实现通用算法

通过使用泛型,程序员可以实现通用算法,这些算法可以处理不同类型的集合,可以自定义,并且类型安全易于阅读.

## 泛型类型

`泛型类型` 是被参数化的类或接口

### 泛型类
泛型类的语法形式:
`class name<T1,T2,...,Tn>{/* ... */}`
泛型类的声明和非泛型类的声明类似,除了类名后面添加类型参数声明部分.由尖括号(`<>`)分隔的类型参数部分跟在类名后面.它指定类型参数(也成为类型变量) T1,T2,....Tn

一般将泛型的类名称为`原型`,而将`<>`指定的参数称为`类型参数`

在程序中,在初始化一个泛型类时,使用`<>`指定了内部具体类型,在编译时就会根据这个类型做强类型检查.


- 未使用泛型的类
在泛型出现之前,如果一个类想持有一个可以为任意类型的数据,只能使用 `object` 做类型转化.示例如下:

```java
public class Info {
    private Object value;

    public Object getValue() {
        return value;
    }

    public void setValue(Object value) {
        this.value = value;
    }
}

```

- 单类型参数的泛型类
```java

public class Info<T> {
    private T value;

    public Info() { }

    public Info(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }

    public void setValue(T value) {
        this.value = value;
    }

    @Override
    public String toString() {
        return "Info{" + "value=" + value + '}';
    }
}

public class GenericsClassDemo01 {
    public static void main(String[] args) {
        Info<Integer> info = new Info<>();
        info.setValue(10);
        System.out.println(info.getValue());

        Info<String> info2 = new Info<>();
        info2.setValue("xyz");
        System.out.println(info2.getValue());
    }
}
// Output:
// 10
// xyz
```

在上面的例子中，在初始化一个泛型类时，使用 <> 指定了内部具体类型，在编译时就会根据这个类型做强类型检查。

实际上，不使用 <> 指定内部具体类型，语法上也是支持的（不推荐这么做），如下所示：

```java

public static void main(String[] args) {
    Info info = new Info();
    info.setValue(10);
    System.out.println(info.getValue());
    info.setValue("abc");
    System.out.println(info.getValue());
}
```
上面的例子,不会产生编译错误,也能正常运行.但这样的调用就失去了泛型类型的优势

- 多个类型参数的泛型类

```java
class MyMap<K, V> {
    private K key;
    private V value;

    public MyMap(K key, V value) {
        this.key = key;
        this.value = value;
    }

    @Override
    public String toString() {
        return "MyMap{" + "key=" + key + ", value=" + value + "}";
    }
}


public class GenericsClassDemo2 {
    public static void main(String[] args) {
        MyMap<Integer, String> map = new MyMap<>(1, "one");
        System.out.println(map);
    }
}
# MyMap{key=1, value=one}
```

- 泛型类的类型嵌套

```java
public class GenericsClassDemo03 {
    public static void main(String[] args) {
        Info<String> info = new Info("Hello");
        MyMap<Integer, Info<String>> map = new MyMap<>(1,info);
        System.out.println(map);
    }
}
# MyMap{key=1, value=Info{value=Hello}}
```



### 泛型接口
接口也可以声明泛型

泛型接口语法形式:
```java
public interface Content<T>{
    T text();
}
```

泛型接口有两种实现方式:

- 实现接口的子类明确声明泛型类型
```java

public class GenericsInterfaceDemo01 implements Content<Integer> {
    private int text;

    public GenericsInterfaceDemo01(int text){
        this.text = text;
    }


    @Override
    public Integer text() {
        return text;
    }

    public static void main(String[] args) {
        GenericsInterfaceDemo01 demo = new GenericsInterfaceDemo01(10);
        System.out.println(demo.text());
    }
}

```

- 实现接口的子类不明确声明泛型类型

```java
public class GenericsInterfaceDemo02<T> implements Content<T> {
    private T text;

    public GenericsInterfaceDemo02(T text) {
        this.text = text;
    }

    @Override
    public T text() {
        return text;
    }

    public static void main(String[] args) {
        GenericsInterfaceDemo02<String> gen = new GenericsInterfaceDemo02<>("ABC");
        System.out.println(gen.text());

    }
}
# ABC

```
注意这里实现接口的地方有点不一样


## 泛型方法

泛型方法是引入其自己的类型参数的方法.泛型方法可以是普通方法,静态方法以及构造方法.

泛型方法语法形式如下:

```
public <T> T func(T obj) {}
```

`是否拥有泛型方法,与其所在的类是否是泛型没有关系`

泛型方法的语法包括一个类型参数列表,在尖括号内,它出现在方法的返回类型之前.
对于静态泛型方法,类型参数部分必须出现在方法的返回类型之前.类型参数能被用来声明返回值类型,并且能作为泛型方法得到的实际类型参数的占位符.

`使用泛型方法时候,通常不必指明类型参数,因为编译器会为我们找出具体的类型.这称为类型参数推断.类型推断只对赋值操作有效,其他时候并不其作用`.

如果将一个泛型方法调用的结果作为参数,传递给另一个方法,这时编译器并不会执行推断.编译器会认为:调用泛型方法后,其返回值被赋给一个Object 类型的变量.

```java
public class GenericsMethidDemo01 {
    public static <T> void printClass(T obj){
        System.out.println(obj.getClass().toString());
    }

    public static void main(String[] args){
        printClass("abc");
        printClass(10);
    }
}
# class java.lang.String
# class java.lang.Integer

```

泛型方法中也可以使用可变参数列表

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class GenericVarargsMethidDemo {
    public static <T> List<T> makeList(T... args) {
        List<T> result = new ArrayList<T>();
        Collections.addAll(result, args);
        return result;
    }

    public static void main(String[] args) {
        List<String> ls = makeList("A");
        System.out.println(ls);
        ls = makeList("A", "B", "C");
        System.out.println(ls);
    }
}

// [A]
// [A, B, C]

```

## 类型擦除
 
 java 语言引入泛型是为了在编译时提供更严格的类型检查,并支持泛型编程.不同 C++ 的模板机制,
 `java泛型是使用类型擦除来实现的,使用泛型时,任何具体的类型信息都被擦除了`.

那么,类型擦除能做什么呢?它做了以下工作:
- 把泛型中的所有类型参数替换为 Object ,如果指定类型边界,则使用类型边界来替换.因此,生成的字节码仅包含普通的类,接口和方法.

- 擦除出现的类型声明,即去掉 `<>`的内容.比如 `T get()`方法声明就变成了 `Object get()`;`List<String>`就边成 `List`.如有必要,插入类型转换以保持类型安全.

- 生成桥接方法以保留扩展泛型类型中的多态性.类型擦除确保不为参数化类型创建新类;因此,泛型不会产生运行时开销.


```java
import java.util.ArrayList;
import java.util.List;

public class GenericsErasureTypeDemo {
    public static void main(String[] args) {
        List<Object> list1 = new ArrayList<Object>();
        List<String> list2 = new ArrayList<String>();
        System.out.println(list1.getClass());
        System.out.println(list2.getClass());

    }
}
// class java.util.ArrayList
// class java.util.ArrayList

```
上面的例子中,虽然我们指定了不同的类型参数,但是list1和list2的类信息却是一样的.
这是因为:`使用泛型时,任何具体的类型信息都被擦除了`
这意味着:`ArrayList<Object>`和`ArrayList<String>`在运行时,JVM将它们视为同一类型.


Java 泛型的实现方式不太优雅,但这是因为泛型是在 JDK5 时代引入的,为了兼容老代码,必须在设计上做一定的折中.


## 泛型和继承
`泛型不能用于显示地引用运行时类型的操作之中,例如,转型, instanceof 操作和new表达式,因为所有关于参数的类型信息都丢失了`


正是由于泛型时基于类型擦除实现的,所以,`泛型类型无法向上转型`

向上转型是指用子类实例去初始化父类,这是面向对象中多态的重要表现.

![](assets/20200113192842798_2128816898.png =600x)

`Integer`继承了`Object`;`ArrayList`继承了`List`;但是`List<Integer>`却并非继承了`List<Object>`.

这是因为,泛型类并没有自己独有的 class 类对象.比如:并不存在`List<Object>.class`或是`List<Integer>.class`,Java编译器会将二者都视为`List.class`



## 类型边界
[fanxing](https://dunwu.github.io/javacore/#/basics/java-generic)