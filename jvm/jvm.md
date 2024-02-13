# JVM内存结构

## 程序计数器

Program Counter Register程序计数器(寄存器)

程序计数器在物理层上是通过寄存器实现的

* 作用：记住下一条jvm指令的执行地址
* 特点
  1. 是线程私有的(每个线程都有属于自己的程序计数器)
  2. 不会存在内存溢出

## 虚拟机栈(默认大小为1024kb)

* 每个线程运行时所需要的内存称为虚拟机栈
* 每个栈由多个栈帧组成，对应着每次方法调用时所占的内存
* 每个线程只能有一个活动栈帧，对应着当前正在执行的那个方法

### 栈内存溢出(StackOverflowError)

* 栈帧过多导致内存溢出
* 栈帧过大导致内存溢出

>java编译工具：
>
>jstack 线程id：可以根据线程id找到有问题的线程进一步定位到有问题的代码行数

## 本地方法栈(Native Method stack)

java代码在完成一些需求时，需要调用一些底层的，如c/c++代码，那么就需要本地方法栈

例如hashCode()方法，就是一个本地方法

```java
public native int hashCode();

    /**
     * Indicates whether some other object is "equal to" this one.
     * <p>
     * The {@code equals} method implements an equivalence relation
     * on non-null object references:
     * <ul>
     * <li>It is <i>reflexive</i>: for any non-null reference value
     *     {@code x}, {@code x.equals(x)} should return
     *     {@code true}.
     * <li>It is <i>symmetric</i>: for any non-null reference values
     *     {@code x} and {@code y}, {@code x.equals(y)}
     *     should return {@code true} if and only if
     *     {@code y.equals(x)} returns {@code true}.
     * <li>It is <i>transitive</i>: for any non-null reference values
     *     {@code x}, {@code y}, and {@code z}, if
     *     {@code x.equals(y)} returns {@code true} and
     *     {@code y.equals(z)} returns {@code true}, then
     *     {@code x.equals(z)} should return {@code true}.
     * <li>It is <i>consistent</i>: for any non-null reference values
     *     {@code x} and {@code y}, multiple invocations of
     *     {@code x.equals(y)} consistently return {@code true}
     *     or consistently return {@code false}, provided no
     *     information used in {@code equals} comparisons on the
     *     objects is modified.
     * <li>For any non-null reference value {@code x},
     *     {@code x.equals(null)} should return {@code false}.
     * </ul>
     *
     * <p>
     * An equivalence relation partitions the elements it operates on
     * into <i>equivalence classes</i>; all the members of an
     * equivalence class are equal to each other. Members of an
     * equivalence class are substitutable for each other, at least
     * for some purposes.
     *
     * @implSpec
     * The {@code equals} method for class {@code Object} implements
     * the most discriminating possible equivalence relation on objects;
     * that is, for any non-null reference values {@code x} and
     * {@code y}, this method returns {@code true} if and only
     * if {@code x} and {@code y} refer to the same object
     * ({@code x == y} has the value {@code true}).
     *
     * In other words, under the reference equality equivalence
     * relation, each equivalence class only has a single element.
     *
     * @apiNote
     * It is generally necessary to override the {@link #hashCode() hashCode}
     * method whenever this method is overridden, so as to maintain the
     * general contract for the {@code hashCode} method, which states
     * that equal objects must have equal hash codes.
     *
     * @param   obj   the reference object with which to compare.
     * @return  {@code true} if this object is the same as the obj
     *          argument; {@code false} otherwise.
     * @see     #hashCode()
     * @see     java.util.HashMap
     */
 
```



## 堆

堆(Heap)

* 通过new关键字，创建的对象都会使用堆内存
* 特点：
  1. 他是线程共享的，堆中的对象需要考虑线程安全的问题
  2. 有垃圾回收机制

### 堆内存溢出(OutOfMemoryError)

代码演示

```java
List<String> list = new ArrayList<>();
try{
    String a = "hello";
    while(true){
        list.add(a);
        a = a + a;
    }
}catch(Throwable e){
    e.printStackTrace()
}
```

### 堆内存诊断

* jps工具：查看当前系统中有哪些java进程
* jmap工具：查看堆内存占用情况(jmap -heap 进程id)
* jconsole工具：图形界面的，多功能的检测工具，可以连续检测

## 方法区

![image-20240206130911899](E:\桌面\Resource\assets\image-20240206130911899.png)



