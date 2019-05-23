# Java创建对象

### 使用new关键字：

这是我们最常见的也是最简单的创建对象的方式，通过这种方式我们还可以调用任意的够赞函数（无参的和有参的）。比如：Student student = new Student();使用new关键字：这是我们最常见的也是最简单的创建对象的方式，通过这种方式我们还可以调用任意的够赞函数（无参的和有参的）。比如：Student student = new Student();

### 使用Class类的newInstance方法：

我们也可以使用Class类的newInstance方法创建对象，这个newInstance方法调用无参的构造器创建对象，如：Student student2 = (Student)Class.forName("根路径.Student").newInstance();　或者：Student stu = Student.class.newInstance();

### 使用Constructor类的newInstance方法：

本方法和Class类的newInstance方法很像，java.lang.relect.Constructor类里也有一个newInstance方法可以创建对象。我们可以通过这个newInstance方法调用有参数的和私有的构造函数。如： Constructor<Student> constructor = Student.class.getInstance(); Student stu = constructor.newInstance();　这两种newInstance的方法就是大家所说的反射，事实上Class的newInstance方法内部调用Constructor的newInstance方法。这也是众多框架Spring、Hibernate、Struts等使用后者的原因。

### 使用Clone的方法：

无论何时我们调用一个对象的clone方法，JVM就会创建一个新的对象，将前面的对象的内容全部拷贝进去，用clone方法创建对象并不会调用任何构造函数。要使用clone方法，我们必须先实现Cloneable接口并实现其定义的clone方法。如：Student stu2 = <Student>stu.clone();这也是原型模式的应用。

### 使用反序列化：

当我们序列化和反序列化一个对象，JVM会给我们创建一个单独的对象，在反序列化时，JVM创建对象并不会调用任何构造函数。为了反序列化一个对象，我们需要让我们的类实现Serializable接口。如：ObjectInputStream in = new ObjectInputStream (new FileInputStream("data.obj")); Student stu3 = (Student)in.readObject();