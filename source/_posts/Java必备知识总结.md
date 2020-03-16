---
title: Java必备知识总结   
categories: 后台开发   
tags:
  - java
  - 基础知识
  - 必备
---


> * 整理知识，学习笔记
> * 发布日记，杂文，所见所想
> * 撰写发布技术文稿（代码支持）
> * 撰写发布学术论文（LaTeX 公式支持）

## 反射

`反射 (Reflection)` 是 Java 的特征之一，它允许运行中的 Java 程序获取自身的信息，并且可以操作类或对象的内部属性。   

>简而言之，通过反射，我们可以在运行时获得程序或程序集中每一个类型的成员和成员的信息。程序中一般的对象的类型都是在编译期就确定下来的，而 Java 反射机制可以动态地创建对象并调用其属性，这样的对象的类型在编译期是未知的。所以我们可以通过反射机制直接创建对象，即使这个对象的类型在编译期是未知的。   

反射的核心是 `JVM` 在运行时才动态加载类或调用方法/访问属性，它不需要事先（写代码的时候或编译期）知道运行对象是谁。   

* Java 反射主要提供以下功能： 重点：**是运行时而不是编译时**  
1.在运行时判断任意一个对象所属的类；   
2.在运行时构造任意一个类的对象；   
3.在运行时判断任意一个类所具有的成员变量和方法（通过反射甚至可以调用private方法）；   
4.在运行时调用任意一个对象的方法

### 反射的基本运用
#### 1、获得 Class 对象

- 调用运行时类本身的 .class 属性


```
Class clazz1 = Person.class;
System.out.println(clazz1.getName());
```
- 通过运行时类的对象获取 getClass();

```
Person p = new Person();
Class clazz3 = p.getClass();
System.out.println(clazz3.getName());
```
- 使用 Class 类的 forName 静态方法

```
try {
    Class<?> aClass = Class.forName("com.lijian.Thread.Foo");
    System.out.println(aClass.getName());
} catch (ClassNotFoundException e) {
    System.out.println("该类没有找到");
}
```
- （了解）通过类的加载器 ClassLoader

```
ClassLoader classLoader = this.getClass().getClassLoader();
    try {
        Class<?> aClass = classLoader.loadClass("com.lijian.Thread.Foo");
        System.out.println(aClass.getName());
    } catch (ClassNotFoundException e) {
        System.out.println("该类没有找到");
}
```
#### 2、判断是否为某个类的实例
一般地，我们用 instanceof 关键字来判断是否为某个类的实例。同时我们也可以借助反射中 Class 对象的 isInstance() 方法来判断是否为某个类的实例，它是一个 native 方法：
```
public native boolean isInstance(Object obj);
```

####  3、创建实例
通过反射来生成对象主要有两种方式。

* 使用Class对象的newInstance()方法来创建Class对象对应类的实例。

```
Class<?> c = String.class;
Object str = c.newInstance();
```
* 先通过Class对象获取指定的Constructor对象，再调用Constructor对象的newInstance()方法来创建实例。这种方法可以用指定的构造器构造类的实例。

```
//获取String所对应的Class对象
Class<?> c = String.class;
//获取String类带一个String参数的构造器
Constructor constructor = c.getConstructor(String.class);
//根据构造器创建实例
Object obj = constructor.newInstance("23333");
System.out.println(obj);
```
#### 4、获取方法、属性名
- 获取某个Class对象的方法集合，主要有以下几个方法：   
getDeclaredMethods 方法返回类或接口声明的所有方法，包括公共、保护、默认（包）访问和私有方法，但不包括继承的方法。

```
public Method[] getDeclaredMethods() throws SecurityException
public Field[] getDeclaredFields() throws SecurityException
```
- getMethods 方法返回某个类的所有公用（public）方法，包括其继承类的公用方法。

```
public Method[] getMethods() throws SecurityException
public Field[] getFields() throws SecurityException
```
- getMethod 方法返回一个特定的方法，其中第一个参数为方法名称，后面的参数为方法的参数对应Class的对象。

```
public Method getMethod(String name, Class<?>... parameterTypes)
public Field getField(String name)
```

>**获取方法的汇总代码**

```
public class Reflex {


    public static void main(String[] args) {
        Class<MethodClass> methodClassClass = MethodClass.class;

        try {
            // 区别重载 获取方法
            Method add = methodClassClass.getMethod("add", int.class, int.class);
            System.out.println(add.getName());

            //getMethods()方法获取的所有方法 不包括继承
            Method[] declaredMethods = methodClassClass.getDeclaredMethods();
            for (Method declaredMethod : declaredMethods) {
                System.out.println(declaredMethod.getName());
            }
            System.out.println("getMethods获取的方法：");

            //getMethods()方法获取的所有方法 包括继承
            Method[] methods = methodClassClass.getMethods();
            for (Method method : methods) {
                System.out.println(method.getName());
            }

        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        }
    }

}

public class MethodClass {
        public final int fuck = 3;
        public int add(int a,int b) {
            return a+b;
        }
        public int sub(int a,int b) {
            return a+b;
        }
}

```
#### 5、获取构造器信息
获取类构造器的用法与上述获取方法的用法类似。主要是通过Class类的getConstructor方法得到Constructor类的一个实例，而Constructor类有一个newInstance方法可以创建一个对象实例:
```
public T newInstance(Object ... initargs)
```
此方法可以根据传入的参数来调用对应的Constructor创建对象实例。
#### 6、获取类的成员变量（字段）信息
主要是这几个方法，在此不再赘述：
```
getFiled：访问公有的成员变量
getDeclaredField：所有已声明的成员变量，但不能得到其父类的成员变量
getFileds 和 getDeclaredFields 方法用法同上（参照 Method）。
```
#### 7、调用方法
当我们从类中获取了一个方法后，我们就可以用 invoke() 方法来调用这个方法。invoke 方法的原型为:
```
public Object invoke(Object obj, Object... args)
        throws IllegalAccessException, IllegalArgumentException,
           InvocationTargetException
```
>**调用方法的汇总代码**