![image-20240206125333513](E:\桌面\Resource\assets\image-20240206125333513.png)

### 内存溢出

* 1.8以前会导致永久代内存溢出
* 1.8以后会导致原空间内存溢出

### 运行时常量池

`二进制字节码包含：类基本信息，常量池，类方法定义包含了虚拟机指令`

* 常量池就是一张表，虚拟机指令根据这张常量表找到要执行的类名，方法名，参数类型，字面量等信息
* 运行时常量，常量池是*.class文件中的，当该类被加载时，它的常量池信息就会放入运行时常量池，并把里面的符号地址变为真实地址

### StringTable

底层是基于哈希表实现，不能扩容

```java
package bean;

public class Temp {
    public static void main(String[] args) {
        String s1 = "a";
        String s2 = "b";
        String s3 = "ab";
        String s4 = s1 + s2;
        System.out.println(s3==s4);//false
        /*
        解释：
        1. s3是在常量池中
        2. s4是new出来的字符串，在堆中
        3. 二者内存空间地址不一样，所以是false
        
        
        */
    }
}

```

执行`javap -v Temp.class`

```bash
Classfile /E:/code/java/temp/Thread/reflect/src/main/java/bean/Temp.class
  Last modified 2024年2月6日; size 780 bytes
  SHA-256 checksum 56c70e4c76c31646a9cb2cfece3728bc1a10f96185b4456e087039d1e60a0541
  Compiled from "Temp.java"
public class bean.Temp
  minor version: 0
  major version: 65
  flags: (0x0021) ACC_PUBLIC, ACC_SUPER
  this_class: #17                         // bean/Temp
  super_class: #2                         // java/lang/Object
  interfaces: 0, fields: 0, methods: 2, attributes: 3
Constant pool://这里是常量池
   #1 = Methodref          #2.#3          // java/lang/Object."<init>":()V
   #2 = Class              #4             // java/lang/Object
   #3 = NameAndType        #5:#6          // "<init>":()V
   #4 = Utf8               java/lang/Object
   #5 = Utf8               <init>
   #6 = Utf8               ()V
   #7 = String             #8             // a
   #8 = Utf8               a
   #9 = String             #10            // b
  #10 = Utf8               b
  #11 = String             #12            // ab
  #12 = Utf8               ab
  #13 = InvokeDynamic      #0:#14         // #0:makeConcatWithConstants:(Ljava/lang/String;Ljava/lang/String;)Ljava/lang/String;
  #14 = NameAndType        #15:#16        // makeConcatWithConstants:(Ljava/lang/String;Ljava/lang/String;)Ljava/lang/String;
  #15 = Utf8               makeConcatWithConstants
  #16 = Utf8               (Ljava/lang/String;Ljava/lang/String;)Ljava/lang/String;
  #17 = Class              #18            // bean/Temp
  #18 = Utf8               bean/Temp
  #19 = Utf8               Code
  #20 = Utf8               LineNumberTable
  #21 = Utf8               main
  #22 = Utf8               ([Ljava/lang/String;)V
  #23 = Utf8               SourceFile
  #24 = Utf8               Temp.java
  #25 = Utf8               BootstrapMethods
  #26 = String             #27            // \u0001\u0001
  #27 = Utf8               \u0001\u0001
  #28 = MethodHandle       6:#29          // REF_invokeStatic java/lang/invoke/StringConcatFactory.makeConcatWithConstants:(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/String;[Ljava/lang/Object;)Ljava/lang/invoke/CallSite;
  #29 = Methodref          #30.#31        // java/lang/invoke/StringConcatFactory.makeConcatWithConstants:(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/String;[Ljava/lang/Object;)Ljava/lang/invoke/CallSite;
  #30 = Class              #32            // java/lang/invoke/StringConcatFactory
  #31 = NameAndType        #15:#33        // makeConcatWithConstants:(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/String;[Ljava/lang/Object;)Ljava/lang/invoke/CallSite;
  #32 = Utf8               java/lang/invoke/StringConcatFactory
  #33 = Utf8               (Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/String;[Ljava/lang/Object;)Ljava/lang/invoke/CallSite;
  #34 = Utf8               InnerClasses
  #35 = Class              #36            // java/lang/invoke/MethodHandles$Lookup
  #36 = Utf8               java/lang/invoke/MethodHandles$Lookup
  #37 = Class              #38            // java/lang/invoke/MethodHandles
  #38 = Utf8               java/lang/invoke/MethodHandles
  #39 = Utf8               Lookup
{
  public bean.Temp();
    descriptor: ()V
    flags: (0x0001) ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 3: 0

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: (0x0009) ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=5, args_size=1
         0: ldc           #7                  // String a
         2: astore_1
         3: ldc           #9                  // String b
         5: astore_2
         6: ldc           #11                 // String ab
         8: astore_3
         9: aload_1
        10: aload_2
        11: invokedynamic #13,  0             // InvokeDynamic #0:makeConcatWithConstants:(Ljava/lang/String;Ljava/lang/String;)Ljava/lang/String;
        16: astore        4
        18: return
      LineNumberTable:
        line 5: 0
        line 6: 3
        line 7: 6
        line 8: 9
        line 9: 18
}
SourceFile: "Temp.java"
BootstrapMethods:
  0: #28 REF_invokeStatic java/lang/invoke/StringConcatFactory.makeConcatWithConstants:(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/String;[Ljava/lang/Object;)Ljava/lang/invoke/CallSite;
    Method arguments:
      #26 \u0001\u0001
InnerClasses:
  public static final #39= #35 of #37;    // Lookup=class java/lang/invoke/MethodHandles$Lookup of class java/lang/invoke/MethodHandles
```

