# Java 数组

Java语言中提供的数组是用来存储固定大小的同类型元素。

## 声明数组变量
首先必须声明数组变量，才能在程序中使用数组。下面是声明数组变量的语法：
```java
dataType[] arrayRefVar;   // 首选的方法
 
或
 
dataType arrayRefVar[];  // 效果相同，但不是首选方法
```
注意：建议使用 dataType[] arrayRefVar 的声明风格声明数组变量

实例:
```java
double[] myList;         // 首选的方法
 
或
 
double myList[];         //  效果相同，但不是首选方法
```
## 创建数组
Java语言使用new操作符来创建数组，语法如下：

`arrayRefVar = new dataType[arraySize];`

上面的语法语句做了两件事：  
- 使用 dataType[arraySize] 创建了一个数组
- 把新创建的数组的应用赋值给变量 arrayRefVar

数组变量的声明，和创建数组可以使用同一条语句完成，如下所示：
`dataType[] arrayRefvar = new dataType[araySize];`

另外，你还可以使用如下方式创建数组
`dataType[] arrayRefVar = {value0,value1,...,valuek};`
数组的元素是通过索引访问的，数组索引从0开始，所以索引从0到arrayRefVar.length-1



## 处理数组
数组的元素类型和数组的大小都是确定的，所以当处理数组元素时候，我们通常使用基本循环或者 For-Each 循环。

该实例完成的展示了如何创建、初始化和操纵数组：
```java
public class TestArray{
    public static void main(String[] args){
        double[] myList = {1.9,2.9,3.4,2.5}

        //打印所有数组元素
        for (int i=0;i<myList.length;i++){
            System.out.println(myList[i] + " ");
        }
        // 计算所有元素的和
        double total = 0
        for (int i=0;i<myList.length;i++){
            total += myList[i];
        }
        System.out.println("Total is" + total);
        //查找最大元素
        double max = myList[0];
        for (int i = 1;i<myList.length;i++){
            if (myList[i]>max) max = myList[i];
        }
        System.out.print("Max is" + max);
    }
}

```

## For-Each 循环

JDK 1.5 引入了一种新的循环类型，被称为 For-Each 循环或者加强型循环，它能在不使用下标的情况下遍历数组。

语法格式如下：  

```java
for(type element: array)
{
    System.out.println(element);
}
```

该实例用来显示数组 myList 中的所有元素：
```java
public class TestArray {
   public static void main(String[] args) {
      double[] myList = {1.9, 2.9, 3.4, 3.5};
 
      // 打印所有数组元素
      for (double element: myList) {
         System.out.println(element);
      }
   }
}

```

## 数组作为函数的参数

数组可以作为参数传递给方法。

## 数组作为函数的返回值


##  多维数组
多维数组可以看成是数组的数组，比如二维数组就是一个特殊的一维数组，其每一个元素都是一个一维数组，例如：

`String str[][] = new String[3][4];`

### 多维数组的动态初始化（以二维数组为例）
 1、直接为每一维数组分配空间，格式如下：
 `type[][] typeName = new type[typeLength1][typeLength2];`
type 可以为基本数据类型和复合数据类型，arraylength1 和 arraylength2 必须为正整数，arraylength1 为行数，arraylength2 为列数。

`int a[][] = new int[2][3];`

2、从最高维开始，分别为每一维分配空间，例如：

```java
String s[][] = new String[2][];
s[0] = new String[2];
s[1] = new String[3];
s[0][0] = new String("Good");
s[0][1] = new String("Luck");
s[1][0] = new String("to");
s[1][1] = new String("you");
s[1][2] = new String("!");
```
解析：
s[0]=new String[2] 和 s[1]=new String[3] 是为最高维分配引用空间，也就是为最高维限制其能保存数据的最长的长度，然后再为其每个数组元素单独分配空间 s0=new String("Good") 等操作。


### 多维数组的引用（以二维数组为例）
对二维数组中的每个元素，引用方式为 arrayName[index1][index2]，例如：
`num[1][0];`


## Arrays 类
java.util.Arrays 类能方便地操作数组，它提供的所有方法都是静态的。

具有以下功能：

- 给数组赋值：通过 fill 方法。
- 对数组排序：通过 sort 方法,按升序。
- 比较数组：通过 equals 方法比较数组中元素值是否相等。
- 查找数组元素：通过 binarySearch 方法能对排序好的数组进行二分查找法操作。