```
public static void main(String[] args) throws IllegalAccessException, InstantiationException {
        Class<?> methodClassClass = MethodClass.class;
        //创建methodClass的实例
        Object obj = methodClassClass.newInstance();
        try {
            // 区别重载 获取方法
            Method add = methodClassClass.getMethod("add", int.class, int.class);
            //调用method对应的方法 => add(1,4)
            Object result = add.invoke(obj, 1, 2);
            System.out.println(result);
        } catch (NoSuchMethodException | InvocationTargetException e) {
            e.printStackTrace();
        }
    }
```

```
public class Reflex {


    public static void main(String[] args) throws IllegalAccessException, InstantiationException {
        Class<?> methodClassClass = MethodClass.class;
        Object o = methodClassClass.newInstance();
        try {
            Field field = methodClassClass.getDeclaredField("fuck");
            Method method = o.getClass().getMethod("get" + getMethodName(field.getName()));
            Object invoke = method.invoke(o);
            System.out.println(field.getName() + "=" + (int) invoke);
        } catch (Exception  e) {
            e.printStackTrace();
        }
    }
    private static String getMethodName(String fildeName) throws Exception{
                 byte[] items = fildeName.getBytes();
                 items[0] = (byte) ((char) items[0] - 'a' + 'A');
                 return new String(items);
    }
}
```

## 常见的五种单例模式
### 1、饿汉式 (线程安全，调用效率高，但是不能延时加载)
```
public class Singleton1 {
   /*
    * 饿汉式是在声明的时候就已经初始化Singleton1,确保了对象的唯一性
    *
    * 声明的时候就初始化对象会浪费不必要的资源
    **/
    private static Singleton1 instance = new Singleton1();
    
    private Singleton1() {
    }
    
    // 通过静态方法或枚举返回单例对象
    public Singleton1 getInstance() {
        return instance;
    }
}
```
>一上来就把单例对象给创建出来了，要用的时候直接返回即可，这种可以说是单例模式最简单的一种实现方式，但是问题也比较明显，单例在还没有使用的时候，初始化已经完成了，也就是说，如果程序从有到尾都没有使用这个单例的话，单例的对象还是会创建，这就造成了不必要资源浪费，多以不推荐使用这种方式。
### 2、懒汉式（线程安全，调用效率不高，但是能延迟加载）
```
public class SingletonDemo2 {
     
    //类初始化时，不初始化这个对象(延时加载，真正用的时候再创建)
    private static SingletonDemo2 instance;
     
    //构造器私有化
    private SingletonDemo2(){}
     
    //方法同步，调用效率低
    public static synchronized SingletonDemo2 getInstance(){
        if(instance==null){
            instance=new SingletonDemo2();
        }
        return instance;
    }
}
```
### 3、Double CheckLock 双重锁判断机制 DCL 也就是（由于JVM底层模型原因，偶尔会出问题，不建议使用）
```
public class Singleton3 {
    private static Singleton3 instance;
    
    private Singleton3() {}
    
    /**
     * 两次判空，第一次判空是为了不必要的同步，第二次判空为了在instance 为 null 的情况下创建实例
     * 既保证了线程安全且单例对象初始化后调用getInstance又不会进行同步锁判断
     * 
     * 优点:资源利用率高,效率高
     * 缺点:第一次加载稍慢，由于java处理器允许乱序执行，偶尔会失败
     *
     * @return
     */
    public static Singleton3 getInstance() {
        if (instance == null) {
            synchronized (Singleton3.class) {
                if (instance == null) {
                    instance = new Singleton3();
                }
            }
        }
        return instance;
    }
}
```
### 4、★ 静态内部类实现模式 (线程安全，调用效率高，可以延迟加载)
```
public class Singleton4 {
   /*
    * 当第一次加载Singleton类时并不会初始化SINGLRTON，只有第一次调用getInstance方法的时候才会初始化 SINGLETON
    * 第一次调用getInstance 方法的时候虚拟机才会加载SingletonHoder类，这种方式不仅能够保证线程安全，也能够保证对象的唯一
    * 还延迟了单例的实例化，所有推荐使用这种方式
    **/
    private Singleton4() {}
    
    public Singleton4 getInstance() {
        return SingletonHolder.SINGLETON;
    }
    
    private static class SingletonHolder {
        private static final Singleton4 SINGLETON = new Singleton4();
    }
}
```
### 5、枚举类（线程安全，调用效率高，不能延时加载，可以天然的防止反射和反序列化调用）

