在Java中，处理字符串、文本的时候，一般常用一下三种类：

String、StringBuffer、StringBuilder

三者分别有各自适用的场合。

String：适用于少量的字符串操作的情况。

StringBuilder：适用于单线程下在字符缓冲区进行大量操作的情况。

StringBuffer：适用多线程下在字符缓冲区进行大量操作的情况。

**在运行方面速度快慢为：StringBuilder > StringBuffer > String**

String最慢的原因：

　String为字符串常量，而StringBuilder和StringBuffer均为字符串变量，即String对象一旦创建之后该对象是不可更改的，但后两者的对象是变量，是可以更改的。

对于Java初学者来说，初步掌握string是必不可少的。而StringBuffer和StringBuilder我们先不必掌握，稍有了解即可。以后有应用的场合我们再作补充。

# String用法：

## 1、数组字符串相互转化

### 1.1数组->字符串

##### **byte数组**转字符串举例如下：

```java
public class str
{
    public static void main(String[] args)
    {
        byte[] b={97,98,99};
        String str=new String(b);
        //String string =String.copyValueOf(arr);
        System.out.println(str);
    }
}
```

输出结果：abc

##### **char数组转字符串举例如下：**

```java
public class strTest
{
    public static void main(String[] args)
    {
        char[] c={'H','e','l','l','o'};
        String str=new String(c);
        System.out.println(str);
    }
}
```

输出结果：Hello

当然，也可以把数组中的特定元素片段转换成字符串输出。

```java
public class strTest
{
    public static void main(String[] args)
    {
        byte[] b={97,98,99,100,101,102};//注意是byte 是字节码
        String str=new String(b,3,2);
        System.out.println(str);
    }
}
```

输出结果：de

从数组元素b[3]开始（包括b[3]在内）的连续两个元素以字符串形式输出。

##### **String数组**转字符串

```java
String[] arr ={"0123","sb","12f"};

　　StringBuffer sb = new StringBuffer();
　　for(int i = 0;i<arr.length;i++){
　　sb.append(arr[i]);        //append String并不拥有该方法，所以借助StringBuffer
　　}
　　String sb1 = sb.toString();
　　System.out.println(sb1);    //0123sb12f


 String str = new String(chars);
```

##### int数组转字符串

```java
//方法1
int[] array ={1,2,3,4,5,6,7,8,9,10};
        System.out.println(Arrays.toString(array));
        //输出[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
        StringBuilder  sb = new StringBuilder ();
        for(int i=0;i<array.length;i++){
            sb.append(array[i]+",");
        }
        System.out.println(sb.toString());//最后记得to String
        //输出1,2,3,4,5,6,7,8,9,10,
```

### 1.1字符串->数组

##### 字符串转化为int数组

```java
    String str = "6921168509256";
    int[] arr = new int[str.length()];
    for (int i = 0; i < str.length(); i++) {
        arr[i] = Integer.parseInt(str.substring(i, i + 1));//substring是找出包含起始位置，不包含结束位置，到结束位置的前一位的子串
    }
    for(int i:arr)//输出数组arr
        System.out.print(i+" ");
```

##### **字符串转化为char数组**

```java
char[] chars = str.toCharArray();
```

###  1.3集合转化为数组

```java
List<String> list = new ArrayList<>(2);
list.add("guan");
list.add("bao");
String[] array = list.toArray(new String[0]);
```




## **2、字符串中的一些常用函数：连接concat()、提取substring()、charAt()、length()、equals()、equalsIgnoreCase()等等。**

```java
String str1="you";
String str2=" welcome";        
System.out.println(str1.concat(str2));
```

输出结果：you welcome

```java
String str="we are students and he is a techer";
System.out.println(str.substring(2,10));
```

输出结果： are stu

从str[2]开始，到str[9]结束。
substring() 方法用于提取字符串中介于两个指定下标之间的字符。

**stringObject.substring(start,stop)**

```
 String str="we are students and he is a worker";
 System.out.println(str.charAt(1));
```

输出结果：e

**charAt(int index)方法是一个能够用来检索特定索引下的字符的String实例的方法.**
charAt()方法返回指定索引位置的char值。索引范围为0~length()-1.
如: str.charAt(0)检索str中的第一个字符,str.charAt(str.length()-1)检索最后一个字符.

```java
 String str="we are";
 System.out.println(str.length());
```

输出结果：6

**比较两个字符串是否相同用equals()**

```java
public class test {
	public static void main(String[] args) {
		 String str1="we are students and he is a worker";
		 String str2="we are students and he is a worker";
	        System.out.println(str1.equals(str2));
	}
}
```

输出结果：true

