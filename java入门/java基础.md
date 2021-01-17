# java基础
* java基础 
    * [一、数据类型](#数据类型)
        * [基本类型](#基本类型) 
        * [包装类型](#包装类型)
        * [缓冲池](#缓冲池)
    * [二、String](#String)
        * [概览](#概览)
        * [不可变的好处](#不可变的好处)
        * [String,stringBuffer and StringBuilder](#String)
        * [String Poll](#poll)
        * [new String("abc")](#)

## 一、数据类型 <div id="数据类型"></div>
### 基本类型
    * byte 8
    * char 16
    * short 16
    * int 32
    * float 32
    * long 64 
    * double 64
    * boolean ~

boolean只有两个值，true和false,可以用一个bit来表示，但是具体大小没有明确规定。JVM在编译时将boolean转换为int类型，会用1表示true,0表示false。JVM支持boolean数组，但是通过读写byte数组来实现。

* [Primitive Data Types](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html)
* [The Java® Virtual Machine Specification](https://docs.oracle.com/javase/specs/jvms/se8/jvms8.pdf)
### 包装类型
基本类型都有对应的包装类型，基本类型与对应的包装类型转换通过拆箱与装箱的方式完成。
```java
Integer x = 2    //装箱，调用Integer.valueOf(2)
int y = x        //拆箱，调用x.intValue()
```
* [Autoboxing and Unboxing](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)

### 缓冲池
new Integer(123) 与 Integer.valueOf(123)的区别在于：
* new Integer(123)每次都会新建一个对象
* Integer.valueOf(123)使用缓冲池中的对象，多次调用会获取同一个对象的引用。
```java
Integer x = new Integer(123);
Integer y = new Integer(123);
System.out.println(x == y)  //false
x = Integer.valueOf(123);
y = Integer.valueOf(123);
System.out.println(x == y) //true
```
valueOf的方法比较简单，就是判断值是否存在缓存池中，如果是就直接 返回缓存中中的内容。
```java
public static Integer valueOf(int i) {
    if(i >= IntegerCache.low && i <= IntegerCache.high) {
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
    }
}
```
在java8中缓冲池的大小默认为-128~127.
```java
static final int low = -128;
static final int high;
static final Integer cache[];

static {
    // high value may be configured by property
    int h = 127;
    String integerCacheHighPropValue =
        sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
    if (integerCacheHighPropValue != null) {
        try {
            int i = parseInt(integerCacheHighPropValue);
            i = Math.max(i, 127);
            // Maximum array size is Integer.MAX_VALUE
            h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
        } catch( NumberFormatException nfe) {
            // If the property cannot be parsed into an int, ignore it.
        }
    }
    high = h;

    cache = new Integer[(high - low) + 1];
    int j = low;
    for(int k = 0; k < cache.length; k++)
        cache[k] = new Integer(j++);

    // range [-128, 127] must be interned (JLS7 5.1.7)
    assert IntegerCache.high >= 127;
}
```
编译器在装箱时会自动调用valueOf()方法，因此多个值相同且在缓冲池范围内的Integer实例使用自动装箱来创建，会引用相同的对象。
```java
Integer x = 123;
Integer y = 123;
System.out.println(x == y )   //true
```
基本类型对应的缓冲池如下：
* boolean true ans false
* all byte values
* short values between -127 to 128
* int as same as short
* char in the range \u0000 to \u007F
在使用这些基本类型的包装类型时，如果对应的值在缓冲池范围内，那么就可以直接使用缓冲池中的对象。

在jdk1.8的所哟类型的缓冲池中，Integer类型的IntegerCache很特殊，这个缓冲池的下界是-128，上界是127.但这个上界是可调的。在启动jvm时，通过-XX:AutoBoxCacheMax=<size> 指定缓冲池的大小，该选项在JVM初始化时会形成一个java.lang.IntegerCache.high的对象，IntegerCache初始化缓冲池时会读取该属性来决定上界。

[StackOverflow : Differences between new Integer(123), Integer.valueOf(123) and just 123](https://stackoverflow.com/questions/9030817/differences-between-new-integer123-integer-valueof123-and-just-123)

## 二、String <div id="String"></div>
### 概览
String被声明为final,因此它不可被继承。（基本类型的包装类也不可以被继承）
在java8中，String内部使用char数组存储数据。
```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
}
```
java9之后，String使用byte字符来存储数组，并使用coder来表示使用哪种编码方式。
```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final byte[] value;

    /** The identifier of the encoding used to encode the bytes in {@code value}. */
    private final byte coder;
}
```
value数组被声明为final，这意味着数组生成后不能再引用其它数组。并且String内部没有改变数组的方法，因此可以保证String不可变。

#### 不可变的好处
1. 可以缓存hash值，比如String做HashMap的key值。不可变的特性可以使得hash值也不可变，因此只需计算一次。
2. String Poll的需要
如果一个String对象创建过来，那么就会从String Poll获得该对象的引用。只有String是不可变的，才能使用String Poll。
3. 安全性
String作为参数，不可变性可以保证参数不变。比如String做网络连接的参数是可变的，再网络连接中String被改变了。那么改变String的那一方以为现在连接的是其它主机。实际上并不是。
4. 线程安全
String不可变性天生具备线程安全，可以在多个线程中安全地使用。

[Program Creek : Why String is immutable in Java?](https://www.programcreek.com/2013/04why-string-is-immutable-in-java/)

### String,StringBuilder and StringBuffer <div id="String"></div>
1. 可变性
* String不可变性
* StringBuilder和StringBuffer可变
2. 线程安全
* String不可变性天生具备线程安全
* StringBuffer线程安全，内部使用synchronized
* StringBuilder不是线程安全的。

[StackOverflow : String, StringBuffer, and StringBuilder](https://stackoverflow.com/questions/2971315/string-stringbuffer-and-stringbuilder)
### String Poll
字符串常量池保存着所有字符串字面量，这些字面量在编译时就确定，不仅如此，还可以使用String的intern()方法在运行过程中将字符串添加到String Poll中。
当一个字符串调用intern()方法时，当String Poll中已经有与该字符串字面量相同的字符串(equals()方法确定)时，就会返回String Poll中该字符串的引用，否则，就会在String Poll中添加一个字符串，并返回这个新字符串的引用。
下面示例中，使用new String()的方式和使用intern()方法创建，可以确定后者产生的同一字面量的字符指向同一字符的引用。
```java
String s1 = new String("aaa");
String s2 = new String("aaa");
System.out.println(s1 == s2);           // false
String s3 = s1.intern();
String s4 = s2.intern();
System.out.println(s3 == s4);           // true
```
如果使用下面的方式创建字面量，会自动放入String Poll中。
```java
String s5 = "bbb";
String s6 = "bbb";
System.out.println(s5 == s6);  // true
```
在java7之前，String Poll被放在运行时常量池中，它属于永久代。在java7之后被放到了对中。这是因为永久代空间有限，如果放入太多字符串会导致OutOfMemoryErro错误。

### new String("abc")
使用这种方式会创建两个对象(前提是String Poll中没有字面量"abc")
* "abc"属于字面量，因此在编译时期，会在String Poll中创建一个对象，指向这个字面量。
* new会在堆中创建一个字符串对象。
创建一个测试类，其main方法使用这种方式创建字符串对象
```java
public class NewStringTest {
    public static void main(String[] args) {
        String s = new String("abc");
    }
}
```






### 补充：<div id="补充"></div>
* 年轻代、老年代、永久代
年轻代用来存放新近创建的对象，尺寸随堆大小的增大和减小而相应的变化，连续空间。年青代的特点是对象更新速度快，在短时间内产生大量的“死亡对象”；使用复制清除算法和并行收集器进行垃圾回收。对年轻代的垃圾回收称作初级回收 (Minor GC)。

老年代里面的对象几乎个个都是从年轻代熬过来的，它们是不会那么容易就 “死掉” 了的，不连续的内存空间。对老年代的垃圾回收称作Full GC，其发生的次数不会有 Minor GC 那么频繁，并且做一次 Full GC 要比进行一次 Minor GC 的时间更长。

永久代存放JVM运行时使用的类，永久代同样包含了Java SE库的类和方法，永久代并不是Java堆内存的一部分。永久代的对象在Full GC时进行垃圾收集。

Jvm区域总体分两类，heap区和非heap区。

heap区又分：Eden Space（伊甸园）、Survivor Space(幸存者区)、Tenured Gen（老年代-养老区）。

非heap区又分：Code Cache(代码缓存区)、Perm Gen（永久代）、Jvm Stack(java虚拟机栈)、Local Method Statck(本地方法栈)。

将对象分成不同类别，其实也是按其生命周期设置合适的内存结构即对应的回收策略，是解耦合的一种体现。

[什么是年轻代、老年代和永久代？](https://www.sohu.com/a/394418289_120175716)
