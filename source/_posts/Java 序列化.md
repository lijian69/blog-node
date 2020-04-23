---

title: Java 序列化 
categories: 后台开发   
tags:

  - java
  - 基础知识
  - 必备

---

### Java 序列化

#### 什么是序列化？为什么需要序列化

- 序列化：将 Java 对象转换成字节流的过程。
- 反序列化：将字节流转换成 Java 对象的过程。

当 Java 对象需要在`网络上传输` 或者 `持久化存储到文件中`时，就需要对 Java 对象进行序列化处理。

**注意事项：**

1. 某个类可以被序列化，则其子类也可以被序列化

2. 声明为 static 和 transient 的成员变量，不能被序列化。static 成员变量是描述类级别的属性，transient 表示临时数据

3. 反序列化读取序列化对象的顺序要保持一致

#### Java 序列化方式有几种？

**答案：** 三种

##### 1. **实现 Serializable 接口(隐式序列化)**

通过实现 Serializable 接口，这种是隐式序列化(不需要手动)，这种是最简单的序列化方式，会自动序列化所有非 static 和 transient 关键字修饰的成员变量。

>  Java 的序列化机制是通过在运行时判断类的 serialVersionUID 来验证版本一致性的。在进行反序列化时，JVM 会把传来的字节流中的 serialVersionUID 与本地相应实体（类）的 serialVersionUID 进行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的异常。

```java
class Student implements Serializable{
	private String name;
	private int age;
	public static int QQ = 1234;
	private transient String address = "CHINA";
	
	Student(String name, int age ){
		this.name = name;
		this.age = age;
	}
	public String toString() {
		return "name: " + name + "\n"
				+"age: " + age + "\n"
				+"QQ: " + QQ + "\n" 
				+ "address: " + address;
				
	}
	public void SetAge(int age) {
		this.age = age;
	}
}
public class SerializableDemo {
	public static void main(String[] args) throws IOException, ClassNotFoundException {
		//创建可序列化对象
		System.out.println("原来的对象：");
		Student stu = new Student("Ming", 16);
		System.out.println(stu);
		//创建序列化输出流
		ByteArrayOutputStream buff = new ByteArrayOutputStream();
		ObjectOutputStream out = new ObjectOutputStream(buff);
		//将序列化对象存入缓冲区
		out.writeObject(stu);
		//修改相关值
		Student.QQ = 6666; // 发现打印结果QQ的值被改变
		stu.SetAge(18);   //发现值没有被改变
		//从缓冲区取回被序列化的对象
		ObjectInputStream in = new ObjectInputStream(new ByteArrayInputStream(buff.toByteArray()));
		Student newStu = (Student) in.readObject();
		System.out.println("序列化后取出的对象：");
		System.out.println(newStu);
	}
}
```

##### 2.实现 Externalizable 接口。(显式序列化)**

Externalizable 接口继承自 Serializable, 我们在实现该接口时，`必须实现writeExternal()和readExternal()方法`，而且**只能通过手动进行序列化**，并且两个方法是自动调用的，因此，这个序列化过程是可控的，可以自己选择哪些部分序列化。

```java
public class Blip implements Externalizable{
	private int i ;
	private String s;
	public Blip() {}
	public Blip(String x, int a) {
		System.out.println("Blip (String x, int a)");
		s = x;
		i = a;
	}
	public String toString() {
		return s+i;
	}
	@Override
	public void writeExternal(ObjectOutput out) throws IOException {
		// TODO Auto-generated method stub
		System.out.println("Blip.writeExternal");
		out.writeObject(s);
		out.writeInt(i);
//		
	}
	@Override
	public void readExternal(ObjectInput in) throws IOException, ClassNotFoundException {
		// TODO Auto-generated method stub
		System.out.println("Blip.readExternal");
		s = (String)in.readObject();
		i = in.readInt();
	}
	public static void main(String[] args) throws FileNotFoundException, IOException, ClassNotFoundException {
		System.out.println("Constructing objects");
		Blip b = new Blip("A Stirng", 47);
		System.out.println(b);
		ObjectOutputStream o = new ObjectOutputStream(new FileOutputStream("F://Demo//file1.txt"));
		System.out.println("保存对象");
		o.writeObject(b);
		o.close();
		//获得对象
		System.out.println("获取对象");
		ObjectInputStream in = new ObjectInputStream(new FileInputStream("F://Demo//file1.txt"));
		System.out.println("Recovering b");
		b = (Blip)in.readObject();
		System.out.println(b);
	}
 
}
```

##### 3.**实现 Serializable 接口+添加 writeObject() 和 readObject() 方法。(显+隐序列化)**

如果想将方式一和方式二的优点都用到的话，可以采用方式三， 先实现 Serializable 接口，并且添加writeObject()和readObject()方法。`注意这里是添加，不是重写或者覆盖。`但是添加的这两个方法必须有相应的格式。

1. 方法必须要被 private 修饰                                ----->才能被调用
2. 第一行调用默认的 defaultRead/WriteObject(); ----->隐式序列化非static和transient
3. 调用 read/writeObject() 将获得的值赋给相应的值  --->显式序列化

```java
public class SerDemo implements Serializable{
	public transient int age = 23;
	public String name ;
	public SerDemo(){
		System.out.println("默认构造器。。。");
	}
	public SerDemo(String name) {
		this.name = name;
	}
	private  void writeObject(ObjectOutputStream stream) throws IOException {
		stream.defaultWriteObject();
		stream.writeInt(age);
	}
	private void readObject(ObjectInputStream stream) throws ClassNotFoundException, IOException {
		stream.defaultReadObject();
		age = stream.readInt();
	}
	
	public String toString() {
		return "年龄" + age + "  " + name; 
	}
	public static void main(String[] args) throws IOException, ClassNotFoundException {
		SerDemo stu = new SerDemo("Ming");
		ByteArrayOutputStream bout = new ByteArrayOutputStream();
		ObjectOutputStream out = new ObjectOutputStream(bout);
		out.writeObject(stu);
		ObjectInputStream in = new ObjectInputStream(new ByteArrayInputStream(bout.toByteArray()));
		SerDemo stu1 = (SerDemo) in.readObject();
		System.out.println(stu1);
	}
}
```

