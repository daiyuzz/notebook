# Java 容器概述

## 容器简介

### 数组与容器

java中常用的存储容器就是数组和容器,二者有以下区别:
- 数组是固定长度的;容器的长度是可变的.
- 数组可以存储基本数据类型,也可以存储引用数据类型;容器只能存储引用数据类型,基本数据类型的变量需转化成对应的包装类才能放入容器类中.


![](assets/20200116163601745_565738286.png =1000x)

`Java`容器框架主要分为 `Collection` 和 `Map` 两种.其中,`Collection`又分为 `List`,`Set` 以及`Queue`.

- `Collection` :一个独立元素的序列,这些元素都服从一条或者多条规则
    - `List`:必须按照插入的顺序保存元素
    - `Set`:不可能有重复的元素
    - `Queue`:按照排队规则来确定对象产生的顺序(通常与它们被插入的顺序相同).

- `Map`:一组成对的"键值对"对象,允许你使用键来查找


## 容器的基本机制

### 泛型
java `容器通过泛型技术来保证其数据的类型安全`,什么是类型安全呢?

举例来说：如果有一个 List<Object> 容器，Java 编译器在编译时不会对原始类型进行类型安全检查，却会对带参数的类型进行检查，通过使用 Object 作为类型，可以告知编译器该方法可以接受任何类型的对象，比如 String 或 Integer。


### Iterable 和 Iterator


`Collection`接口扩展了 `Iterable`接口

迭代是一个经典的设计模式 -- 迭代器模式

`迭代器模式- 提供一种方法顺序访问一个聚合对象中各个元素,而又无需暴露对象的内部表示`

示例:迭代器遍历

```java
public class IteratorDemo{
    plubli static void main(String[] args){
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);
        Iterator it = list.iterator();
        while(it.hasNext()):
            System.out.println(it.next())
    }
}
}
```


### Comparable 和 Comparator

`Comparable` 是排序接口.若一个类实现了 `Comparable`接口,就意味着该类支持排序.实现了 Comparable 接口的类的对象的列表或数组可以通过 `Collections.sort` 或 `Arrays.sort`进行自动排序.


`Comparator`是比较接口,我们如果需要控制某个类的次序,而该类本身不支持排序(即没有实现`Comparable`接口),那么我们就可以建立一个"该类的比较器"来进行排序,这个"比较器"只需要实现 `Comparator`接口即可.
也就是说，我们可以通过实现 `Comparator` 来新建一个比较器，然后通过这个比较器对类进行排序


在 Java 容器中，一些可以排序的容器，如 TreeMap、TreeSet，都可以通过传入 Comparator，来定义内部元素的排序规则。


### Cloneable
java 中一个类要实现 `clone`功能,必须实现 `Cloneable`接口,否则在调用 `Clone()`时会报 `CloneNotSuppressException`异常.

Java 中所有类都默认继承 java.lang.Object 类，在 java.lang.Object 类中有一个方法 clone()，这个方法将返回 Object 对象的一个拷贝。Object 类里的 clone() 方法仅仅用于浅拷贝（拷贝基本成员属性，对于引用类型仅返回指向改地址的引用）

如果 Java 类需要深拷贝，需要覆写 clone() 方法。

### fail-fast

fail-fast 是 Java 容器的一种错误检测机制
当多个线程对容器进行结构上的改变的操作时，就可能触发 fail-fast 机制。记住是有可能，而不是一定

