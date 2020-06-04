# **Java基础**

## String能被继承吗？为什么？

不可以，因为String类有final修饰符，而final修饰的类是不能被继承的，实现细节不允许改变。平常我们定义的String str=”abc”(直接赋一个字面量);其实和String str=new String(“abc”)(通过构造器构造)还是有差异的。

## String， Stringbuffer， StringBuilder 的区别

String 字符串常量(final修饰，不可被继承)，String是常量，当创建之后即不能更改。(可以通过StringBuffer和StringBuilder创建String对象(常用的两个字符串操作类)。) 

==StringBuffer 字符串变量（线程安全）,==其也是final类别的，不允许被继承，其中的绝大多数方法都进行了同步处理，包括常用的Append方法也做了同步处理(synchronized修饰)。其自jdk1.0起就已经出现。其toString方法会进行对象缓存，以减少元素复制开销。 

## ArrayList 和 LinkedList 有什么区别

ArrayList和LinkedList都实现了List接口，有以下的不同点： 

1、ArrayList是基于索引的数据接口，它的底层是数组。它可以以O(1)时间复杂度对元素进行随机访问。与此对应，LinkedList是以元素列表的形式存储它的数据，每一个元素都和它的前一个和后一个元素链接在一起，在这种情况下，查找某个元素的时间复杂度是O(n)。 

2、相对于ArrayList，LinkedList的插入，添加，删除操作速度更快，因为当元素被添加到集合任意位置的时候，不需要像数组那样重新计算大小或者是更新索引。 

3、LinkedList比ArrayList更占内存，因为LinkedList为每一个节点存储了两个引用，一个指向前一个元素，一个指向下一个元素。 

## == 和 equals 的区别是什么

== 解读: 

对于基本类型和引用类型 == 的作用效果是不同的，如下所示： 

基本类型：比较的是值是否相同； 

引用类型：比较的是引用是否相同； 

实例: 



​        String x = "string"; 

​        String y = "string"; 

​        String z = new String("string"); 

​        System.out.println(x==y); // true,引用相同 

​        System.out.println(x==z); // false,==:string比较引用,开辟了新的堆内存空间,所以false 

​        System.out.println(x.equals(y)); // true,equals:string:比较值,相同 

​        System.out.println(x.equals(z)); // true,equals:string比较值,相同 



equals 解读: 

equals 本质上就是 ==，只不过 String 和 Integer 等重写了 equals 方法，把它变成了值比较。看下面的代码就明白了。 

首先来看默认情况下 equals 比较一个(有相同值的对象)，代码如下： 



public class Cat { 

​    private  String name; 

​    public Cat(String name){ 

​        this.name = name; 

​    } 

​    public String getName() { 

​        return name; 

​    } 

​    public void setName(String name) { 

​        this.name = name; 

​    } 

​    public static void main(String[] args) { 

​        Cat c1 = new Cat("cat1");//c1是Cat的实例化对象,c2同理 

​        Cat c2 = new Cat("cat2"); 

​           String s1 = new String("隔壁老王"); 

​        String s2 = new String("隔壁老王"); 

​        System.out.println(c1.equals(c2));//false,equals在比较的类对象的时候比较的是引用 

​        System.out.println(s1.equals(s2)); //true,而在比较string的时候,因为重写了equals方法,和基本数据类型一样,比较的是值,所以为true 



} 



总结 ：== 对于基本类型来说是值比较(不难理解,八种基本数据类型是可以有确定值的)，对于引用类型来说是比较的是引用(数组、类、接口没有确定值)；而 equals 默认情况下是引用比较，只是很多类重新了 equals 方法，比如 String、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等。 

## **如何实现数组和 List 之间的转换**

List转换成为数组：调用ArrayList的toArray方法。

数组转换成为List：调用Arrays的asList方法。

## **Java中的hashCode和equals**

### **1、关于hashCode**

1. hashCode的存在主要是用于查找的快捷性，如Hashtable，HashMap等，hashCode是用来在散列存储结构中确定对象的存储地址的

2. 如果两个对象相同，就是适用于equals(java.lang.Object) 方法，那么这两个对象的hashCode一定要相同

3. 如果对象的equals方法被重写，那么对象的hashCode也尽量重写，并且产生hashCode使用的对象，一定要和equals方法中使用的一致，否则就会违反上面提到的第2点

4. 两个对象的hashCode相同，并不一定表示两个对象就相同，也就是不一定适用于equals(java.lang.Object) 方法，只能够说明这两个对象在散列存储结构中，如Hashtable，他们“存放在同一个篮子里“

   **再归纳一下就是hashCode是用于查找使用的，而equals是用于比较两个对象的是否相等的。**

### **2、关于equals**

#### 1.equals和==

==用于比较引用和比较基本数据类型时具有不同的功能： 



比较基本数据类型，如果两个值相同，则结果为true 



而在比较引用时，如果引用指向内存中的同一对象，结果为true;



equals()作为方法，实现对象的比较。由于==运算符不允许我们进行覆盖，也就是说它限制了我们的表达。因此我们复写equals()方法，达到比较对象内容是否相同的目的。而这些通过==运算符是做不到的。

#### 2.object类的equals()方法的比较规则为

如果两个对象的类型一致，并且内容一致，则返回true,这些类有： 



java.io.file,java.util.Date,java.lang.string,包装类（Integer,Double等） 



String s1=new String("abc"); 



String s2=new String("abc"); 



System.out.println(s1==s2); 



System.out.println(s1.equals(s2)); 



运行结果为false true