* StringTable特性
  1. 常量池中的字符串仅是符号，第一次用到时才变为对象
  2. 利用串池的机制，来避免重复创建字符串对象
  3. 字符串拼接原理是StringBuilder(1.8)
  4. 字符串常量拼接的原理是编译期优化
  5. 可以使用intern方法，可以主动将串池中还没有的字符串对象放入串池(1.8尝试将字符串对象放入串池，如果有则并不会放入，如果没有，则会把串池中的对象返回)
* StringTable位置
  1. 1.6之前，StringTable在永久代内存空间中
  2. 1.8以后，StringTable在堆内存空间中
* StringTable垃圾回收
  1. 超过阈值，会触发GC垃圾回收
* StringTable性能调优
  1. StringTable底层是基于哈希表实现的，所以StringTable调优，就是优化底层哈希表的大小，以此减少哈希碰撞
  2. 参数`-XX:StringTableSize = 桶个数`
  3. 考虑将字符串对象是否入池(包含很多重复的字符串)

## 直接内存

定义：(操作系统内存)

	1. 常见与NIO操作，用于数据缓冲区
	1. 分配回收成本较高，但读写性能高
	1. 不受JVM内存回收管理

![image-20240206162911375](E:\桌面\Resource\assets\image-20240206162911375.png)

### 内存分配和释放原理

是通过一个对象`unsafe`来分配和释放内存的，并且回收需要主动调用freeMemory方法

ByteBuffer的实现类内部，使用了`Cleaner(虚引用)`来检测ByteBuffer对象，一旦该对象被GC垃圾回收机制回收，那么就会由`ReferenceHandler`线程通过`Cleaner`的`clean`方法调用freeMemory方法来释放直接内存

# GC垃圾回收

### 如何判断对象可以回收

* 引用计数法

  如果有对象引用计数加一，没有对象引用，计数减一，如果计数为零，则回收

  但是如果存在循环引用，即A对象引用B对象，B对象引用A对象，会造成内存泄漏

* 可达性分析算法

  1. java虚拟机中的垃圾回收器采用可达性分析来探索所有存活的对象

  2. 扫描堆中的对象，看是否能够沿着GC Root对象为起点的引用链找到该对象，找不到，表示可以回收

  3. 哪些对象可以作为GG Root？

     1. System Class

        例如：Object，String，HashMap等

     2. Native Stack

     3. Thread

     4. Busy Monitor

  

  

