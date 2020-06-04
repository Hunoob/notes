# Java反射

## 1、获取类的Class对象

Class 类的实例表示正在运行的 Java 应用程序中的类和接口。获取类的Class对象有多种方式：



调用getClass    

Boolean var1 = true;



Class<?> classType2 = var1.getClass();



System.out.println(classType2);



输出：class java.lang.Boolean



运用.class 语法    

Class<?> classType4 = Boolean.class;



System.out.println(classType4);



输出：class java.lang.Boolean



运用static method Class.forName()    

Class<?> classType5 = Class.forName("java.lang.Boolean");



System.out.println(classType5);



输出：class java.lang.Boolean



运用primitive wrapper classes的TYPE 语法



这里返回的是原生类型，和Boolean.class返回的不同



Class<?> classType3 = Boolean.TYPE;



System.out.println(classType3);        



输出：boolean

## 2、获取类的Fields

可以通过反射机制得到某个类的某个属性，然后改变对应于这个类的某个实例的该属性值。JAVA 的Class<T>类提供了几个方法获取类的属性。



public FieldgetField(String name)    返回一个 Field 对象，它反映此 Class 对象所表示的类或接口的指定公共成员字段

public Field[] getFields()    返回一个包含某些 Field 对象的数组，这些对象反映此 Class 对象所表示的类或接口的所有可访问公共字段

public FieldgetDeclaredField(Stringname)    返回一个 Field 对象，该对象反映此 Class 对象所表示的类或接口的指定已声明字段

public Field[] getDeclaredFields()    

返回 Field 对象的一个数组，这些对象反映此 Class 对象所表示的类或接口所声明的所有字段

*可见getFields和getDeclaredFields区别：*

*getFields返回的是申明为public的属性，包括父类中定义，*

*getDeclaredFields返回的是指定类定义的所有定义的属性，不包括父类的。*