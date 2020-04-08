

### collect 流转换

> 将流转换为list。还有toSet()，toMap()等。及早求值。

```java
 List<Student> studentList = Stream.of(new Student("路飞", 22, 175),
                new Student("红发", 40, 180),
                new Student("白胡子", 50, 185)).collect(Collectors.toList());
```

### filter 过滤筛选的作用

```java
public class TestCase {
    public static void main(String[] args) {
        List<Student> students = new ArrayList<>(3);
        students.add(new Student("路飞", 22, 175));
        students.add(new Student("红发", 40, 180));
        students.add(new Student("白胡子", 50, 185));

        List<Student> list = students.stream()
            .filter(stu -> stu.getStature() < 180)
            .collect(Collectors.toList());
        System.out.println(list);
    }
}
//输出结果
//[Student{name='路飞', age=22, stature=175, specialities=null}]
```

### map 转换功能，惰性求值

```java
public class TestCase {
    public static void main(String[] args) {
        List<Student> students = new ArrayList<>(3);
        students.add(new Student("路飞", 22, 175));
        students.add(new Student("红发", 40, 180));
        students.add(new Student("白胡子", 50, 185));

        List<String> names = students.stream().map(student -> student.getName())
                .collect(Collectors.toList());
        System.out.println(names);
    }
}
//输出结果
//[路飞, 红发, 白胡子]
```

### flatMap 将多个Stream合并为一个Stream。惰性求值

```java
public class TestCase {        
    public static void main(String[] args) {        
        List<Student> students = new ArrayList<>(3);
        students.add(new Student("路飞", 22, 175));
        students.add(new Student("红发", 40, 180));
        students.add(new Student("白胡子", 50, 185));

        List<Student> studentList = Stream.of(students,
                asList(new Student("艾斯", 25, 183),
                        new Student("雷利", 48, 176)))
                .flatMap(students1 -> students1.stream()).collect(Collectors.toList());
        System.out.println(studentList);
    }
}
//输出结果
//[Student{name='路飞', age=22, stature=175, specialities=null}, 
//Student{name='红发', age=40, stature=180, specialities=null}, 
//Student{name='白胡子', age=50, stature=185, specialities=null}, 
//Student{name='艾斯', age=25, stature=183, specialities=null},
//Student{name='雷利', age=48, stature=176, specialities=null}]
```

### max和min

```java
public class TestCase {
    public static void main(String[] args) {
        List<Student> students = new ArrayList<>(3);
        students.add(new Student("路飞", 22, 175));
        students.add(new Student("红发", 40, 180));
        students.add(new Student("白胡子", 50, 185));

        Optional<Student> max = students.stream()
            .max(Comparator.comparing(stu -> stu.getAge()));
        Optional<Student> min = students.stream()
            .min(Comparator.comparing(stu -> stu.getAge()));
        //判断是否有值
        if (max.isPresent()) {
            System.out.println(max.get());
        }
        if (min.isPresent()) {
            System.out.println(min.get());
        }
    }
}
//输出结果
//Student{name='白胡子', age=50, stature=185, specialities=null}
//Student{name='路飞', age=22, stature=175, specialities=null}
```

> max、min接收一个Comparator（例子中使用java8自带的静态函数，只需要传进需要比较值即可。）并且返回一个Optional对象，该对象是java8新增的类，专门为了防止null引发的空指针异常。可以使用max.isPresent()判断是否有值；可以使用max.orElse(new Student())，当值为null时就使用给定值；也可以使用max.orElseGet(() -> new Student());这需要传入一个Supplier的lambda表达式。

### count 

统计功能，一般都是结合filter使用，因为先筛选出我们需要的再统计即可。及早求值

```java
public class TestCase {
    public static void main(String[] args) {
        List<Student> students = new ArrayList<>(3);
        students.add(new Student("路飞", 22, 175));
        students.add(new Student("红发", 40, 180));
        students.add(new Student("白胡子", 50, 185));

        long count = students.stream().filter(s1 -> s1.getAge() < 45).count();
        System.out.println("年龄小于45岁的人数是：" + count);
    }
}
//输出结果
//年龄小于45岁的人数是：2
```

### reduce

> reduce 操作可以实现从一组值中生成一个值。在上述例子中用到的 count 、 min 和 max 方
> 法，因为常用而被纳入标准库中。事实上，这些方法都是 reduce 操作。及早求值。

```
public class TestCase {
    public static void main(String[] args) {
        Integer reduce = Stream.of(1, 2, 3, 4).reduce(0, (acc, x) -> acc+ x);
        System.out.println(reduce);
    }
}
//输出结果
//10
```

