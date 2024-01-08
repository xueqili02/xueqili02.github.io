---
title: java
date: 2024-01-09 00:30:46
---

# Java笔记 (Java Note)

@Copyright By Li Xueqi. 2023/07/27.

- [Java笔记 (Java Note)](#java笔记-java-note)
  - [Java基础](#java基础)
  - [面向对象](#面向对象)
    - [（1）基础](#1基础)
    - [（2）进阶](#2进阶)
    - [（3）高级](#3高级)
  - [异常](#异常)
  - [多线程](#多线程)
    - [单例模式中懒汉式的线程安全问题](#单例模式中懒汉式的线程安全问题)
    - [dead lock](#dead-lock)
    - [线程通信](#线程通信)
  - [String](#string)
  - [比较器](#比较器)
  - [集合](#集合)
    - [概述](#概述)
    - [Collection中的方法](#collection中的方法)
    - [List](#list)
    - [Set](#set)
    - [TreeSet](#treeset)
    - [Map](#map)
    - [TreeMap](#treemap)
    - [Properties](#properties)
    - [Collections工具类](#collections工具类)
    - [HashMap源码](#hashmap源码)
      - [JDK 1.7](#jdk-17)
      - [JDK 1.8与JDK 1.7的不同](#jdk-18与jdk-17的不同)
  - [泛型](#泛型)
  - [File IO](#file-io)
    - [IO流](#io流)
    - [转换流](#转换流)
    - [对象流](#对象流)
  - [反射](#反射)
    - [Class类](#class类)
    - [反射的应用](#反射的应用)
  - [动态代理](#动态代理)
  - [网络编程](#网络编程)
  - [Java多线程总结](#java多线程总结)
    - [进程与线程基本概念](#进程与线程基本概念)
    - [Java多线程入门 类和接口](#java多线程入门-类和接口)
    - [线程组和线程优先级](#线程组和线程优先级)
    - [锁与同步](#锁与同步)
      - [锁](#锁)
      - [等待/通知机制](#等待通知机制)
      - [信号量：Java基于volatile关键字自己实现了一种信号量](#信号量java基于volatile关键字自己实现了一种信号量)
      - [管道](#管道)
      - [其他通信相关知识](#其他通信相关知识)
    - [Java内存模型](#java内存模型)
    - [重排序与happens-before](#重排序与happens-before)
      - [重排序](#重排序)
      - [顺序一致性模型](#顺序一致性模型)
      - [happens-before](#happens-before)
    - [volatile](#volatile)
    - [synchronized](#synchronized)
      - [锁的分类](#锁的分类)
      - [偏向锁](#偏向锁)
      - [轻量级锁](#轻量级锁)
      - [重量级锁](#重量级锁)
      - [锁升级的流程](#锁升级的流程)
    - [CAS与原子操作](#cas与原子操作)
      - [乐观锁与悲观锁](#乐观锁与悲观锁)
      - [CAS](#cas)
      - [Java实现CAS：Unsafe类](#java实现casunsafe类)
      - [AtomicInteger](#atomicinteger)
      - [CAS实现原子操作的三大问题](#cas实现原子操作的三大问题)
    - [AQS](#aqs)
      - [AQS的数据结构](#aqs的数据结构)
      - [资源共享模式](#资源共享模式)
    - [线程池原理](#线程池原理)
      - [线程池主要的任务处理流程：](#线程池主要的任务处理流程)
      - [线程池线程复用](#线程池线程复用)
      - [四种常见的线程池](#四种常见的线程池)


## Java基础



## 面向对象

### （1）基础

1. 方法重载、重写

- 方法重载是在同一个类中，可以定义多个函数名相同、参数列表不同的函数。参数列表不同指的是数据类型不同或顺序不同。函数返回类型不同不构成重载。
- 方法重写是子类继承父类时，对同名函数的逻辑进行修改，形参不能改变。重写的方法，返回类型必须是父类返回类型或其子类，不能抛出新的异常或比被重写方法更宽的异常。static、final修饰方法不能被重写。构造器不能被重写。

2. 可变个数形参

格式为 type func(type ... name)

3. 方法值传递机制

- 基本数据类型：在栈区创建新变量
- 引用数据类型：传入地址，变量存在堆上

4. 面向对象的三大（四大）特征

封装、继承、多态、（抽象）

5. 类的成员有

变量、方法、构造器、代码块、内部类

6. 实例变量赋值过程

- 默认赋值
- 显式赋值 / 代码块
- 构造器
- 方法中赋值

7. 4种权限修饰符及作用

- private：仅能在类内部访问
- default：同一包下可以访问
- protected：同一包下可以访问，不同包下子类可以访问，但不能通过父类对象访问
- public：任意位置都可访问

### （2）进阶

1. super、this关键字

- 子类中通过super调用父类属性、方法、构造器等
- 子类构造器中可以通过super()调用父类构造器，且必须在第一行
- 类中通过this调用属性、方法、构造器等
- 子类构造器中可以通过this()调用重载的构造器
- this和super调用构造器只能出现一个，且子类最终一定会调用到父类的构造器

2. 向下转型、向上转型

- 向上转型：子类对象直接赋给父类引用

    Father f = new Son();

- 向下转型：指向子类对象的父类引用赋给子类引用，需要强制类型转换

    Son s = (Son) f;

    若Father ff = new Father();，则上面一句会报错，因为子类引用不能指向父类对象

3. Java内存结构，JVM中划分为哪些区域

堆、方法区、程序计数器、虚拟机栈、本地方法区

4. 多继承？父类哪些成员可以被继承

不允许多继承。父类属性、方法、内部类可以被继承

5. Object类，方法：equals、toString、hashCode、finalize、

- equals：默认比较引用对象的地址
- finalize：在对象内存被销毁之前执行，已过时

### （3）高级

1. static

可以修饰属性、方法、代码块、内部类，不可以修饰构造器

static变量和方法，在类加载的时候就会初始化，属于类而不属于某个对象，被所有对象共享。可以通过类名直接访问，也可通过对象访问

静态方法中不能使用非静态方法和变量，不能重写

1. final

表示常量，可以修饰属性、方法、类。

修饰属性时，对于基本数据类型，一旦初始化就不可更改；对于引用类型，初始化后不能指向其他对象。修饰方法时，方法不可以被重写。修饰类时，类不能被继承。

修饰引用类型对象时，对象内部的值可以改变，因为final限定的是对象的地址不变。

3. 单例模式

- 懒汉式：获取实例的时候才创建对象，线程不安全
- 饿汉式：声明变量的时候就创建对象，线程安全

4. 代码块，类中属性赋值的位置及过程

分为静态和非静态

静态代码块：随着类的加载而执行
非静态代码块：随着对象的创建而执行

代码块和显式赋值哪个在前就先运行哪个

5. 抽象类、抽象方法

抽象类不能直接创建对象，内部包含的抽象方法，可以在子类中实现并调用。

子类必须重写抽象类中的所有抽象方法，否则只能将子类也声明为abstract

构造器不能声明为abstract，静态方法不能声明为abstract

6. 内部类



7. 接口

接口中方法隐式指定为public abstract，变量隐式指定为public static final。接口中不能实现方法。

一个类可以实现多个接口，必须实现接口中所有的方法

与抽象类的区别：

- 抽象类中的方法可以有方法体，就是能实现方法的具体功能，但是接口中的方法不行。
- 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是 public static final 类型的。
- 接口中不能含有静态代码块以及静态方法(用 static 修饰的方法)，而抽象类是可以有静态代码块和静态方法。
- 一个类只能继承一个抽象类，而一个类却可以实现多个接口。

注：JDK 1.8 以后，接口里可以有静态方法和方法体了。

注：JDK 1.8 以后，接口允许包含具体实现的方法，该方法称为"默认方法"，默认方法使用 default 关键字修饰。

注：JDK 1.9 以后，允许将方法定义为 private，使得某些复用的代码不会把方法暴露出去。

8. 枚举类

enum。可以直接定义，也可以定义属性、构造器和方法

9. Annotation
基础注解
- @Override
- @Deprecated
- @SuppressWarnings

元注解
- @Target：表明用来修饰的结构，通过指定枚举类型ElementType来确定，包括：TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE, ANNOTATION_TYPE, PACKAGE, TYPE_PARAMETER, TYPE_USE
- @Retention：表明生命周期，通过枚举类型RetentionPolicy来确定，包括：SOURCE, CLASS, RUNTIME

自定义注解

1.   包装类

为基本数据类型提供对象功能，例如List中的add，只能传入对象

自动装箱、自动拆箱

## 异常

Java中一旦出现异常，就创建这个异常类型的对象，throw出去，外部代码可以catch到异常对象；如果没有catch到，那么异常会导致程序终止

Throwable类 -> Error, Exception

Error错误是JVM无法解决的严重问题，例如StackOverflowError, OutOfMemoryError

异常分为编译时期异常(checked异常)和运行时期异常(unchecked异常)

开发中常见的异常
- 运行时异常：ArrayIndexOutOfBoundsException, NullPointerException, ClassCastException, NumberFormatException, InputMismatchException, ArithmeticException，一般不做处理，报错修改代码
- 编译时异常：ClassNotFoundException, FileNotFoundException, IOException，必须处理，通过try-catch捕获

开发中异常处理方式的选择
- try-catch：资源一定要被释放；父类没有throws，子类必须try-catch
- throws：不适合在当前代码中解决，抛给调用方解决；方法a中调用了方法b c d等，方法b c d之间是递进关系，如果bcd中有异常，则使用throws抛出，在a中使用try-catch

自定义异常类
1. 继承现有的异常体系，通常继承于RuntimeException / Exception
2. 通常提供几个重载的构造器
3. 提供一个全局常量，声明为static final long serialVersionUID

## 多线程

程序、进程、线程概念区分

JVM中，线程共享区：方法区、堆，线程隔离区：虚拟机栈、本地方法栈、程序计数器

单核CPU和多核CPU，多核效率不一定是单核倍数，因为：
- 多核心的其他共用资源限制，例如内存、cache、register并没有扩充多倍
- 多核CPU之间的协调管理损耗。

并行和并发
- 并行parallel，指两个或多个事件在同一时刻发生。同一时刻，多条指令在多个CPU上执行
- 并发concurrency，指两个或多个事件在同一个时间段内发生。即一段时间内，有多条指令在单个CPU上快速轮换、交替执行

创建线程方式
1. 继承Thread类
   - 创建一个类继承Thread类
   - 重写父类run()方法
   - new一个对象
   - 调用start()方法，启动线程+调用run()方法

不能start两次同一个对象

还可以创建匿名子类的匿名对象

``` java
new Thread() {
    @Override
    public void run() {
        ....
    }
}.start();
```

2. 实现Runnable接口
    - 创建一个实现Runnable接口的类
    - 实现接口中的run()方法
    - 创建该类的对象
    - 将对象作为参数传递到Thread类的构造器中，创建Thread类实例。
    - 调用start()

两种方式的对比
- 共同点：
  - 启动线程，使用的都是Thread类的start()
  - 创建的线程对象都是Thread类或其子类的对象
- 不同点：1是类的继承，2是接口的实现
- 建议使用实现Runnable接口的方式
  - 好处：实现方式，避免类的单继承的局限性；更适合处理共享数据问题；实现代码和数据的分离
- 联系：Thread类也实现了Runnable接口

常用方法：
- start()
- run()
- currentThread()：获取当前执行代码对应的线程
- getName()：获取线程名
- setName(String name)：设置线程名
- sleep(long millis)：static方法，使当前线程睡眠指定millis
- yield()：主动释放CPU的执行权
- join()：在线程a中通过线程b调用join，意味着线程a进入阻塞状态，直到线程b执行结束，a才继续执行
- isAlive()：判断当前线程是否存活

过时方法：
- stop()：强行结束线程，部分资源可能无法释放，不建议使用
- suspend() / resume()：二者必须成对出现，可能造成死锁，不建议使用

线程优先级

- getPriority()：获取线程优先级
- setPriority()：设置

三个优先级常量：
- MAX_PRIORITY = 10，最高
- MIN_PRIORITY = 1，最低
- NORM_PRIORITY = 5，普通，默认情况下main的优先级
- 优先级高的“较大概率”会优先执行，并不是一定先执行

多线程的生命周期

JDk1.5之前：新建、就绪、运行、死亡、阻塞（new, runnable, blocked, waiting, timed_waiting, terminated）

线程进入阻塞状态：
- sleep()
- join()：该方法会使主线程进入等待状态，等待子线程执行完毕后才继续执行
- 失去同步锁
- wait()
- suspend()

Java解决线程安全问题：使用现成的同步机制
1. 同步代码块

```java
synchronized(同步监视器) {
    // code 操作共享数据的代码
    // 共享数据：多个线程需要操作的数据
    // 被synchronized包裹后，使得一个线程在操作这些代码的过程中，其他线程必须等待
    // 同步监视器，又叫锁。哪个线程获取锁，哪个线程就能执行
    // 同步监视器，可以使用任意类的对象充当。但是多个线程必须共用同一个同步监视器
}
```

在实现Runnable接口的方式中，同步监视器可考虑使用：this

在继承Thread类的方式中，同步监视器慎用this，可用当前类.class

2. 同步方法

如果操作共享数据的代码完整声明在了方法中，那么可将此方法声明为同步方法。

方法声明加synchronized关键字，

非静态的同步方法：默认同步监视器是this

静态的同步方法：默认同步监视器是当前类本身

synchronized好处
- 解决线程安全问题

弊端
- 实质上 多线程是串行执行的，性能低

### 单例模式中懒汉式的线程安全问题

饿汉式不存在线程安全

``` java
public class SingletonSync {
    private static volatile SingletonSync instance;

    public static SingletonSync() {
        if (instance == null) {
            synchronized(SingletonSync.class) {
                if (instance == null) {
                    instance = new SingletonSync();
                }
            }
        }
        return instance;
    }
}
```

使用volatile的原因：Java在创建对象时要经过三步，
1. 分配内存空间
2. 初始化对象
3. 设置instance指向刚刚分配的内存地址，此时instance != null

但是步骤2和3可能会在指令重排序后颠倒，此时会先指向内存地址，但是对象为null，这时有线程来获取到单例，可能会导致返回未初始化完成的对象。

### dead lock

不同线程分别占用对方需要的同步资源不释放，都在等待对方释放，形成线程的死锁

引发死锁的原因：
- 互斥条件
- 占用且等待
- 不可抢占
- 循环等待

解决方案：
- 互斥条件无法破坏
- 一次性申请所有所需资源
- 占用部分资源的线程进一步申请其他资源时，如果申请不到，就主动释放已占用的资源
- 将资源改为线性顺序。申请时先申请序号小的，避免循环等待

Lock锁解决线程安全
1. 创建Lock实例，需确保多个线程公用同一个Lock实例，考虑声明为static final
2. 执行lock()，锁定对共享资源的调用
3. 执行unlock()，释放对共享数据的锁定，考虑放到finally中

synchronized与lock对比
- synchronized不管是同步代码块还是同步方法，都要在结束{}后，释放对同步监视器的调用
- Lock通过两个方法，更灵活
- Lock作为接口，提供了多种实现类，适合更多更复杂的场景，效率更高

### 线程通信

- wait()会使当前线程进入等待状态，释放对同步监视器的调用。而sleep()不会释放
- notify()：唤醒被wait()线程中优先级最高的一个线程，若相同则随即唤醒一个
- notifyAll()：唤醒所有被wait的线程

- 这三个方法，必须在同步代码块或同步方法中
- 三个方法的调用者，必须是同步监视器
- 三个方法声明在Object类中

wait和sleep的区别
- 相同点：使线程进入阻塞状态
- 不同点
  - 声明的位置：wait在Object类中，sleep在Thread类中static
  - 使用场景：wait只能在同步代码块或同步方法；sleep可在任意位置使用
  - 使用在同步代码块或同步方法中：wait会释放同步监视器，sleep不会
  - 结束阻塞方式：wait到达指定时间或notify；sleep到达指定时间


创建多线程的方式
1. 实现Callable
    - 创建实现Callable的类
    - 实现call方法，可以有返回值
    - 创建实现Callable的类的对象
    - 将上一步的对象作为参数，创建FutureTask对象
    - 将FutureTask的对象作为参数，创建Thread对象
    - 通过get()方法获取返回值

与Runnable对比
- call可以有返回值
- call可以使用throws方式处理异常
- Callable使用泛型参数，可以指明具体的call的返回值类型
- 缺点：使用get()可能会阻塞

2. 使用线程池

好处：
- 提高程序执行效率，提前创建好线程
- 提高资源复用率，执行完的线程不会被销毁，可以执行其他任务
- 可以设置相关的参数，对线程池中的线程的使用进行管理

## String

jdk8中，声明为private final char[] value; final表示地址不可改变

jdk9开始，声明为private final byte[] value; 节省存储空间

字符串常量池在堆中（jdk8及之后，jdk6在方法区）

String的不可变性：
- 当对字符串变量重新赋值时，需要重新指定一个字符串常量的位置进行赋值，不能在原有位置修改
- 对现有字符串增加时，需要开辟新的空间
- 调用replace方法时，开辟新的空间

String的连接操作：+
1. 常量+常量：会指向新的字符串常量
2. 常量+变量或变量+变量：会创建新对象，新对象内指向字符串常量
3. 调用intern()：返回字符串常量池中字面量的地址
4. 调用concat()：都会返回一个新new的对象

String、StringBuffer、StringBuilder
- String：不可变的字符序列
- StringBuffer：可变的字符序列，线程安全，性能低于StringBuilder
- StringBuilder：可变的字符序列，线程不安全，性能高

StringBuffer：添加新字符串时，如果count超过value.length时，需要扩容。默认扩容为 原容量 * 2 + 2。若空间仍然不够，则直接扩容到新字符串的长度。最后将原有value[]的元素复制到新数组中。

如果开发中大致确定要操作的字符个数，建议使用带capacity参数的构造器

## 比较器

- 自然排序Comparable：实现Comparable接口中的compareTo(Object o)，指明如何判断大小
  - 如果返回值是正数，则当前对象大
  - 如果返回值是负数，则当前对象小
  - 如果返回值是零，则相等
- 定制排序Comparator
  - 创建一个实现了Comparator接口的实现类，重写compare(Object o1, Object o2)方法，指明要比较大小的对象的大小关系
  - 创建实现类对象，传入排序方法中，例如Arrays.sort(xxx, comparator)

## 集合
### 概述
数组存储数据的弊端：
- 数组一旦初始化，长度就不可变了
- 数组中存储数据单一。难以处理无序的、不可重复的数据
- 数组中可用方法、属性很少
- 数组删除、插入性能低

Java集合框架体系
- java.util.Collection：存储单个的数据
  - List：存储有序的、可重复的数据（动态数组）
    - ArrayList, LinkedList, Vector
  - Set：存储无序的，不可重复的数据
    - HashSet, TreeSet, LinkedHashSet
- java.util.Map：存储成对数据（kv键值对）
  - HashMap, TreeMap, LinkedHashMap, Hashtable, Properties

Set的底层是Map，可以理解为 当Map中的value都是null，只关心key的值时，就Map成为了Set。实际实现中，value都是new Object()，防止空指针异常。

### Collection中的方法

- add(E e)
- addAll(Collection c)
- size()
- isEmpty()
- contains(E e)
- containsAll(Collection c)
- clear()
- equals(Object o)
- hashCode()
- remove(Object o)
- removeAll(Collection c)
- removeIf(Predicate)
- retainAll(Collection c): retain ele in current collection and c (intersect)
- toArray()
- iterator()

数组转集合：调用Arrays.asList(T... t)

向Collection中添加元素，元素的类一定要重写equals方法

Iterator迭代器作用：遍历集合数据

```java
Iterator iterator = c.iterator();
while (iterator.hasNext()) {
    iterator.next(); // 1. 指针下移  2. 返回指针所指元素
}
```

增强型for循环遍历集合
```java
for (Object o: c) {
    System.out.println(o);
}
```

for each底层仍然是迭代器。执行过程中，是将元素地址赋值给临时变量，注意，循环中对元素的修改，可能不会导致原有集合/数组中的元素改变。e.g.
```java
String[] arr = new String[]{"A", "B", "C"};
for (String s: arr) {
    s = "D"; // arr is not changed.
}
```

### List

存储的是有序的，可以重复的数据

List常见方法：
- Collection中的方法
- 插入
  - add(int index, E e)
  - addAll(int index, Collection c)
- 查
  - get(int index)
  - subList(int fromIndex, int toIndex)
- 删
  - remove(int index)
- 改
  - set(int index, E e)

- List
  - ArrayList：线程不安全，性能高；底层是Object[]；添加、查找数据效率高，插入、删除数据效率低
  - Vector（很古老的实现类）：线程安全，性能差；底层是Object[]
  - LinkedList：底层是双向链表。插入、删除数据效率高，添加、查找数据效率低。频繁插入、删除时用这个集合

ArrayList和LinkedList区别？

ArrayList和Vector区别？

### Set

- Set
  - HashSet：底层实现HashMap，是数组+单向链表+红黑树（jdk8）
  - LinkedHashSet：是HashSet的子类，在现有结构基础上，添加了一组双向链表，用于记录添加元素的顺序，即可以按照添加元素的顺序遍历。
  - TreeSet：底层使用红黑树。可以按照添加元素的指定的属性的大小进行遍历
  
常用方法是Collection中声明的方法，没有新增

Set使用较List、Map少

使用场景：过滤重复数据

两个特性：
- 无序性：并不是随机性。无序性与添加的元素的存储位置有关，不是紧密排列的，表现为无序性
- 不可重复性：基于hashCode方法得到的哈希值和equals方法得到的结果。若hashCode()相等，则判断equals()，若为true，那么认为两个元素相等
### TreeSet

向TreeSet添加元素，必须是同一类型的，否则会报错ClassCastException；如果是引用类型，必须实现Comparable接口

TreeSet判断数据是否相同：不使用hashCode()和equals()，添加到TreeSet中元素所属的类可以不重写这两个方法。比较的标准是compareTo()和compare()，若返回值是0，则认为两个元素相等

### Map

java.util.Map：存储成对的数据，kv
- HashMap：线程不安全，效率高；key和value可以为null；底层实现数组+单向链表+红黑树（jdk8）
  - LinkedHashMap：是HashMap的子类，在HashMap的基础上增加了一对双向链表，记录添加的元素的先后顺序；遍历时可以按添加元素的顺序显示
- Hashtable：线程安全，效率低；key和value不可以为null；底层实现数组+单向链表（jdk8）
- TreeMap：底层使用红黑树，可以按照key的属性的大小顺序遍历。考虑使用（1）自然排序，（2）定制排序
  - Properties：其key和value都是String类型。常用于处理属性文件

Map中存在key和value属性，其中key不可以重复，组成keySet，key元素所属的类要重写hashCode和equals方法

value可以重复，组成Collection。value元素所属的类要重写equals方法

每条kv形成一个Entry，所有Entry组成EntrySet。

常用方法：
- 添加、插入
  - Object put(Object key, Object value)
- 删
  - Object remove(Object key) 返回value
- 改
  - Object put(Object key, Object value)
  - putAll(Object key, Object value)
- 查
  - Object get(Object key)
  - 
- 长度
  - size()
- 遍历
  - Set keySet() 遍历keySet
  - Collection values() 遍历value
  - Set entrySet() 遍历Entry

```java
    // define hashmap
    // keySet
    Set keySet = map.keySet();
    Iterator iterator = keySet.iterator();
    while (iterator.hasNext()) {
        // operation
    }

    // value collection
    Collection values = map.values();
    for (Object obj: values) {
        // operation
    }
    for (Object key: keySet) {
        map.get(key);
    }

    // entrySet 
    Set entrySet = map.entrySet();
    Iterator iterator = entrySet.iterator();
    while (iterator.hasNext()) {
        Map.Entry entry = (Map.Entry) iterator.next();
        entry.getKey();
        entry.getValue();
    }
```

### TreeMap
key的数据类型要一致

### Properties

使用场景：
- 数据和代码耦合度高，如果修改的话，需要重新编译打包
- 将数据封装到具体的配置文件中，在程序中读取文件

```java
    File file = new File("/path/filename");
    FileInputStream fis = new FileInputStream(file);
    Properties prop = new Properties();
    prop.load(fis);
    String property = prop.getProperty("property");
```

### Collections工具类

Collections是一个操作Set、List和Map等集合的工具类

区分Collection和Collections：
- Collection是一个抽象类，提供了集合的各种方法，子类分为List和Set两大类
- Collections是一个操作Set、List和Map等集合的工具类

常用方法：
- reverse(List)
- shuffle(List)
- sort(List)
- sort(List, Comparator)
- swap(List, int i, int j)
- max(Collection)
- max(Collection, Comparator)
- min(Collection)
- min(Collection, Comparator)
- binarySearch(List, T key)
- binarySearch(List, T key, Comparator)
- frequency(Collection, Object)
- 复制、替换
- copy(List dest, List src)
- boolean replaceAll(Collection c, T...)
- unmodifiableXxx，例如unmodifiableList
- 同步
- synchronizedXxx，例如，使对象变成线程安全

### HashMap源码
#### JDK 1.7

```java
    // 底层初始化一个数组，Entry[] entry = new Entry[16]; 扩容时每次乘2
    HashMap<String, Integer> map = new HashMap<>();

    // 将key value封装成一个entry，将此对象添加到table数组中
    map.put(key, value);
```

添加/修改的过程，将(key1, value1)添加进map

- 首先调用key1所在类的hashCode()，计算key1对应的hash1，再调用HashMap中的hash()，计算key1对应的hash2，调用indexFor()，计算key1在table数组中对应的索引位置i
    - 1.1 如果索引i上没有元素，那么(key1, value1)添加成功
    - 1.2 如果索引i上有元素(key2, value2)，那么需要比较key1和key2的hash2
      - 2.1 如果key1和key2的hash2不同，那么(key1, value1)添加成功
      - 2.2 如果key1和key2的hash2相同，那么调用key1和key2所属类的equals()。调用key1所在类的equals方法，即新添加的类的equals()：key1.equals(key2);
        - 3.1 调用equals()，返回false：那么(key1, value1)添加成功
        - 3.2 调用equals()，返回true：认为key1和key2是相同的，value1替换原有的value2

HashMap扩容机制
```java
    // 元素个数大于阈值 && 当前索引位置已经有元素
    // 阈值计算：数组长度 * 加载因子，加载因子默认为0.75，默认扩容为原来的2倍
    (size >= threshold) && (null != table[i])
```

HashMap允许插入key为null的值，会将其添加到索引0的位置

#### JDK 1.8与JDK 1.7的不同
- JDK 1.8中创建HashMap实例后，底层没有初始化table数组。当首次添加kv时，进行判断，如果table尚未初始化，则进行初始化
- JDK 1.8中定义了Node内部类，替换了JDK 1.7中的Entry内部类。即，创建的table数组是Node类型
- JDK 1.8中，如果当前kv经过hash运算后，可以添加到当前数组索引i中，且i上有元素，则将旧元素指向新的(key, value)（尾插法）。JDK 1.7中将新的(key, value)指向旧元素（头插法）
- JDK 1.7中，底层数据结构：数组+单向链表；JDK 1.8中，底层数据结构：数组+单向链表+红黑树
  - 使用红黑树的条件：如果数组索引i的位置上的元素个数大于等于8，且数组长度大于64，会将索引i位置上的多个元素建成红黑树
  - 红黑树变为单向链表的条件：索引i的位置上的元素个数小于等于6，就将红黑树变为单向链表。原因：树节点占用大小是链表节点的2倍
  - JDK 1.7，put, get, remove时间复杂度：O(n)
  - JDK 1.8，put, get, remove时间复杂度：O(log n)

为什么HashMap 在jdk 1.8之后使用尾插法，而不是头插法了？
- 为了防止出现环形链表
- 插入元素时，如果两个线程同时发生resize，那么所有元素都要被rehash到新的位置
- 此时可能会形成环形链表，造成死循环

HashMap put
- 首先检查table数组是否为空，是的话进行初始化

HashMap resize
- 检查oldCap是否超过MAXIMUM_CAPACITY
  - 是，将大小resize到Integer.MAX_VALUE
  - 否，将capacity扩容为2倍，将threshold扩容为2倍
- 如果oldThr>0，newCap=oldThr
- else，HashMap尚未初始化，对table数组设置初始大小16，thr设置为initial_capacity * load_factor
- 将原有元素映射到新的范围上

```java
// HashMap 定义/插入/treeify
public class HashMap<K,V> xxx {
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    static final int TREEIFY_THRESHOLD = 8;
    static final int UNTREEIFY_THRESHOLD = 6;
    static final int MIN_TREEIFY_CAPACITY = 64;

    final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        if ((p = tab[i = (n - 1) & hash]) == null) // (n - 1) & hash，其中n - 1是容量-1，是全1，与全1进行&操作，得到的结果就是原数；若为空，直接插入
            tab[i] = newNode(hash, key, value, null);
        else { // hash后不为空
            Node<K,V> e; K k;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k)))) // hash相同，而且equals比较也相同
                e = p;
            else if (p instanceof TreeNode) // 插入树上
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) { // 遍历到最后，也没找到key相同的，直接插入（尾插法），如果大于TREEIFY_THRESHOLD，进行树化
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k)))) // 在遍历桶的时候，找到了相同的key，直接跳出，并覆盖旧值
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
}
```

HashMap如何设定初始大小
- 若没有指定，则初始化为16
- 若指定为n，则将n扩大为>=n的第一个2的整数次幂
  - 将高位第一个1，不断右移，直到后面全部都是1

HashMap的hash函数如何设计的
- 首先获取key的hashCode，是int类型的32位数
- 将h与h>>>16进行^运算
- 为何这样设计？
  - 扰动函数
  - 原因一：降低hash碰撞
    - 异或完后，需要与n-1进行取模，此时异或的结果，既包含高位信息，又包含低位信息，增大了低位的随机性
  - 原因二：高频操作，采用^效率高

HashMap是线程安全的吗
- 不是
- HashTable、SynchronizedMap、ConcurrentHashMap是线程安全的
  - HashTable直接使用synchronized锁住方法，锁住的是整个数组，粒度大
  - SynchronizedMap是使用Collections的方法，通过传入map，获取到一个SynchronizedMap对象，内部定义了一个对象锁，方法内通过对象锁实现
  - ConcurrentHashMap，使用分段锁，降低了锁粒度，提高并发
    - 分段锁原理：成员变量使用volatile，禁止了指令重排，实现内存可见性，另外使用CAS和sychronized操作实现赋值，多线程操作只会锁住当前节点

HashMap链表转红黑树阈值，红黑树转链表阈值？为什么？
- 8和6
- 8是因为，hash碰撞8次的概率为百万分之6，具体与泊松分布有关
- 6是因为，在8附近可能发生红黑树和链表的频繁转换，因此设置低一点

## 泛型

## File IO

File类位于java.io包下，File类的对象对应OS中的文件或文件目录

构造器：
- public File(String filename)
- public File(String parent, String child)
- public File(File parent, String child)

常用方法：
- String getName()
- String getPath()
- String getAbsolutePath()
- File getAbsoluteFile()
- String getParent()
- long length()：获取文件长度（字节数），不能获取目录的长度
- long lastModified()
- 列出目录的下一级
  - String[] list()：返回该File目录下的所有子文件和目录
  - File[] listFiles()：返回该File目录下的所有子文件和目录
- boolean renameTo(File dest)：把文件重命名为指定的文件路径；file1.renameTo(file2)，要求：file1必须存在，file2不存在，file2所在目录必须存在
- 判断功能
  - boolean exists()
  - boolean isDirectory()
  - boolean isFile()
  - boolean canRead()
  - boolean canWrite()
  - boolean isHidden()
- 创建、删除
  - boolean createNewFile()
  - boolean mkdir()
  - boolean mkdirs()
  - boolena delete() 若要删除目录，要保证目录内没有文件或子目录

### IO流
分类
- 按方向不同：输入流、输出流
- 按传输数据的不同：字符流、字节流
- 流的角色不同：节点流、处理流

抽象基类：
- 字节流
  - InputStream
  - OutputStream
- 字符流
  - Reader
  - Writer

缓冲流（处理流的一种）
- BufferedInputStream
- BufferedOutputStream
- BufferedReader
- BufferedWriter

BufferedInputStream和BufferedOutputStream的速度快于FileInputStream和FileOutputStream，因为Buffered内部开了较大空间的数组用于缓存

### 转换流

字符编码：字符、字符串、字符数组 -> 字节、字节流

字符解码；字符编码的逆过程

转换流：防止程序读取字符时出现乱码。解码时使用的字符集，必须是编码时的字符集或与之兼容的字符集

- InputStreamReader：将输入型的字节流转换为输入型的字符流
- OutputStreamWriter：将输出型的字符流转换为输出型的字节流

字符集：
- ascii：基础符号、大小写字母、数字，共128个字节。每个字符占一个字节
- GBK：简体中文码表，中文字符使用2个字节存储，向下兼容ascii
- Unicode：表示任意字符
- utf-8：UCS Transfer Format，是将Unicode数字转换到程序数据的编码方案，每次实现8位传输。
  - 变长编码
    - ascii：1字节
    - 拉丁文等字符：2字节
    - 中文：3字节
    - 其他极少使用的Unicode字符：4字节
- 在内存中的字符：char占2字节，字符集使用Unicode

### 对象流

对象流可以将Java对象写入到数据源中，也能把对象从数据源中还原回来。原理是将对象转换为平台无关的二进制流，将二进制流永久保存在磁盘上，或通过网络流传到另一个节点上。

- ObjectInputStream：反序列化使用
- ObjectOutputStream：序列化使用

自定义类序列化的条件：
- 实现Serializable接口，不需要重写任何方法，称为标识接口
- 声明一个全局常量：static final long serialVersionUID = xxxL; 注意：如果不声明，系统会自动生成一个serialVersionUID。此时修改此类，UID会改变，导致反序列化时，出现InvalidClassException异常
- 要求自定义类的各个属性也是可序列化的
  - 基础数据类型：本身可序列化
  - 引用数据类型：需要实现Serializable接口
- 类中属性如果声明为transient或static，那么不会实现序列化


## 反射
反射机制允许程序在运行期间借助Reflection API取得任何类的内部信息，并且能直接操作任意对象的内部属性及方法

调用对象的属性、方法、构造器，使不使用反射的区别
- 不使用反射，需要考虑封装性。在类的外部，无法调用private成员
- 使用反射，可以调用运行时类中任意成员，包括private成员

### Class类
针对于编写好的.java进行编译（javac.exe），会生成一个或多个.class文件。然后使用java.exe对.class文件进行解释运行，在这个过程中，需要将.class字节码文件加载到内存中(使用类的加载器，加载到方法区)。加载到内存中的.class文件的结构就是Class的一个实例。

运行时类在内存中会缓存起来，程序运行期间只会加载一次

获取class实例的方式：
- 调用运行时类的静态属性class：Class c = User.class;
- 调用getClass()方法：User u = new User(); Class c = u.getClass();
- 调用Class.forName()：String className = "package.className";  Class c = Class.forName(className);
- 使用类的加载器：Class c = ClassLoader.getSystemClassLoader.loadClass(className);

类的加载过程
1. 加载（load）：将类的class文件读到内存中，创建一个java.lang.Class对象。这个过程由类加载器完成。
2. 链接（linking）：
   1. 验证（verify）：确保加载的类信息符合JVM规范。例如以cafebabe开头，没有安全问题
   2. 准备（prepare）：正式为类变量（static）分配内存并设置默认初始值的阶段，这些内存都将在方法区中进行分配
   3. 解析（resolve）：虚拟机常量池中的符号引用（常量名）替换为直接引用（地址）的过程
3. 初始化（initialization）：执行类构造器\<clinit\>()方法的过程。类构造器\<clinit\>()方法是由编译期自动收集所有类变量的赋值动作和静态代码块中的语句合并产生

类加载器分类
- Bootstrap ClassLoader：启动类加载器
  - 使用C/C++编写，通过java无法获取实例
  - 负责加载java的核心库
- 继承于ClassLoader的类加载器
  - Extension ClassLoader：扩展类加载器
  - System ClassLoader/Application ClassLoader：系统/应用类加载器，自定义的类使用这个加载器加载
  - 用户自定义的ClassLoader：实现应用隔离（同一个类在一个程序中可以加载多份），数据的加密

### 反射的应用
创建运行时类的对象

通过Class类的实例调用newInstance()方法（jdk8及之前）

条件是：
- 要求运行时类必须提供一个空参构造器
- 空参构造器的访问权限必须足够

在jdk9中，通过Constructor类的newInstance(...)

获取运行时类的内部结构：
- 1 所有属性、方法、构造器
- 2 父类、接口们、包、带泛型的父类、父类的泛型等

```java
    //获取运行时的父类的泛型类型
    Class clazz = Class.forName("xxx/Person");
    // 获取带泛型的父类
    Type superclass = clazz.getGenericSuperclass();
    // 如果父类是带泛型的，可以强转为ParameterizedType
    ParameterizedType paramType = (ParameterizedType) superclass;
    // 通过getActualTypeArguments()获取泛型参数，返回结果是数组，因为可能有多个
    Type[] arguments = paramType.getActualTypeArguments();
    // 获取泛型参数的名称
    System.out.println(((Class)arguments[0]).getName());
```

调用指定的结构：指定的属性、方法、构造器

```java
    // 获取属性
    Class<Student> c = Student.class;
    Student s1 = (Student) c.newInstance();
    Field idField = c.getDeclaredField("id");
    idField.setAccessible(true);
    // 如果属性是static，将下面的对象改为null或Student.class
    idField.set(s1, 10);
    System.out.println(idField.get(s1));

    // 获取方法；如果参数类型是int，要传入int.class，不能是Integer.class
    Method showMethod = c.getDeclaredMethod("show", String.class);
    showMethod.setAccessible(true);
    Object obj = showMethod.invoke(s1, "lixueqi"); // 如果method的返回类型是void，则obj是null
    System.out.println(obj);

    // 调用指定的构造器
    Class clazz = Student.class;
    Constructor constructor = clazz.getDeclaredConstructor(Stirng.class, int.class);
    constructor.setAccessible(true);
    Student s1 = (Student) constructor.newInstance("Tom", 12);
```

获取指定的注解
```java
    // 获取声明类上的注解
    Class clazz = Customer.class;
    Table annotation = (Table) clazz.getDeclaredAnnotation(Table.class);
    System.out.println(annotation.value());

    // 获取属性声明的注解 @Column(columnName="", columnType="")
    Class clazz = Customer.class;
    Field nameField = clazz.getDeclaredField("name");
    Column nameColumn = nameField.getDeclaredAnnotation(Column.class);
    System.out.println(nameColumn.columnName() + nameColumn.columnType());
```

## 动态代理

代理模式：给真实的对象提供一个代理，由代理控制对真实对象的访问

代理模式角色：
- Subject 抽象主题角色：定义代理类和真实主题的公共对外方法，也是代理类代理真实主题的方法
- RealSubject 真实主题角色：真正实现业务逻辑的类
- Proxy 代理主题角色：用来代理和封装真实主题

根据字节码的创建时机对代理分类：
- 静态代理：在程序运行之前就存在代理类的字节码文件，代理类和真实主题角色的关系已经确定
- 动态代理：在程序运行期间由JVM根据反射动态生成

动态代理的两种实现方式
- JDK动态代理，通过实现接口的方式：
    ```java
    import java.lang.reflect.InvocationHandler;
    import java.lang.reflect.Method;
    import java.util.Date;

    public class LogHandler implements InvocationHandler {
        Object target;  // 被代理的对象，实际的方法执行者

        public LogHandler(Object target) {
            this.target = target;
        }

        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            before();
            Object result = method.invoke(target, args);  // 调用 target 的 method 方法
            after();
            return result;  // 返回方法的执行结果
        }
        // 调用invoke方法之前执行
        private void before() {
            System.out.println(String.format("log start time [%s] ", new Date()));
        }
        // 调用invoke方法之后执行
        private void after() {
            System.out.println(String.format("log end time [%s] ", new Date()));
        }
    }
    ```

    客户端实现
    ```java
    import proxy.UserService;
    import proxy.UserServiceImpl;
    import java.lang.reflect.InvocationHandler;
    import java.lang.reflect.Proxy;

    public class Client2 {
        public static void main(String[] args) throws IllegalAccessException, InstantiationException {
            // 设置变量可以保存动态代理类，默认名称以 $Proxy0 格式命名
            // System.getProperties().setProperty("sun.misc.ProxyGenerator.saveGeneratedFiles", "true");
            // 1. 创建被代理的对象，UserService接口的实现类
            UserServiceImpl userServiceImpl = new UserServiceImpl();
            // 2. 获取对应的 ClassLoader
            ClassLoader classLoader = userServiceImpl.getClass().getClassLoader();
            // 3. 获取所有接口的Class，这里的UserServiceImpl只实现了一个接口UserService，
            Class[] interfaces = userServiceImpl.getClass().getInterfaces();
            // 4. 创建一个将传给代理类的调用请求处理器，处理所有的代理对象上的方法调用
            //     这里创建的是一个自定义的日志处理器，须传入实际的执行对象 userServiceImpl
            InvocationHandler logHandler = new LogHandler(userServiceImpl);
            /*
          5.根据上面提供的信息，创建代理对象 在这个过程中，
                  a.JDK会通过根据传入的参数信息动态地在内存中创建和.class 文件等同的字节码
                  b.然后根据相应的字节码转换成对应的class，
                  c.然后调用newInstance()创建代理实例
        */
            UserService proxy = (UserService) Proxy.newProxyInstance(classLoader, interfaces, logHandler);
            // 调用代理的方法
            proxy.select();
            proxy.update();
            
            // 保存JDK动态代理生成的代理类，类名保存为 UserServiceProxy
            // ProxyUtils.generateClassFile(userServiceImpl.getClass(), "UserServiceProxy");
        }
}
    ```

- CGLIB动态代理，通过继承类的方式：
    ```java
    import java.lang.reflect.Method;
    import java.util.Date;

    public class LogInterceptor implements MethodInterceptor {
        /**
        * @param object 表示要进行增强的对象
        * @param method 表示拦截的方法
        * @param objects 数组表示参数列表，基本数据类型需要传入其包装类型，如int-->Integer、long-Long、double-->Double
        * @param methodProxy 表示对方法的代理，invokeSuper方法表示对被代理对象方法的调用
        * @return 执行结果
        * @throws Throwable
        */
        @Override
        public Object intercept(Object object, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
            before();
            Object result = methodProxy.invokeSuper(object, objects);   // 注意这里是调用 invokeSuper 而不是 invoke，否则死循环，methodProxy.invokesuper执行的是原始类的方法，method.invoke执行的是子类的方法
            after();
            return result;
        }
        private void before() {
            System.out.println(String.format("log start time [%s] ", new Date()));
        }
        private void after() {
            System.out.println(String.format("log end time [%s] ", new Date()));
        }
    }
    ```
    测试
    ```java
    import net.sf.cglib.proxy.Enhancer;

    public class CglibTest {
        public static void main(String[] args) {
            DaoProxy daoProxy = new DaoProxy(); 
            Enhancer enhancer = new Enhancer();
            enhancer.setSuperclass(Dao.class);  // 设置超类，cglib是通过继承来实现的
            enhancer.setCallback(daoProxy);

            Dao dao = (Dao)enhancer.create();   // 创建代理类
            dao.update();
            dao.select();
        }
    }
    ```
CGLIB创建动态代理类的模式是：
1. 查找目标类上的所有非final的public类型的方法定义；
2. 将这些方法的定义转换成字节码；
3. 将组成的字节码转换成相应的代理的class对象；
4. 实现MethodInterceptor接口，用来处理对代理类上所有方法的请求

JDK动态代理和CGLIB动态代理对比：
- JDK优势：
  - 最小化依赖关系，减少依赖意味着简化开发和维护，JDK 本身的支持，可能比 cglib 更加可靠
  - 平滑进行 JDK 版本升级，而字节码类库通常需要进行更新以保证在新版 Java 上能够使用
  - 代码实现简单
- CGLIB优势：
  - 无需实现接口，达到代理类无侵入
  - 只操作我们关心的类，而不必为其他相关类增加工作量
  - 高性能


## 网络编程

InetAddress的一个实例就代表一个具体的IP地址

实例化：
- getByName(String host)
- getLocalHost()

```java
InetAddress inet1 = InetAddress.getByName("xxx.xxx.xxx.xxx");
InetAddress inet2 = InetAddress.getByName("www.google.com");
InetAddress inet3 = InetAddress.getLocalHost();

// 两个常用方法
System.out.println(inet1.getHostName());
System.out.println(inet2.getHostAddress());
```


## Java多线程总结

### 进程与线程基本概念

程序：代码的集合，一段静态代码。

进程：应用程序在内存中分配的空间，运行中的程序。

线程：一个进程中包含多个线程，分别执行多个子任务。

进程和线程的区别
- 进程独占一块内存地址空间，进程之间的数据是隔离的，不可共享。线程共享所属进程的内存地址，它们之间数据可以共享
- 进程出现问题不会影响其他程序的稳定。线程如果崩溃，可能会导致进程和其他线程崩溃
- 进程的创建和销毁不仅要保存栈信息和寄存器，还有资源的分配和页调度，开销大。线程只需要保存寄存器和栈信息，开销较小。

上下文：某一时间点的CPU寄存器和程序计数器的内容

上下文切换是计算密集型的，会消耗大量CPU时间

### Java多线程入门 类和接口

Java多线程实现方式：
- 继承Thread类，重写run()
- 实现Runnable接口，实现run()
- Callable, Future, FutureTask
  - Callable接口，需要实现call()，有返回值，且支持泛型
  - Callable使用通常搭配线程池Executor Service
  ```java
        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task();
        Future<Integer> result = executor.submit(task);
        // 注意调用get方法会阻塞当前线程，直到得到结果。
        // 所以实际编码中建议使用可以设置超时时间的重载get方法。
        System.out.println(result.get()); 
  ```
  - Future接口
  ```java
    public abstract interface Future<V> {
    public abstract boolean cancel(boolean paramBoolean);
    public abstract boolean isCancelled();
    public abstract boolean isDone();
    public abstract V get() throws InterruptedException, ExecutionException;
    public abstract V get(long paramLong, TimeUnit paramTimeUnit)
            throws InterruptedException, ExecutionException, TimeoutException;
    }
  ```
  其中cancel()不一定能成功
  - FutureTask类，实现了RunnableFuture接口，该接口同时继承了Runnable和Future. FutureTask类实现了Future接口中的各个方法. FutureTask能够保证高并发情况下任务只执行一次
  ```java
        // 使用
        ExecutorService executor = Executors.newCachedThreadPool();
        FutureTask<Integer> futureTask = new FutureTask<>(new Task());
        executor.submit(futureTask);
        System.out.println(futureTask.get());
  ```
  - FutureTask的几个状态
  ```java
  /**
  *
  * state可能的状态转变路径如下：
  * NEW -> COMPLETING -> NORMAL
  * NEW -> COMPLETING -> EXCEPTIONAL
  * NEW -> CANCELLED
  * NEW -> INTERRUPTING -> INTERRUPTED
  */
    private volatile int state;
    private static final int NEW          = 0;
    private static final int COMPLETING   = 1;
    private static final int NORMAL       = 2;
    private static final int EXCEPTIONAL  = 3;
    private static final int CANCELLED    = 4;
    private static final int INTERRUPTING = 5;
    private static final int INTERRUPTED  = 6;
  ```
  

### 线程组和线程优先级

每个Thread必然存在于一个ThreadGroup中，Thread不能独立于ThreadGroup存在。

如果new Thread时没有显示指定，那么该线程所在线程组是父线程的线程组

ThreadGroup是一个向下引用的树状结构，原因是防止"上级"线程被"下级"线程引用而无法有效地被GC回收

Java中优先级1-10，10是最高，但操作系统可能不支持这样的优先级，只支持低、中、高三级。Java中优先级高的线程被执行的概率更高。

Java提供一个线程调度器来监视和控制处于RUNNABLE状态的线程。线程的调度策略采用抢占式。

守护线程（Daemon），如果所有非守护线程都结束了，那守护线程也会自动结束
- 应用场景：当所有非守护线程都结束，其余守护线程都自动结束，免去了手动结束的麻烦

当线程组与线程优先级不一致，如果线程优先级高于线程组优先级，那么线程的优先级会降为线程组优先级

线程状态的转换
- BLOCKED <--> RUNNABLE：BLOCKED被阻塞
- WAITING <--> RUNNABLE：Object.wait()，调用wait前线程必须持有对象的锁，调用后会释放锁，直到被notify/notifyAll唤醒 ； Thread.join()，等待调用者线程执行完毕
- TIMED_WAITING <--> RUNNABLE：Thread.sleep(long) / Object.wait(long) / Thread.join(long)

### 锁与同步
#### 锁

在Java中，锁的概念都是基于对象的，所以我们又经常称它为对象锁。一个锁同一时间只能被一个线程持有。

可以通过锁实现线程的同步

#### 等待/通知机制

wait() / notify() / notifyAll()

A持有锁，调用wait()会释放锁，此时线程B获得锁并开始执行。执行到某一时刻，调用notify()，通知之前持有锁并进入等待的线程A继续执行。注意：此时B并没有释放锁，而是要主动调用wait()或执行完毕同步区的代码，才会释放锁。

#### 信号量：Java基于volatile关键字自己实现了一种信号量

volatile关键字能够保证内存的可见性，如果用volatile关键字声明了一个变量，在一个线程里面改变了这个变量的值，那其它线程是立马可见更改后的值的。

```java
public class Signal {
    private static volatile int signal = 0;

    static class ThreadA implements Runnable {
        @Override
        public void run() {
            while (signal < 5) {
                if (signal % 2 == 0) {
                    System.out.println("threadA: " + signal);
                    signal++;
                }
            }
        }
    }

    static class ThreadB implements Runnable {
        @Override
        public void run() {
            while (signal < 5) {
                if (signal % 2 == 1) {
                    System.out.println("threadB: " + signal);
                    signal = signal + 1;
                }
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new Thread(new ThreadA()).start();
        Thread.sleep(1000);
        new Thread(new ThreadB()).start();
    }
}

// 输出：
threadA: 0
threadB: 1
threadA: 2
threadB: 3
threadA: 4
```

注意：signal++并不是原子操作。因此实际中，通常用synchronized包围或使用AtomicInteger等原子类

#### 管道

管道是基于“管道流”的通信方式。JDK提供了PipedWriter、 PipedReader、 PipedOutputStream、 PipedInputStream。其中，前面两个是基于字符的，后面两个是基于字节流的。

应用场景：当我们一个线程需要先另一个线程发送一个信息（比如字符串）或者文件等等时

#### 其他通信相关知识

1. join

join()方法是Thread类的一个实例方法。它的作用是让当前线程陷入“等待”状态，等join的这个线程执行完成后，再继续执行当前线程。

有时候，主线程创建并启动了子线程，如果子线程中需要进行大量的耗时运算，主线程往往将早于子线程结束之前结束。

如果主线程想等待子线程执行完毕后，获得子线程中的处理完的某个数据，就要用到join方法了。

2. sleep

sleep方法是Thread类的一个静态方法。它的作用是让当前线程睡眠一段时间。

sleep不会释放当前的锁，wait会释放锁

sleep与wait的区别
- sleep必须指定时间，wait可以指定也可以不指定
- slee释放cpu资源，不释放锁，易死锁；wait释放cpu资源和锁
- sleep可以在任意位置；wait必须在同步块或同步方法中

3. ThreadLocal

ThreadLocal是一个本地线程副本变量工具类。内部是一个弱引用的Map来维护。

ThreadLocal为每个线程都创建一个副本，每个线程可以访问自己内部的副本变量。

4. InheritableThreadLocal

线程的子线程也可以存取副本值

### Java内存模型

并发编程模型两个关键问题
- 线程之间如何通信
- 线程之间如何同步

解决上面两个问题的两种模型
- 消息传递并发模型
  - 如何通信：线程之间没有公共状态，必须发送消息来进行通信
  - 如何同步：同步是隐式的，因为发送消息总是在接收消息之前
- 共享内存并发模型（java使用）
  - 如何通信：线程之间共享程序的公共状态，通过写-读内存中的公共状态进行隐式通信
  - 如何同步：同步是显式的，必须显式指定某段代码在线程之间互斥执行

Java运行时，线程之间共享`方法区、堆`，私有`本地方法栈、虚拟机栈、程序计数器`，也就是栈中变量（局部变量、方法定义参数、异常处理器参数）不会在线程之间共享，也就不会有内存可见性的问题，也不受内存模型的影响。而在堆中的变量是共享的，内存可见性针对的是共享变量。

JMM抽象模型中，每个线程都有自己的本地内存区，存放着共享变量的副本；所有共享变量都存在主内存中；线程A和B通信的步骤
- 线程A将本地内存A中更新过的共享变量刷新到主内存中去
- 线程B到主内存中去读取线程A之前已经更新过的共享变量

JMM和Java内存区域的区别与联系
- 区别
  - JMM是抽象的，描述一组规则，控制变量的访问；而Java内存区域是JVM划分的具体内存
- 联系
  - 本地内存都是`虚拟机栈、本地方法栈、程序计数器`
  - 共享内存都是`堆、方法区`

### 重排序与happens-before

#### 重排序

重排序：计算机在执行程序时，为了提高性能，编译器和处理器常常会对指令做重排

流水线技术用于提升指令执行效率，但是中断会造成很大代价。指令重排可以减少中断。

指令重排
- 编译器优化重排：编译器在不改变**单线程**程序语义前提下，重排指令
- 指令并行重排：处理器在后面的指令不依赖于前面指令的结果时，重排
- 内存系统重排：由于处理器使用缓存和缓冲区，使得加载和存储操作看上去是在乱序执行，因为三级缓存的存在，导致内存与缓存的数据同步存在时间差

指令重排可以保证串行语义一致，但是没有义务保证多线程间的语义也一致。

#### 顺序一致性模型

JMM保证了如果线程正确同步，程序的执行将具有顺序一致性，不会出现数据竞争

顺序一致性模型是一个理想化的理论参考模型，两大特性
- 一个线程中的所有操作必须按照程序顺序执行
- 不管程序是否同步，所有线程都只能看到一个单一的操作执行顺序。即在顺序一致性模型中，每个操作必须是原子性的，且立刻对所有线程可见。**但是，JMM没有这样的保证**

#### happens-before

happens-before关系的定义如下：
  1. 如果一个操作happens-before另一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前。
  2. 两个操作之间存在happens-before关系，并不意味着Java平台的具体实现必须要按照happens-before关系指定的顺序来执行。如果重排序之后的执行结果，与按happens-before关系来执行的结果一致，那么JMM也允许这样的重排序。

Java中，有以下天然的happends-before规则
- 程序顺序规则：一个线程中的每一个操作，happens-before于该线程中的任意后续操作
- 监视器锁规则：对一个锁的解锁，happens-before于随后对这个锁的加锁
- volatile变量规则：对一个volatile域的写，happens-before于任意后续对这个volatile域的读
- 传递性：如果A happens-before B，且B happens-before C，那么A happens-before C
- start规则：如果线程A执行操作ThreadB.start()启动线程B，那么A线程的ThreadB.start（）操作happens-before于线程B中的任意操作
- join规则：如果线程A执行操作ThreadB.join（）并成功返回，那么线程B中的任意操作happens-before于线程A从ThreadB.join()操作成功返回

### volatile

Java中volatile的内存语义：
- 保证变量的内存可见性
  - 当一个线程对volatile修饰的变量进行写操作时，JMM会立即把该线程本地内存中的共享变量的值刷新到主内存
  - 当一个线程对volatile修饰的变量进行读操作时，JMM会立即把该线程本地内存置为无效，从主内存中重新读取共享变量的值
- 禁止volatile变量与普通变量重排序（从jdk5开始）
  - JVM通过内存屏障，限制处理器的重排序，提供了一种比锁更轻量级的通信机制
  - JMM屏障插入策略：
    - 在每个volatile写操作前插入一个StoreStore屏障
    - 在每个volatile写操作后插入一个StoreLoad屏障
    - 在每个volatile读操作后插入一个LoadLoad屏障
    - 在每个volatile读操作后再插入一个LoadStore屏障

volatile和普通变量的重排序规则
- 如果第一个操作是volatile读，无论第二个操作是什么，都不能重排序
- 如果第二个操作是volatile写，无论第一个操作是什么，都不能重排序
- 如果第一个操作是volatile写，第二个操作是volatile读，都不能重排序

功能上，锁比volatile强；性能上，volatile高于锁

```java
// Singleton

instance = new Singleton(); // 第10行

// 可以分解为以下三个步骤
1 memory=allocate();// 分配内存 相当于c的malloc
2 ctorInstanc(memory) //初始化对象
3 s=memory //设置s指向刚分配的地址

// 上述三个步骤可能会被重排序为 1-3-2，也就是：
1 memory=allocate();// 分配内存 相当于c的malloc
3 s=memory //设置s指向刚分配的地址
2 ctorInstanc(memory) //初始化对象
```

线程2可能会得到未初始化的对象

### synchronized

java中的锁都是基于对象的

synchronized加锁的几种方式
```java
// 关键字在实例方法上，锁为当前实例
public synchronized void instanceLock() {
    // code
}

// 关键字在静态方法上，锁为当前Class对象
public static synchronized void classLock() {
    // code
}

// 关键字在代码块上，锁为括号里面的对象
public void blockLock() {
    Object o = new Object();
    synchronized (o) {
        // code
    }
}
```

#### 锁的分类

java 6 为了减少获得锁和释放锁带来的性能损耗，加入了偏向锁和轻量级锁。此后，锁的级别从低到高是：
- 无锁：没有对资源进行锁定
- 偏向锁
- 轻量级锁
- 重量级锁

几种锁会随着竞争逐渐升级，但是锁降级条件很苛刻，发生在Stop the World期间，当JVM进入安全点时，会检查是否有闲置的锁，然后进行降级

#### 偏向锁

大多数情况下锁不仅不存在多线程竞争，而且总是由同一线程多次获得，于是引入了偏向锁。

偏向锁会偏向于第一个访问锁的线程，如果在接下来的运行过程中，该锁没有被其他的线程访问，则持有偏向锁的线程将永远不需要触发同步。也就是说，偏向锁在资源无竞争情况下消除了同步语句，连CAS操作都不做了，提高了程序的运行性能。

- 加锁
  - 一个线程第一次进入同步块时，会在栈帧和对象头中记录偏向的线程ID，下次该线程进入同步块时，会检查是不是自己的ID
    - 是，则说明已经获取了偏向锁，不需要CAS来加锁和解锁
    - 不是，表示有另外的线程来竞争偏向锁，会尝试使用CAS来替换Mark Word里的线程ID为新的ID
      - 成功，表示之前的线程不存在了，用新线程ID设置Mark Work里的线程ID，锁不会升级，仍是偏向锁
      - 失败，表示之前的线程存在，暂停之前的线程，设置偏向锁标志为0，设置锁标志位00，升级为轻量级锁
- 解锁
  - 等到竞争才释放锁

#### 轻量级锁

- 加锁
  - JVM会为每个线程在当前线程的栈帧中创建用于存储锁记录的空间，我们称为Displaced Mark Word。如果一个线程获得锁的时候发现是轻量级锁，会把锁的Mark Word复制到自己的Displaced Mark Word里面。
  - 线程尝试用CAS将锁的Mark Word替换为指向锁记录的指针
    - 成功，获得轻量级锁
    - 失败，使用自旋来获取锁（一般通过循环来实现），JDK采用适应性自旋：线程如果自旋成功了，则下次自旋的次数会更多，如果自旋失败了，则自旋的次数就会减少。
    - 如果自旋到一定程度，依然没有获取到锁，称为自旋失败，那么这个线程会阻塞。锁升级为重量级锁
- 解锁
  - 在释放锁时，当前线程会使用CAS操作将Displaced Mark Word的内容复制回锁的Mark Word里面。如果没有发生竞争，那么这个复制的操作会成功。如果有其他线程因为自旋多次导致轻量级锁升级成了重量级锁，那么CAS操作会失败，此时会释放锁并唤醒被阻塞的线程。

#### 重量级锁

依赖于OS的互斥量mutex实现，而os中线程状态转换需要较长时间，所以重量级锁效率很低，但是被阻塞的线程不消耗CPU

当多个线程同时请求对象锁时，对象锁会设置几种状态来区分请求的线程
```java
Contention List：所有请求锁的线程将被首先放置到该竞争队列
Entry List：Contention List中那些有资格成为候选人的线程被移到Entry List
Wait Set：那些调用wait方法被阻塞的线程被放置到Wait Set
OnDeck：任何时刻最多只能有一个线程正在竞争锁，该线程称为OnDeck
Owner：获得锁的线程称为Owner
!Owner：释放锁的线程
```

- 当一个线程试图获取锁时，如果该锁已经被占用，则会将当前线程封装成一个ObjectWaiter对象插入Contention List队首，然后调用park函数挂起当前线程
- 当线程释放锁时，会从Contention List或EntryList中挑选一个线程唤醒，被选中的线程叫做Heir presumptive即假定继承人，假定继承人被唤醒后会尝试获得锁，但synchronized是非公平的，所以假定继承人不一定能获得锁。这是因为对于重量级锁，线程先自旋尝试获取锁，目的是减少执行OS同步带来的开销，如果自旋不成功再进入等待队列
- 如果线程获得锁后调用Object.wait方法，则会将线程加入到WaitSet中，当被Object.notify唤醒后，会将线程从WaitSet移动到Contention List或EntryList中去。需要注意的是，当调用一个锁对象的wait或notify方法时，**如当前锁的状态是偏向锁或轻量级锁，则会先膨胀成重量级锁**

#### 锁升级的流程

每一个线程在准备获取共享资源时：
1. 检查MarkWord里面是不是放的自己的ThreadId ,如果是，表示当前线程是处于 “偏向锁” 。
2. 如果MarkWord不是自己的ThreadId，锁升级，这时候，用CAS来执行切换，新的线程根据MarkWord里面现有的ThreadId，通知之前线程暂停，之前线程将Markword的内容置为空。
3. 两个线程都把锁对象的HashCode复制到自己新建的用于存储锁的记录空间，接着开始通过CAS操作，把锁对象的MarKword的内容修改为自己新建的记录空间的地址的方式竞争MarkWord。
4. 第三步中成功执行CAS的获得资源，失败的则进入自旋。
5. 自旋的线程在自旋过程中，成功获得资源(即之前获的资源的线程执行完成并释放了共享资源)，则整个状态依然处于轻量级锁的状态，如果自旋失败。
6. 进入重量级锁的状态，这个时候，自旋的线程进行阻塞，等待之前线程执行完成并唤醒自己。

### CAS与原子操作

#### 乐观锁与悲观锁

- 悲观锁：认为线程一定会冲突，所有操作都加锁
- 乐观锁：又叫“无锁”，假设没有冲突，若冲突，使用CAS解决，不会发生死锁

#### CAS

CAS，compare and swap，其中有三个值
- V：要更新的变量var
- E：预期值expected
- N：新值new

CAS操作过程：判断V是否等于E，如果等于，将V的值设置为N；如果不等，说明已经有其它线程更新了V，则当前线程放弃更新，什么都不做。

CAS是一种原子操作，是一条CPU原子指令。当多个线程同时使用CAS操作一个变量时，只有一个会成功，其他的线程可以进行重试或放弃

#### Java实现CAS：Unsafe类

Unsafe类中有几个native方法，通过C++实现CAS

```java
boolean compareAndSwapObject(Object o, long offset,Object expected, Object x);
boolean compareAndSwapInt(Object o, long offset,int expected,int x);
boolean compareAndSwapLong(Object o, long offset,long expected,long x);
```

#### AtomicInteger

AtomicInteger中的`getAndAdd`方法底层是通过调用Unsafe类的方法实现的

```java
// AtomicInteger.getAndAdd()
private static final jdk.internal.misc.Unsafe U = jdk.internal.misc.Unsafe.getUnsafe();


public final int getAndAdd(int delta) {
    return U.getAndAddInt(this, VALUE, delta);
}

// U.getAndAddInt()
@HotSpotIntrinsicCandidate
public final int getAndAddInt(Object o, long offset, int delta) {
    int v;
    do {
        v = getIntVolatile(o, offset);
    } while (!weakCompareAndSetInt(o, offset, v, v + delta));
    return v;
}

// weakCompareAndSetInt
public final boolean weakCompareAndSetInt(Object o, long offset,
                                          int expected,
                                          int x) {
    return compareAndSetInt(o, offset, expected, x);
}

public final native boolean compareAndSetInt(Object o, long offset,
                                             int expected,
                                             int x);
```

#### CAS实现原子操作的三大问题
- ABA问题
  - 一个值原来是A，被更新为B，又变回了A，此时CAS检查不出来，但是被更新了两次
  - 解决方案：在变量前加上时间戳或版本号。从JDK 1.5开始，atomic包提供了AtomicStampedReference来解决ABA问题
  - 这个类首先比较当前引用是否等于预期引用，在比较当前标志是否等于预期标志，若都相等，才使用CAS
- 循环时间长，开销大
  - CAS多与自旋相结合，长时间自旋不成功，会消耗大量CPU资源
  - 解决方案：JVM支持处理器提供的**pause指令**
  - pause指令能让自旋失败时cpu睡眠一小段时间再继续自旋，从而使得读操作的频率低很多,为解决内存顺序冲突而导致的CPU流水线重排的代价也会小很多
- 只能保证一个共享变量的原子操作，解决方案
  - 1 使用JDK 1.5开始就提供的AtomicReference类保证对象之间的原子性，把多个变量放到一个对象里面进行CAS操作
  - 2 使用锁。锁内的临界区代码可以保证只有当前线程能操作

### AQS

AQS是AbstractQueuedSynchronizer，抽象队列同步器，是一个用来构建锁和同步器的框架，ReentrantLock, Semaphore, ReentrantReadWriteLock, SynchronousQueue, FutureTask等都是基于AQS的

#### AQS的数据结构
AQS内部使用了一个volatile的state表示资源的状态，并提供了几个方法操作state，子类重写这几个方法来实现自己的逻辑
```java
getState()
setState()
compareAndSetState()
```

这三个操作都是原子操作，其中`compareAndSetState`方法的实现依赖于Unsafe类的`compareAndSwapInt`方法

AQS类本身实现的是一些排队和阻塞的机制，比如具体线程等待队列的维护（如获取资源失败入队/唤醒出队等）。它内部使用了一个先进先出（FIFO）的双端队列，并使用了两个指针head和tail用于标识队列的头部和尾部。但它并不是直接储存线程，而是储存拥有线程的Node节点。

#### 资源共享模式
- 独占模式Exclusive：一次只能有一个线程获取，例如ReentrantLock
- 共享模式Share：多个线程可以同时获取，具体资源个数可通过参数指定，如Semaphore/CountDownLatch

一般情况子类只需根据需求实现一个，也可都实现，如：ReadWriteLock

AQS获取资源

入口是`acquire(int arg)`方法，arg是要获取的资源的个数，在exclusive模式下始终为1
  ```java
    public final void acquire(int arg) {
      if (!tryAcquire(arg) &&  // 尝试获取资源
          acquireQueued(addWaiter(Node.EXCLUSIVE), arg)) // 通过addWaiter将线程加入等待队列，CAS自选等待；通过acquireQueued队列的第二个节点尝试获取资源，循环直到成功
          selfInterrupt(); // 获取资源成功判断是否需要中断
    }
  ```

AQS释放资源
```java
public final boolean release(int arg) {
    if (tryRelease(arg)) {
        Node h = head;
        if (h != null && h.waitStatus != 0)
            unparkSuccessor(h);
        return true;
    }
    return false;
}

private void unparkSuccessor(Node node) {
    // 如果状态是负数，尝试把它设置为0
    int ws = node.waitStatus;
    if (ws < 0)
        compareAndSetWaitStatus(node, ws, 0);
    // 得到头结点的后继结点head.next
    Node s = node.next;
    // 如果这个后继结点为空或者状态大于0
    // 通过前面的定义我们知道，大于0只有一种可能，就是这个结点已被取消
    if (s == null || s.waitStatus > 0) {
        s = null;
        // 等待队列中所有还有用的结点，都向前移动
        for (Node t = tail; t != null && t != node; t = t.prev)
            if (t.waitStatus <= 0)
                s = t;
    }
    // 如果后继结点不为空，
    if (s != null)
        LockSupport.unpark(s.thread);
}
```

### 线程池原理

使用线程池的原因：
- 操作系统每次创建销毁线程都需要消耗资源，而线程池可以复用提前创建好的线程
- 控制并发的数量。并发数量过多，可能会导致资源消耗过多，使系统崩溃。（主要原因）
- 便于对线程统一管理

Java中的线程池顶层接口是Executor接口，ThreadPoolExecutor是这个接口的实现类。

ThreadPoolExecutor有四个构造方法，包含以下参数（5个必须参数，2个可选参数）：
- 必须参数
  - corePoolSize：该线程池中核心线程数最大值，核心线程会一直存在于线程池内，区别于非核心线程
  - maximumPoolSize：该线程池中线程总数最大值，等于核心线程数+非核心线程数
  - keepAliveTime：非核心线程闲置超时时长，非核心线程闲置超时会被销毁
  - unit：keepAliveTime的单位，enum类型，从nanoseconds到days
  - BlockingQueue workQueue：阻塞队列，维护着等待执行的Runnable任务对象，常见对象包括
    - LinkedBlockingQueue
    - ArrayBlockingQueue
    - SynchronousQueue
    - DelayQueue
- 可选参数
  - ThreadFactory threadFactory：线程创建工厂，用于批量创建线程，可指定参数，是否守护线程、线程优先级等
  - RejectedExecutionHandler handler：拒绝处理策略
    - ThreadPoolExecutor.AbortPolicy
    - ThreadPoolExecutor.DiscardPolicy
    - ThreadPoolExecutor.DiscardOldestPolicy
    - ThreadPoolExecutor.CallerRunsPolicy

ThreadPoolExecutor的策略：线程池本身有一个调度线程，这个线程就是用于管理布控整个线程池里的各种任务和事务，类中使用了一些final int常量变量来表示线程池的状态 ，分别为RUNNING、SHUTDOWN、STOP、TIDYING 、TERMINATED
- 线程池创建后处于RUNNING状态
- 调用shutdown()方法后处于SHUTDOWN状态，线程池不能接受新的任务，清除一些空闲worker,不会等待阻塞队列的任务完成
- 调用shutdownNow()方法后处于STOP状态，线程池不能接受新的任务，中断所有线程，阻塞队列中没有被执行的任务全部丢弃。此时，poolsize=0,阻塞队列的size也为0
- 当所有的任务已终止，ctl记录的”任务数量”为0，线程池会变为TIDYING状态。接着会执行terminated()函数
- 线程池处在TIDYING状态时，执行完terminated()方法之后，就会由 TIDYING -> TERMINATED， 线程池被设置为TERMINATED状态

#### 线程池主要的任务处理流程：
- 若当前线程数小于corePoolSize，调用addWorker创建核心线程
- 若不小于，将任务添加到workQueue队列
- 若放入失败，创建非核心线程执行任务
- 如果创建非核心线程失败，则会执行拒绝策略

两次检查线程池状态：
- 在多线程的环境下，线程池的状态是时刻发生变化的。很有可能刚获取线程池状态后线程池状态就改变了。判断是否将command加入workqueue是线程池之前的状态。倘若没有二次检查，万一线程池处于非RUNNING状态（在多线程环境下很有可能发生），那么command永远不会执行

处理流程总结：
1. 线程总数量 < corePoolSize，无论线程是否空闲，都会新建一个核心线程执行任务（让核心线程数量快速达到corePoolSize，在核心线程数量 < corePoolSize时）。注意，这一步需要获得全局锁。
2. 线程总数量 >= corePoolSize时，新来的线程任务会进入任务队列中等待，然后空闲的核心线程会依次去缓存队列中取任务来执行（体现了线程复用）。
3. 当缓存队列满了，说明这个时候任务已经多到爆棚，需要一些“临时工”来执行这些任务了。于是会创建非核心线程去执行这个任务。注意，这一步需要获得全局锁。
4. 缓存队列满了， 且总线程数达到了maximumPoolSize，则会采取上面提到的拒绝策略进行处理。

#### 线程池线程复用

线程池在创建线程时，会将线程封装为工作线程worker，并放入工作线程组中，然后这个工作线程反复从阻塞队列中获取任务去执行

#### 四种常见的线程池

1. newCachedThreadPool
  - 适合执行许多短时间任务，线程复用率高，显著提高性能
  - 线程60s后会回收，不会占据很多资源
2. newFixedThreadPool
  - 核心线程数与线程总数相同，即没有非核心线程
  - 与newCachedThreadPool对比
    - 只创建核心线程，而cached只创建非核心线程
    - 在getTask方法里，如果队列没有线程可取，线程会一直阻塞在LinkedBlockingQueue.take()，线程不会被回收。cached会在60s后回收线程
    - 由于线程不会被回收，Fixed消耗资源更多
    - 都几乎不会触发拒绝策略。Fixed因为阻塞队列很大，cached是因为线程池很大，几乎不会导致线程数量大于最大线程数
3. newSingleThreadExecutor
  - 只有一个核心线程，使用了LinkedBlockingQueue，容量可以很大，所以不会创建非核心线程
  - 所有任务按照先来先执行的顺序执行
4. newScheduledThreadPool
  - 创建一个定长线程池，支持定时及周期性任务执行