* 四种引用

  * 强引用：new的对象，赋值的对象

    ```java
    Object obj= new Object()//new 的对象都是是强引用
    ```

    只要强引用存在，垃圾回收器将永远不会回收被引用的对象，哪怕内存不足时，JVM也会直接抛出OutOfMemoryError，不会去回收。如果想中断强引用与对象之间的联系，可以显示的将强引用赋值为null，这样一来，JVM就可以适时的回收对象了

  * 软引用

    ```java
    Object obj= new Object()
    SoftReference sr = new SoftReference<>(obj);//obj就是软引用
    ```

    在内存足够的时候，软引用对象不会被回收，只有在内存不足时，系统则会回收软引用对象，如果回收了软引用对象之后仍然没有足够的内存，才会抛出内存溢出异常。这种特性常常被用来实现缓存技术，比如网页缓存，图片缓存等。

  * 弱引用

    ```java
    Object obj= new Object()
    WeakReference wr = newWeakReference<>(obj);//obj就是软引用
    ```

    弱引用的引用强度比软引用要更弱一些，无论内存是否足够，只要 JVM 开始进行垃圾回收，那些被弱引用关联的对象都会被回收。在 JDK1.2 之后，用 java.lang.ref.WeakReference 来表示弱引用。

  * 虚引用

    ```java
    Object obj= new Object()
    PhantomReference pr = new PhantomReference <>(obj);//obj就是软引用
    ```

    虚引用是最弱的一种引用关系，如果一个对象仅持有虚引用，那么它就和没有任何引用一样，它随时可能会被回收，在 JDK1.2 之后，用 PhantomReference 类来表示，通过查看这个类的源码，发现它只有一个构造函数和一个 get() 方法，而且它的 get() 方法仅仅是返回一个null，也就是说将永远无法通过虚引用来获取对象，虚引用必须要和 ReferenceQueue 引用队列一起使用。

  * 终结器引用

    无需手动编码，但其内部配合引用队列使用，在垃圾回收时，终结器引用入队(被引用对象暂时没有被回收)，再由Finalizer线程通过终结引用找到被引用对象并调用它的finalize方法，第二次GC时才能回收被引用的对象

  

### 垃圾回收算法

* 标记清除

  **标记：标记的过程其实就是，遍历所有的GC Roots，然后将所有GC Roots可达的对象标记为存活的对象。**

  **清除：清除的过程将遍历堆中所有的对象，将没有标记的对象全部清除掉。**

  * 优点：速度快
  * 缺点：容易产生内存碎片

* 标记整理

  **标记：标记-整理算法从根对象开始遍历整个对象图，标记所有与根对象可达的对象，即被引用的对象。**

  **整理：标记-整理算法会对内存空间进行整理，将所有存活的对象移动到一端，而未标记的对象则被认为是垃圾对象。**

  * 优点：没有产生"碎片"
  * 缺点：速度慢

* 复制

  将活着的内存空间分为两块，每次只使用一块，在垃圾回收时将正在使用的内存块中的存活的对象复制到未被使用的内存块，之后清除正在使用的内存块中的所有对象，交换两块内存空间，完成垃圾回收

  * 优点：没有标记清除过程，运行高效，不会出现“碎片”问题
  * 缺点：需要两倍的内存空间

### 分代垃圾回收

![image-20240207165746554](E:\桌面\Resource\assets\image-20240207165746554.png)

老年代：存放长时间使用的对象

新生代：存放用完就可以丢弃的对象

* MinorGC

  每次产生的新的对象在伊甸园区，如果进行了垃圾回收，那么会进行遍历所有对象，释放所有的的未被使用的对象，将使用的对象通过复制的方式，复制到幸存区S1，并进行年龄+1操作，表示该对象每次回收都没有被回收，当该值超过一个阈值时，就会将该对象放入老年代区，表示该对象可能会被长时间使用

  2. minor gc 会引发stop the world：进行垃圾回收时，会暂停其他的用户线程，等垃圾回收后，用户线程才恢复运行
  3. 当对象寿命超过阈值时，会晋升到老年区，最大寿命是15(4bit)

* FullGC

  老年代空间都不足时，先触发Minorgc，如果之后内存空间仍然不足，则会触发FullGC，来对内存空间进行清理，STW(stop the world)的时间更长

相关VM的参数

![image-20240207212716437](E:\桌面\Resource\assets\image-20240207212716437.png)

大对象：如果说某个对象的大小超过了新生代内存大小，那么会将这个对象直接晋升到老年代内存块，不会发生GC回收

### 垃圾回收器

* 串行

  1. 单线程的垃圾回收器

  2. 适用于堆内存较小的场景，个人电脑

  3. ```java
     打开串行垃圾回收器
     -XX:+UseSerialGC=Serial(新生代：复制算法) + SerialOld(老年代：标记整理算法)
     ```

  4. ![image-20240207215719855](E:\桌面\Resource\assets\image-20240207215719855.png)