**忽略字符大小写的字符串比较用equalsIgnoreCase()**

```java
public class test {
 
	public static void main(String[] args) {
		 String str1="we are students and he is a worker";
		 String str2="We ARE students AND he is a worker";
	        System.out.println(str1.equalsIgnoreCase(str2));
	}
}
```



## 3、字符串的一些检索查找字符的函数

```java
public class test {
 
	public static void main(String[] args) {
		String str="我们一起数到6吧！";
		System.out.println(str.indexOf("一"));
                System.out.println(str.indexOf("6"));
                System.out.println(str.startsWith("我"));
                System.out.println(str.endsWith("！"));
	}
}
```

**startsWith() 方法用于检测字符串是否以指定的前缀开始。**
输出结果

```
2
6
true
true
```

## 4.Java如何把String字符串转换为数字？

**4.1转换为浮点型：**  

使用Double或者Float的parseDouble或者parseFloat方法进行转换

```java
String   s   =   "123.456 ";  
double   d   =   Double.parseDouble(s); 
float   f   =   Float.parseFloat(s);
```

**转换为整型：** 

使用Integer的parseInt方法进行转换。

```java
int i = Integer.parseInt(str);//str待转换的字符串
```

**转化为char数组**

```java
char[] chars = str.toCharArray();
```


Java如何实现位数对齐

```java
 System.out.println(String.format("%02d:%02d:%02d",hh,mm,ss));
```

还有，Java字符串中还有一个非常非常好用的函数，力推啊！帮忙解决了很多问题，真的很好用（emmmm，我咋突然感觉自己像买东西滴（此处手动滑稽~~~））

## 5.Indexof

让我告诉你它金光闪闪的名字：IndexOf（）！！！

因为它今天下午帮了我很大很大的忙，所以我很感谢它，为它疯狂打call！

emmmm，废话不多说，回到正题。

举个例子吧！

```java
package test_str;
 
import java.util.*;
 
public class Str {
 
    public static void main(String[] args) {
        String str="Hello, world, !";
        int n1 = str.indexOf(',');
        System.out.println(n1);
        int n2 = str.indexOf(',',n1+1);
        System.out.println(n2);
        int n3 = str.indexOf(',',n1);
        System.out.println(n3);
        System.out.println(str.startsWith("H"));
        System.out.println(str.endsWith("!"));
 
    }
 
}
```

```
5
12
5
true
true
```

str.indexOf(',')，表示在整个str字符串中检索！

int n3 = str.indexOf(',',n1);表示从n1开始检索！

## 6.Java的split函数的基本用法

```java
	public static void main(String[] args) {
              String str="good good study, day day up";
	      String[] strarray=str.split(" ");
	      for (int i = 0; i < strarray.length; i++)
	          System.out.println(strarray[i]);
	}
```

输出结果：

```java
good
good
study,
day
day
up
```

**注意：Java里面分离小数点要用到转义字符，否则得不到正确答案**！

```java
String []s = str.split("\\.");
```

## 7.字符串去空格

String.trim() 　trim()是去掉首尾空格 

常用字符：空格(‘ ’) 、换页 (‘\f’) 、换 行(‘\n’)、回车(‘\r’)、水平 制表符 (‘\t’)、垂直 制表符 (‘\v’)



# StringBuffer

下面来总结一下StringBuffer的一些常见用法：

StringBuffer支持以下几种操作函数：**append（）、insert（）、replace（）、delete（）、reserve（）**等等。

public StringBuffer append(String s)：将指定的字符串追加到此字符序列。

StringBuffer insert（int index，String str）：在index前插入字符串。

replace(int start, int end, String str)：使用给定 `String` 中的字符替换此序列的子字符串中的字符。

public delete(int start, int end)：移除此序列的子字符串中的字符。（从start开始，包含start，到end结束，不包含end）

public StringBuffer reverse()：将此字符序列用其反转形式取代。

```java
public class Str {
 
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		StringBuffer sb = new StringBuffer("good");
		sb.append(" study");
		System.out.println(sb);
		sb.reverse();
		System.out.println(sb);
		sb.delete(1,3);
		System.out.println(sb);
		sb.insert(3, "hello");
		System.out.println(sb);
		sb.insert(0, "11");
		System.out.println(sb);
		sb.replace(1, 2, "hello");
		System.out.println(sb);
        
        
	}
 
}
```

```java
good study
yduts doog
yts doog
ytshello doog
11ytshello doog
1helloytshello doog
```

**最后转化为String ：**stringBuilder.toString()

## 使用StringBuilder逆序

```java
String s="123abc";
　　System.out.println(new StringBuilder(s).reverse().toString());
```

















