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
        * [String Poll](#StringPoll)
        * [new String("abc")]()
    * [三、运算](#运算)
        * [参数传递]()
        * [float与double]
        * [隐式类型转换]
        * [switch]
    * [四、关键字](#关键字)
        * [final]
        * [static]
    * [五、Object通用方法](#Object通用方法)
        * [概览]
        * [equals()]
        * [hashCode()]
        * [toString]
        * [clone()]
    * [六、继承](#继承)
        * [访问权限]
        * [抽象类与接口]
        * [super]
        * [重写与重载]
    * [七、反射](#反射)
    * [八、异常](#异常)
    * [九、泛型](#泛型)
    * [十、注解](#注解)
    * [十一、特性](#特性)
        * java各版本的特性
        * java与C++的区别
        * JRE or JDK
    * [补充](#补充)
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
### String Poll <div id="StringPoll"></div>
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
使用 javap -verbose 进行反编译，得到以下内容：
```java
// ...
Constant pool:
// ...
   #2 = Class              #18            // java/lang/String
   #3 = String             #19            // abc
// ...
  #18 = Utf8               java/lang/String
  #19 = Utf8               abc
// ...

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=3, locals=2, args_size=1
         0: new           #2                  // class java/lang/String
         3: dup
         4: ldc           #3                  // String abc
         6: invokespecial #4                  // Method java/lang/String."<init>":(Ljava/lang/String;)V
         9: astore_1
// ...
```
在Constant Poll中，#19代表字符串字面量，#3代表对象引用#19字符串。在main方法中,0:行创建一个对象，ldc #3将字面量作为Strin对象的构造函数的参数。
以下是String构造函数的源码，可以发现在使用参数构造时，并不是将value数组复制，而是都会指向同一个数组。
```java
public String(String original) {
    this.value = original.value;
    this.hash = original.hash;
}
```
## 三、运算 <div id="运算"></div>
### 参数传递
java的值是以值传递传入方法中的，而不是引用传递。  
在以下代码中，dog是一个指针，指向的是对象的地址。在将一个参数传递到一个方法时，本质上是将参数的地址以值的方式传递到形参中。
```java
public class Dog {

    String name;

    Dog(String name) {
        this.name = name;
    }

    String getName() {
        return this.name;
    }

    void setName(String name) {
        this.name = name;
    }

    String getObjectAddress() {
        return super.toString();
    }
}
```
在方法中该改变对象的字段值会改变原对象的字段值，因为引用的是同一个对象。
```java
class PassByValueExample {
    public static void main(String[] args) {
        Dog dog = new Dog("A");
        func(dog);
        System.out.println(dog.getName());          // B
    }

    private static void func(Dog dog) {
        dog.setName("B");
    }
}
```
但是在方法中将指针引用了其它对象，此时方法里和方法外指向不同的对象，在一个指针改变所指向的对象对另一个指针没有影响。
```java
public class PassByValueExample {
    public static void main(String[] args) {
        Dog dog = new Dog("A");
        System.out.println(dog.getObjectAddress()); // Dog@4554617c
        func(dog);
        System.out.println(dog.getObjectAddress()); // Dog@4554617c
        System.out.println(dog.getName());          // A
    }

    private static void func(Dog dog) {
        System.out.println(dog.getObjectAddress()); // Dog@4554617c
        dog = new Dog("B");
        System.out.println(dog.getObjectAddress()); // Dog@74a14482
        System.out.println(dog.getName());          // B
    }
}
```
[StackOverflow: Is Java “pass-by-reference” or “pass-by-value”?](https://stackoverflow.com/questions/40480/is-java-pass-by-reference-or-pass-by-value)

### float与double
java不能隐式执行类型向下转换，因为这样会使精度降低。
1.1字面量是double类型不能直接复制给float类型，因为这是向下转型。  
`// float f = 1.1;`  
1.1f才是float类型。  
`float f = 1.1f;`

### 隐式类型转换
因为字面量1是int类型，short是精度更高的类型，因此int不能隐式地向下转为short类型。
```java
short s1 = 1;
// s1 = s1 + 1;
```
但是使用+=或++运算符会实现隐式类型转换
```java
s1 += 1;
s1++;
```
上面地语句相当于将s1+1地结果进行了向下转型
`s1 = (short) (s1 + 1);`
### swicth
从java7开始，可以在switch条件语句中使用String对象。
```Java
String s = "a";
switch (s) {
    case "a":
        System.out.println("aaa");
        break;
    case "b":
        System.out.println("bbb");
        break;
}
```
switch不支持long float double，因为switch本来就是为少数几个值的类型进行等值判断设计的，如果数值过于复杂，还是用if语句比较合适。
```java
// long x = 111;
// switch (x) { // Incompatible types. Found: 'long', required: 'char, byte, short, int, Character, Byte, Short, Integer, String, or an enum'
//     case 111:
//         System.out.println(111);
//         break;
//     case 222:
//         System.out.println(222);
//         break;
// }
```

[StackOverflow : Why can't your switch statement data type be long, Java?](https://stackoverflow.com/questions/2676210/why-cant-your-switch-statement-data-type-be-long-java)

## 四、关键字 <div id="关键字"></div>
### final
#### 1. 数据
声明数据为常量，可以是编译时常量，也可以是在运行时被初始化后不可以被改变的常量。
* 对于基本类型，final使数值不变
* 对于引用类型，final使引用不变，也就是不能引用其它对象，但是对象本身可以改变。
```java
final int x = 1;
// x = 2;  // cannot assign value to final variable 'x'
final A y = new A();
y.a = 1;
```
#### 2. 方法
声明方法不能被子类重写。

private方法隐式地被指定为final类型，如果在子类中定义地一个方法与基类中地一个private方法名相同，此时子类方法不是重写基类方法，而是在子类中定义了一个新的方法。
#### 3. 类
声明类不允许被继承。

### static
#### 1. 静态变量
* 静态变量：又称类变量，也就是这个变量是属于类的，类的所有实例都共享静态变量，可以通过类名来访问它，静态变量在内存中只存在一份。
* 实例变量：每创建一个实例就会产生一份实例变量，它与该实例同生共死。
```java
public class A {

    private int x;         // 实例变量
    private static int y;  // 静态变量

    public static void main(String[] args) {
        // int x = A.x;  // Non-static field 'x' cannot be referenced from a static context
        A a = new A();
        int x = a.x;
        int y = A.y;
    }
}
```
#### 2. 静态方法
静态方法在类加载的时候就存在了，它不依赖于任何实例。也就是说静态方法必须有实现，不能是抽象方法。
```java
public abstract class A {
    public static void func1(){
    }
    // public abstract static void func2();  // Illegal combination of modifiers: 'abstract' and 'static'
}
```
只能访问所属类的静态字段和静态方法，方法中不能有this和super，因为这两个关键词与具体对象关联。
```java
public class A {

    private static int x;
    private int y;

    public static void func1(){
        int a = x;
        // int b = y;  // Non-static field 'y' cannot be referenced from a static context
        // int b = this.y;     // 'A.this' cannot be referenced from a static context
    }
}
```
#### 3.静态语句块
静态语句块在类初始化时运行一次。
```java
public class A {
    static {
        System.out.println("123");
    }

    public static void main(String[] args) {
        A a1 = new A();
        A a2 = new A();
    }
}

123
```
#### 4.静态内部类
非静态内部类依赖于外部类的实例，也就是说需要先创建外部类实例，才能用这个实例去创建非静态内部类，而静态内部类不需要。
```java
public class OuterClass {

    class InnerClass {
    }

    static class StaticInnerClass {
    }

    public static void main(String[] args) {
        // InnerClass innerClass = new InnerClass(); // 'OuterClass.this' cannot be referenced from a static context
        OuterClass outerClass = new OuterClass();
        InnerClass innerClass = outerClass.new InnerClass();
        StaticInnerClass staticInnerClass = new StaticInnerClass();
    }
}
```
#### 5. 静态导包
使用静态变量和方法时不再指明ClasssName，从而简化代码，但是降低了代码的可读性。
```java
import static com.xxx.ClassName.*
```
#### 6. 初始化顺序
静态变量和静态语句块优先于实例变量和一般语句块。静态变量和静态语句块初始化的顺序取决于他们在代码中的顺序。

最后才是构造函数的初始化
  
存在继承的情况下，初始化顺序为：
* 父类(静态变量、静态语句块)
* 子类(静态变量、静态语句块)
* 父类(实例变量、普通语句块)
* 父类(构造函数)
* 子类(实例变量、普通语句块)
* 子类(构造函数)

### 五、Object通用方法 <div id="Object通用方法"></div>
#### 概览
```java
public native int hashCode()

public boolean equals(Object obj)

protected native Object clone() throws CloneNotSupportedException

public String toString()

public final native Class<?> getClass()

protected void finalize() throws Throwable {}

public final native void notify()

public final native void notifyAll()

public final native void wait(long timeout) throws InterruptedException

public final void wait(long timeout, int nanos) throws InterruptedException

public final void wait() throws InterruptedException
```
#### equals()
1. 等价关系  
两个对象具有等价关系，需要满足以下五个条件  
I 自反性  
`x.equals(x); //true`  
II 对称性  
`x.equals(y) == y.equals(x);  //true`  
III 传递性
```java
if(x.equals(y) && y.equals(z)) {
    x.equals(z);  //true
}
```
IV 一致性
`x.equals(y) == x.equals(y); //true 多次比较结果一致`  
V 与null比较  
除了null外的任何字符与null比较均为false  
2. 等价相等  
* 对于基本类型，==判断两个值是否相等，基本类型没有equals方法
* 对于引用类型，equals()判断值是否相等，而==判断其是否引用的是同一个对象。







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


* javap
javap 是 JDK 自带的反汇编器，可以查看 java 编译器为我们生成的字节码。通过它，我们可以对照源代码和字节码，从而了解很多编译器内部的工作。