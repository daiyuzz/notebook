# Java循环结构
- while 循环
- do…while 循环
- for 循环



对于while语句而言，如果不满足条件，则不进入循环。但有时候我们需要即使不满足条件，也至少执行一次。
do...while循环和while循环相似，不同的是，do...while 循环至少会执行一次。

```java
do {
       //代码语句
}while(布尔表达式);
```

java增强for循环

Java增强for循环的语法格式如下：
```java
for(声明语句 : 表达式)
{
   //代码句子
}
```
- 声明语句：声明新的局部变量，该变量的类型必须和数组元素的类型匹配。其作用域限定在循环语句块，其值与此时数组元素的值相等。

- 表达式：表达式是要访问的数组名，或者是返回值为数组的方法。

```java
public class Test {
   public static void main(String args[]){
      int [] numbers = {10, 20, 30, 40, 50};
 
      for(int x : numbers ){
         System.out.print( x );
         System.out.print(",");
      }
      System.out.print("\n");
      String [] names ={"James", "Larry", "Tom", "Lacy"};
      for( String name : names ) {
         System.out.print( name );
         System.out.print(",");
      }
   }
}
```

以上实例编译运行结果如下：
```
10,20,30,40,50,
James,Larry,Tom,Lacy,
```
