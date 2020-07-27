# Java 基础

#### 1. 访问修饰符

|           | 同一个类内 | 同一个包内 | 不同包内子类 | 不同包非子类 |
| --------- | ---------- | ---------- | ------------ | ------------ |
| public    | v          | v          | v            | v            |
| protected | v          | v          | v            |              |
| default   | v          | v          |              |              |
| private   | v          |            |              |              |


#### 2. 重载与重写的区别
* 重写 Override：发生在父类子类中，方法名和参数列表必须相同，返回值范围小于等于父类，抛出异常范围小于等于父类，访问修饰符范围大于等于父类；如果父类是访问修饰符是 private 子类不能重写父类。
* 重载 overload：发生在同一个类中，方法名必须相同，参数列表不同，返回值可以不同、访问修饰符可以不同。

#### 3. String 为什么是不可变的？
JDK 源码中 String 类保存字符串的数组是 final: 
`private final byte[] value;`
不可变对象：创建后不能修改引用，所有的字段都用 private 修饰，构造函数不会返回this引用。

* 为了安全性考虑，且多线程安全。
* 为了实现 String 的 Hashcode 的不可变性。
* 为了实现字符串池，节约 heap 空间。

#### 4. String、StringBuffer、StringBuilder 区别是什么？

String 是字符串常量，不可变对象；StringBuffer 和 StringBuilder 是字符串变量。

可变性：String 是不可变对象，StringBuffer 和 StringBuilder 是可变对象。
线程安全性：String 和 StringBuffer 是线程安全的，因为String 是不可变对象，StringBuffer 用 synchornized 修饰。
性能：对 String 的任何改变都会重新生成 String，然后将引用指向新的 String，频繁操作字符串常量影响 JVM 性能；StringBuilder 比 StringBuffer 性能高 10%～15%，但存在线程不安全问题。

操作少量字符串 --> String
频繁操作大量字符串 --> StringBuilder / StringBuffer 
多线程操作 --> StringBuffer
单线程操作 --> StringBuilder



#### 5. 字符串常量池
字符串常量会放到一个常量池中，当创建字符串时，JVM 会查找在常量池中有没有，如果有，把当前引用直接指向常量池中的字符串。因为 String 是不可变对象，所以可以存在多个引用共享一个字符串。为了提高性能减小空间消耗。

#### 6. 自动装箱与拆箱

装箱：基本类型用他们对用的类包装起来 int, float, double... --> Integer, Float, Double
拆箱：将包装类转化为基本类型 Integer, Float --> int float

#### 7. == 与 equals() 方法

== 基本数据类型是比较值，引用类型比较的是内存地址。
如果没有重写 equals() 方法，equals 和 == 一样。
重写 equals 方法一般是比较两个对象的内容是否相等。

String 重写了 equals，比较对象的值。

一般重写 equals 方法，一定要重写 hashcode 方法。

#### 8. final 关键字

final 修饰基本数据类型： 一旦创建不能改变。
final 修饰引用类型：引用所指向的对象不能改变，但对象中的内容可以改变（除非也是 final）。
final 修饰类：不能被继承。
final 修饰方法：方法不能被重写

#### 9. 抽象类 与 接口的区别

* 接口方法默认修饰是 public abstract，字段默认修饰是 public static final
* 接口中 static 修饰方法时，必须有实现体。该方法属于接口。
* 接口中 default 方法，必须有实现体，表示默认方法，实现类中可以重写也可以不重写。
* 抽象类中可以有非抽象方法，接口中方法都是抽象的abstract 默认的default
* 接口中不可以有构造方法，抽象类中可以有构造方法
* 一个类只能继承一个父类，可以实现多个方法

* 都是抽象层
* 都不能被实例化
* 都包含抽象方法

| 接口                     | 抽象类                         |
| ------------------------ | ------------------------------ |
| 默认public不允许其他     |                                |
| 成员方法public abstract  | 成员方法可以abstract也可以实现 |
| 字段 public/static/final | 都行                           |
| 类可以实现多个           | 只能继承一个                   |



#### 10. transient 关键字

中文短暂的，实现了 Serializable 接口的类中，表示字段不能被序列化。

#### 11. static 关键字
static 修饰成员方法和字段，是属于类的，直接通过类名访问，在内存中只有一份拷贝。也能通过实例对象访问（不建议）。

static 代码块：在加载类的时候被执行，用于优化执行效率，一些固定的内容每次实例化都要执行的代码放到 static 块中。一个类中可以有多个，按顺序执行。


#### 12. Java泛型机制

定义类型，可以将类型通过参数传递。

