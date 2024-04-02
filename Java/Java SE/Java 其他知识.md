# Java 静态代理
	每一个代理对应一个类，将需要代理的类作为成员添加到代理类中

# Java 动态代理
	只写一个代理对象，使用不同的目标对象
	原理是把目标对象的类型信息（比如接口）作为参数，利用 Java 支持的反射来创建代理对象

## invoke 自动调用

![[Pasted image 20240329191946.png]]
	目标对象作为参数

1. 获得目标对象的接口信息和类加载器，反射得到代理类的class
2. 获得目标对象class
3. 根据代理类class，目标对象class实例化代理对象

**java.lang.reflect.InvovationHandler** 接口用于绑定目标对象需要增强的方法
**java.lang.reflect.Proxy 提供静态方法 NewProxyInstance()** 用于创建一个代理类实例

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyTest {
    public static void main(String[] args) throws Throwable {
        // 这里能清楚地看到，目标对象 RealTeacher 是一个纯粹的参数
        Teacher teacher = (Teacher)getProxy(new RealTeacher());
        // teacher 是接口 Teacher 的一个实现类，
        // 完全从多态的角度也能想到执行的实际上是 RealTeacher 的 teach() 方法
        teacher.teach();
    }
    public static Object getProxy(Object target) throws Throwable{

        return Proxy.newProxyInstance(target.getClass().getClassLoader(),
                target.getClass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        Object result = null;
                        System.out.println("Do something before");
                        result = method.invoke(target, args);
                        System.out.println("Do something after");
                        return result;
                    }
        });
    }
}


```
## 解析invok方法

Teacher teacher = (Teacher)getProxy(new RealTeacher());
	输入目标类作为参数，返回一个实现了 Teacher 接口的代理类

```java
//打印一些信息
 System.out.println(teacher.getClass().getSuperclass());//输出是class java.lang.reflect.Proxy

        Class<?>[] interfaces = teacher.getClass().getInterfaces();

        for(Class<?> i : interfaces){
            System.out.println(i);// 输出是interface Teacher
        }

        Method[] methods = teacher.getClass().getDeclaredMethods();

        for (Method method : methods) {
            System.out.println(method);
            // 输出是：
            // public final boolean com.sun.proxy.$Proxy0.equals(java.lang.Object)
            // public final java.lang.String com.sun.proxy.$Proxy0.toString()
            // public final int com.sun.proxy.$Proxy0.hashCode()
            // public final void com.sun.proxy.$Proxy0.teach() 
```

代理类 teacher 实际是名字是 $Proxy0，它父类 Proxy，实现了 Teacher 接口，实现了 teach() 方法

### teach()方法到底怎么和invoke()有关联的呢？

是因为代理对象实例 teacher 的父类是 Proxy，由 Proxy 去和我们实例化的匿名内部类 InvocationHandler 去关联

teacher.teach() --> $Proxy0.teach() --> super.h.invoke() --> proxy.invoke() --> invocationHandler.invoke()

### invoke方法中的proxy，method

实际上 proxy 参数就是代指反射创建的代理类 $Proxy0，之所以不用 this，因为 this 代表的是 InvocationHandler() 这个匿名内部类的实例。

method 用于绑定的目标类的方法



