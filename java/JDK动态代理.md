# JDK动态代理

## 动态Proxy是这样的一种类:



它是在运行生成的类，在生成时你必须提供一组Interface给它，然后该class就宣称它实现了这些interface。你可以把该class的实例当作这些interface中的任何一个来用。当然，这个Dynamic Proxy其实就是一个Proxy，它不会替你作实质性的工作，在生成它的实例时你必须提供一个handler，由它接管实际的工作。



在使用动态代理类时，我们必须实现InvocationHandler接口

## 步骤：

### 1、定义抽象角色



public interface Subject {



public void Request();



}



 



### 2、定义真实角色



public class RealSubject implements Subject {



@Override



public void Request() {



// TODO Auto-generated method stub



System.out.println("RealSubject");



}



}



 



### 3、定义代理角色



public class DynamicSubject implements InvocationHandler {



private Object sub;



public DynamicSubject(Object obj){



this.sub = obj;



}



@Override



public Object invoke(Object proxy, Method method, Object[] args)



throws Throwable {



// TODO Auto-generated method stub



System.out.println("Method:"+ method + ",Args:" + args);



method.invoke(sub, args);



return null;



}



}



 



### 4、通过Proxy.newProxyInstance构建代理对象



RealSubject realSub = new RealSubject();



InvocationHandler handler = new DynamicSubject(realSub);



Class<?> classType = handler.getClass();



Subject sub = (Subject)Proxy.newProxyInstance(classType.getClassLoader(),



realSub.getClass().getInterfaces(), handler);



System.out.println(sub.getClass());        



 



### 5、通过调用代理对象的方法去调用真实角色的方法。



sub.Request();



输出：



class $Proxy0 新建的代理对象，它实现指定的接口



Method:public abstract void DynamicProxy.Subject.Request(),Args:null



RealSubject 调用的真实对象的方法