泛型类 `public class Test<T> { }`
泛型方法 `public <T> void testFunc(T a) { }`
泛型接口 `public interface Interface<T> { }`

无限定通配符<?> `Collection<?>`
`<? extends T>`T 类的子类都可以
`<? super E>` E 的父类都可以

#### 13. 反射
java 运行时，可以拿到整个类对象，new 实例对象，访问字段和方法。
动态获取类对象，动态调用类方法。
对象在编译期是未知的。

拿到类对象的 3 种方法
```java
Class c1 = Code.class; // 通过获取类的静态成员变量class得到
Class c2 = code1.getClass();  // 通过实例对象的getClass()方法获得
Class c3 = Class.forName("com.trigl.reflect.Code");  // 调用forName方法，通过一个类的全量限定名获得
```

Method
```java
public Method getDeclaredMethod(String name, Class<?>... parameterTypes) // 得到该类所有的方法，不包括父类的
public Method getMethod(String name, Class<?>... parameterTypes) // 得到该类所有的public方法，包括父类的
```

Field
```java
public Field getDeclaredField(String name) // 获得该类自身声明的所有变量，不包括其父类的变量
public Field getField(String name) // 获得该类自所有的public成员变量，包括其父类变量

Constructor
​```java
public Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes) //  获得该类所有的构造器，不包括其父类的构造器
public Constructor<T> getConstructor(Class<?>... parameterTypes) // 获得该类所以public构造器，包括父类
```

#### 14. 值传递与引用传递
基本数据类型(boolean, byte, int, long, float, double和包装类)是传值。
引用类型(class, interface, array)是传的是引用，对象的内存地址。

#### 15. 注解原理
注解是放在代码中的元数据(描述代码的数据), 可以被解析工具或编译器解析。
本质是实现了 Annotation 接口的接口。
可以在运行时通过反射得到注解的数据。
主要用于编译器检查，替代xml配置

#### 16. final关键字的作用，能修饰抽象类吗
不能，抽象类目的就是子类继承。

#### 17. 内部类是什么（inner class，nested class）
一个类定义在另一个类或者方法的内部，分为：成员内部类、局部内部类、匿名内部类、静态内部类

成员内部类：定义在类中。
1) 内部类可以无条件访问外部类中的字段方法。
2) 外部类必new一个内部类才能访问内部类方法和字段。
3) 内部类依附于外部类存在 
4) 4 4种访问控制都有。

局部内部类：定义在方法或一个作用域里。访问权限仅在方法或作用域中。

匿名内部类：new出来一个对象，重写方法。
```java
btn.setOnClickListener(new onClickListener(){
    @Override
    public void onClick(...) {...}
});
```
静态内部类：static修饰的成员内部类。独立与外部类。



#### 18. Java中的多态是怎么体现的

多态体现的 3 个条件：
1) 继承关系 
2) 子类重写父类 
3) 父类引用指向子类对象

多态: 引用变量所指向的具体字段和发出的方法调用，在编译时并不确定，而是在程序运行期间才确定。

字段跟父类走。
成员方法跟子类，动态绑定。
静态字段/方法跟父类。

#### 19. Lambda 表达式
java 8 开始的新功能。
用于简化匿名内部类。
但不能简化所有的匿名内部类，只能取代函数接口。
函数接口 functional interface: 内部只有一个抽象方法的接口。
编译器能够自动推断出 lambda表达式 的类型。

`(args) -> {body}`

```java
new Thread(
    () -> {  // 推断出 Runnable 对象，实现 run 方法
        System.out.print("thread ");
        System.out.println("run...");
    }
).start();

List<String> list = Arrays.asList("a", "b", "c");
Collection.sort(list, (s1, s2) -> {  // 推断出 Compareable 对象，实现 campare 方法
    if (s1 == null) return -1;
    if (s2 == null) return 1;
    return s1.length() - s2.length();
})

list.forEach(n -> System.out.println(n));  // 

// Lambda表达式的书写形式
Runnable run = () -> System.out.println("Hello World");// 1 无参函数
ActionListener listener = event -> System.out.println("button clicked");// 2 有参函数
Runnable multiLine = () -> {// 3 代码块
    System.out.print("Hello");
    System.out.println(" Hoolee");
};
BinaryOperator<Long> add = (Long x, Long y) -> x + y; // 4 类型推断
BinaryOperator<Long> addImplicit = (x, y) -> x + y; // 5 类型推断
```

自定义函数接口
写一个只有一个抽象方法的接口。
```java
@FunctionalInterface
public interface MyInterface<T> {
    void foo(T t);
}
```

