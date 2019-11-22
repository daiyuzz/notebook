# Java String 类

字符串广泛应用在 Java编程中，在Java中字符创属于对象，Java提供了String 类来创建和操作字符串。

## 创建字符创
创建字符串最简单的方式如下:

`String greeting = "菜鸟教程";`

在代码中遇到字符串常量时，这里的值是 "菜鸟教程""，编译器会使用该值创建一个 String 对象。

和其它对象一样，可以使用关键字和构造方法来创建 String 对象。

String 类有 11 种构造方法，这些方法提供不同的参数来初始化字符串，比如提供一个字符数组参数:

```java
public class StringDemo{
   public static void main(String args[]){
      char[] helloArray = { 'r', 'u', 'n', 'o', 'o', 'b'};
      String helloString = new String(helloArray);  
      System.out.println( helloString );
   }
}
```
以上实例编译运行结果如下：
```
runoob
```

注意:String 类是不可改变的，所以你一旦创建了 String 对象，那它的值就无法改变了

## 字符串长度
用于获取有关对象的信息的方法称为访问器方法。

String 类的一个访问器方法是 length() 方法，它返回字符串对象包含的字符数。

下面的代码执行后，len 变量等于 14:
```java
public class StringDemo {
    public static void main(String args[]) {
        String site = "www.runoob.com";
        int len = site.length();
        System.out.println( "菜鸟教程网址长度 : " + len );
   }
}
```
`菜鸟教程网址长度 : 14`


## 连接字符串
String 类提供了连接两个字符串的方法：

-`string1.concat(string2);`
返回 string2 连接 string1 的新字符串。也可以对字符串常量使用 concat() 方法，如：

`"我的名字是 ".concat("Runoob");`
更常用的是使用'+'操作符来连接字符串，如：

`"Hello," + " runoob" + "!"`
结果如下:

`"Hello, runoob!"`


## 创建格式化字符串
我们知道输出格式化数字可以使用 printf() 和 format() 方法。

String 类使用静态方法 format() 返回一个String 对象而不是 PrintStream 对象。

String 类的静态方法 format() 能用来创建可复用的格式化字符串，而不仅仅是用于一次打印输出。

```java
System.out.printf("浮点型变量的值为 " +
                  "%f, 整型变量的值为 " +
                  " %d, 字符串变量的值为 " +
                  "is %s", floatVar, intVar, stringVar);
```
你也可以这样写

```java
String fs;
fs = String.format("浮点型变量的值为 " +
                   "%f, 整型变量的值为 " +
                   " %d, 字符串变量的值为 " +
                   " %s", floatVar, intVar, stringVar);
```

## String 方法

- `char charAt(int index)`
- `int compareTo(Object o)`
- `int compareTo(String anotherString)`
- `int compareToIgnoreCase(String str)`
- `String concat(String str)`
- `boolean contentEquals(StringBuffer sb)`
- `static String copyValueOf(char[] data)`
- `static String copyValueOf(char[] data, int offset, int count)`
- `boolean endsWith(String suffix)`
- `boolean equals(Object anObject)`
- `boolean equalsIgnoreCase(String anotherString)`
- `byte[] getBytes()`
- `byte[] getBytes(String charsetName)`
- `void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin)` 
- `int hashCode()`
- `int indexOf(int ch)`
- `int indexOf(int ch, int fromIndex)`
- `int indexOf(String str)`
- `int indexOf(String str, int fromIndex)`
- `String intern()`
- `int lastIndexOf(int ch)`
- `int lastIndexOf(int ch, int fromIndex)`
- `int lastIndexOf(String str)`
- `int lastIndexOf(String str, int fromIndex)`
- `int length()`
- `boolean matches(String regex)`
- `boolean regionMatches(boolean ignoreCase, int toffset, String other, int ooffset, int len)`
- `boolean regionMatches(int toffset, String other, int ooffset, int len)`
- `String replace(char oldChar, char newChar)`
- `String replaceAll(String regex, String replacement)`
- `String replaceFirst(String regex, String replacement)`
- `String[] split(String regex)`
- `String[] split(String regex, int limit)`
- `boolean startsWith(String prefix)`
- `boolean startsWith(String prefix, int toffset)`
- `CharSequence subSequence(int beginIndex, int endIndex)`