* 吞吐量优先(1.8默认的垃圾回收器)

  1. 多线程的垃圾回收器

  2. 适用于堆内存较大的场景，需要多核CPU的支持

  3. 让单位时间内STW的时间最短

  4. 在进行垃圾回收时会占满CPU

  5. ![image-20240207220100553](E:\桌面\Resource\assets\image-20240207220100553.png)

  6. ```bash
     -XX:parallelGCThreads=n //指定线程数量
     -XX:UseAdaptiveSizePolicy //采用自适应的大小调整策略，调整新生代的大小
     -XX:UseParallelGC //开启吞吐量优先垃圾回收器
     //ratio默认为99，此时值为0.01，只能由1%的时间用来进行垃圾回收(100min,只能有1min进行垃圾回收)
     -XX:GCTimeRatio=ratio //(公式：1/1+ratio)
     ```

* 响应时间优先(基于标记清除算法)

  1. 多线程的垃圾回收器

  2. 适用于堆内存较大的场景，需要多核CPU的支持

  3. 尽可能的让单次STW的时间最短

  4. ![image-20240207221138878](E:\桌面\Resource\assets\image-20240207221138878.png)

  5. ```bash
     -XX:UseConcMarkSweepGC
     ```

### G1(JDK9之后的默认的垃圾回收器)

定义：Garbage First

1. 适用场景

* 同时注重吞吐量和低延时，默认的暂时目标是200ms
* 超大堆内存，会将堆划分为多个大小相等的Region
* 整体上是标记+整理算法，两个区域之间是复制算法

2. 相关参数

```bash
-XX:+UseG1GC
-XX:G1HeapRegionSize=size //设置Region的大小(1,2,4,8,16)
-XX:MaxGCPauseMillis=time //暂停目标时间
```

3. G1垃圾回收阶段

   ![image-20240207223630550](E:\桌面\Resource\assets\image-20240207223630550.png)

   3.1Young Collection(新生代)

   ​	会STW

   ​	会将幸存的对象，复制到幸存区，之后新生代内存满了，会将部分常用的对象复制到老年代内存

   3.2Young Collection+Concurrent Mark(新生代+并发标记)

   ​	在YoungGC时会进行GC Root的初始标记

   ​	老年代占用堆空间比例达到阈值时(默认为45%)，进行并发标记(不会STW)

   3.3Mixed Collection(混合收集)

   ​	会对伊甸园，新生区，老年区进行全面垃圾回收

   ​	最终标记会STW

   ​	拷贝存活会STW

   

   

   

4. Young Collection 跨代引用

   新生代回收的跨代引用(老年代引用新生代)问题

   

### 垃圾回收调优

1. 调优领域
   1. 内存
   2. 锁竞争
   3. cpu占用
   4. io
2. 最快的GC是不发生GC
   1. 查看FullGC前后内存占用，考虑几个问题
      1. 是不是数据太多了
      2. 数据表示是否太臃肿
      3. 是否存在内存泄漏
3. 新生代调优
   1. 所有new操作的内存分配非常廉价
   2. 死亡对象的回收代价为零
   3. 大部分对象用过即死
   4. minorgc时间远远低于fullgc
   5. 所以新生代内存块的大小应该设置为堆大小的1/4~1/2最佳，过大的话，会导致老年代内存过小，发生频繁 FullGC
   6. 幸存区要足够大到能保留当前活跃对象和需要晋升的对象
   7. 晋升阈值配置要得当，让长时间存活对象能尽快晋升
4. 老年代调优(CMS)
   1. CMS的老年代内存越大越好
   2. 将老年代内存预设调至1/4~1/3

# Class字节码与类加载器

# 类文件结构

![image-20240213133613670](E:\桌面\Resource\assets\image-20240213133613670.png)

```bash
0000000 ca fe ba be 00 00 00 34 00 23 0a 00 06 00 15 09
0000020 00 16 00 17 08 00 18 0a 00 19 00 1a 07 00 1b 07
```

1. majic

   0~3字节，表示他是否是class类型的文件

   ca fe ba be

2. version

   4~7字节，表示类的版本00 34(52) 表示是java8(53表示jdk9)

3. 常量池

   8~9字节，表示常量池的长度，00 23(35)表示常量池有#1~#34项，注意#0项不计入，也没有值

   第#1项0a表示一个Method信息，00 06 和 00 15(21)表示它引用了常量池中的#6和#21项来获得这个方法的所属类和方法名

   第#2项09表示一个Filed信息，00 16(22)和00 17(23)表示它引用了常量池中的#22和#23项来获得这个方法的所属类和方法名













# JIT compiler 即时编译器







