# [java基础](#java基础)
* java基础 <div id="java基础"></div>
    * [一、数据类型](#数据类型)
        * [基本类型](#基本类型) 
        * [包装类型](#包装类型)
        * [缓冲池](#缓冲池)
    * [二、String](#String)
        * [概览](#概览)
        * [不可变的好处](#不可变的好处)
        * [String,stringBuffer and StringBuilder]()
        * [String Poll](#StringPoll)
        * [new String("abc")](#abc)
    * [三、运算](#运算)
        * [参数传递]()
        * [float与double]
        * [隐式类型转换]
        * [switch]
    * [四、关键字](#关键字)
        * [final](#final)
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

### String,StringBuilder and StringBuffer <span id="String"> </span>
1. 可变性
* String不可变性
* StringBuilder和StringBuffer可变
2. 线程安全
* String不可变性天生具备线程安全
* StringBuffer线程安全，内部使用synchronized
* StringBuilder不是线程安全的。

[StackOverflow : String, StringBuffer, and StringBuilder](https://stackoverflow.com/questions/2971315/string-stringbuffer-and-stringbuilder)
### StringPoll <div id="StringPoll"></div>
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
* 对于引用类型，equals()判断引用的对象是否等价，而==判断其是否引用的是同一个对象。  
```java
Integer x = new Integer(1);
Integer y = new Integer(1);
System.out.println(x.equals(y)); // true
System.out.println(x == y);      // false
```
3. 实现  
* 检查是否为同一对象的引用，如果是，返回true
* 检查时同一类型，如果不是则返回false
* 将Object进行转型
* 判断每个关键域是否相等
```java
public class EqualExample {

    private int x;
    private int y;
    private int z;

    public EqualExample(int x, int y, int z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        EqualExample that = (EqualExample) o;

        if (x != that.x) return false;
        if (y != that.y) return false;
        return z == that.z;
    }
}
```

#### hashCode()  
hashCode返回哈希值，而equals()方法判断两个对象是否等价。等价的两个对象散列值一定相同，但是散列值相同的两个对象不一定等价，因为计算哈希值具有随机性，两个不相同的值可能具有相同的哈希值。  
在覆盖equals()方法时应当覆盖hashCode()方法，保证等价的两个对象哈希值也相同。  
HashSet和HashMap使用hashCode()方法来计算对象存储的位置，因此在将对象保存到集合类中时，需要让对应的类实现hashCode()方法。  
下面的代码中，创建了两个对象，并将他们添加到HashSet中。我们希望将这两个对象是相同的，因而仅有一个对象添加到集合。但是EqualExample没有hashCode方法，因此这两个值哈希值是不同的，他们都被添加到Set中。
```java
EqualExample e1 = new EqualExample(1, 1, 1);
EqualExample e2 = new EqualExample(1, 1, 1);
System.out.println(e1.equals(e2)); // true
HashSet<EqualExample> set = new HashSet<>();
set.add(e1);
set.add(e2);
System.out.println(set.size());   // 2
```
理想的哈希函数应当具有均匀性，即不相等的对象哈希值均匀地分布到所有可能的哈希值上。这就要求哈希把所有域的值考虑进来，每个域当成R进制上的一个位，然后组成一个R进制的整数。
R值一般取31，它是一个奇素数，如果是偶数的话，当乘法出现溢出，信息就会丢失。因为与2相乘相当于左移一位，最左边的数会丢失。并且一个数与31相乘可以转换为移位和减法，`31*x == (x<<5)-x`,编辑器会自动进行这个优化。
```java
@Override
public int hashCode() {
    int result = 17;
    result = 31 * result + x;
    result = 31 * result + y;
    result = 31 * result + z;
    return result;
}
```
#### toString()
默认返回toStringExample@4554617c这种形式，其中@后面的是散列码的无符号十六进制表示。
```java
public class ToStringExample {

    private int number;

    public ToStringExample(int number) {
        this.number = number;
    }
}
//**************
ToStringExample example = new ToStringExample(123);
System.out.println(example.toString());

//**************************
ToStringExample@4554617c
```
#### clone()
1. cloneable
clone是Object的projected方法，它不是public类型，一个类如果不显式地重写clone类，其他类就不能直接去调用该实例类的clone方法。
```java
public class CloneExample {
    private int a;
    private int b;
}
/*********************************/
CloneExample e1 = new CloneExample();
// CloneExample e2 = e1.clone(); // 'clone()' has protected access in 'java.lang.Object'
```
重写clone()得到以下实现：
```java
public class CloneExample {
    private int a;
    private int b;
    protected CloneExample clone() {
        return (CloneExample)super.clone();
    }
}
/**********************************/
CloneExample e1 = new CloneExample();
try {
    CloneExample e2 = e1.clone();
} catch (CloneNotSupportedException e) {
    e.printStackTrace();
}
/**************************************/
java.lang.CloneNotSupportedException: CloneExample
```
以上抛出了CloneNotSupportedException,这是因为CloneExample没有实现cloneable接口。
应该注意的是，clone()方法并不是cloneable的接口方法，而是Object的一个projected方法。cloneable接口只是规定，如果一个类没有实现cloneable接口又调用了clone()方法，就会抛出CloneNotSupportedException.
```java
public class CloneExample implements Cloneable {
    private int a;
    private int b;

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```
2. 浅拷贝  
拷贝对象和原始对象的引用类型引用同一个对象。
```java
public class ShallowCloneExample implements Cloneable {

    private int[] arr;

    public ShallowCloneExample() {
        arr = new int[10];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = i;
        }
    }

    public void set(int index, int value) {
        arr[index] = value;
    }

    public int get(int index) {
        return arr[index];
    }

    @Override
    protected ShallowCloneExample clone() throws CloneNotSupportedException {
        return (ShallowCloneExample) super.clone();
    }
}
/***************************************/
ShallowCloneExample e1 = new ShallowCloneExample();
ShallowCloneExample e2 = null;
try {
    e2 = e1.clone();
} catch (CloneNotSupportedException e) {
    e.printStackTrace();
}
e1.set(2, 222);
System.out.println(e2.get(2)); // 222
```
3. 深拷贝
拷贝对象和原始对象的引用类型引用不同类型。
```java
public class DeepCloneExample implements Cloneable {

    private int[] arr;

    public DeepCloneExample() {
        arr = new int[10];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = i;
        }
    }

    public void set(int index, int value) {
        arr[index] = value;
    }

    public int get(int index) {
        return arr[index];
    }

    @Override
    protected DeepCloneExample clone() throws CloneNotSupportedException {
        DeepCloneExample result = (DeepCloneExample) super.clone();
        result.arr = new int[arr.length];
        for (int i = 0; i < arr.length; i++) {
            result.arr[i] = arr[i];
        }
        return result;
    }
}
/**************************************/
DeepCloneExample e1 = new DeepCloneExample();
DeepCloneExample e2 = null;
try {
    e2 = e1.clone();
} catch (CloneNotSupportedException e) {
    e.printStackTrace();
}
e1.set(2, 222);
System.out.println(e2.get(2)); // 2
```
4. clone()的替代方案
使用clone()既复杂又有风险，它会抛出异常，并且还需要类型转换。Effective Java 书上讲到，最好不要去使用 clone()，可以使用拷贝构造函数或者拷贝工厂来拷贝一个对象。
```java
public class CloneConstructorExample {

    private int[] arr;

    public CloneConstructorExample() {
        arr = new int[10];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = i;
        }
    }

    public CloneConstructorExample(CloneConstructorExample original) {
        arr = new int[original.arr.length];
        for (int i = 0; i < original.arr.length; i++) {
            arr[i] = original.arr[i];
        }
    }

    public void set(int index, int value) {
        arr[index] = value;
    }

    public int get(int index) {
        return arr[index];
    }
}
/*******************************************/
CloneConstructorExample e1 = new CloneConstructorExample();
CloneConstructorExample e2 = new CloneConstructorExample(e1);
e1.set(2, 222);
System.out.println(e2.get(2)); // 2
```
### 六、 继承  
#### 访问权限  
Java中有三个权限修饰符，private,protected,public,如果不加修饰，表示包级可见。  
可以对类或类中的成员(方法或字段)加上访问修饰符。
* 类表示其它类可以用这个类创建实例对象
* 成员可见表示其它类可以用这个类的实例对象访问到该成员
protected用于修饰成员，表示在继承体系中成员对于子类可见，但是在这个访问修饰符对于类没有意义。  
设计良好的模块会隐藏实现的细节，把它的API和具体实现细节清晰地分隔开。模块间只通过API之间通信，一个模块不需要知道其它模块地内部工作情况。这个概念被称为信息隐藏或封装。因此访问权限应当尽可能地使每个类或者成员不被外界访问。如果子类重写了父类方法，那么子类中该方法地访问权限不允许低于父类地访问级别。这是确保可以使用父类实例地地方都可以使用子类实例去代理，即满足里氏替换原则。  
字段绝不能使公有的，因为这样地话就失去了对该字段修改行为地控制，客户端可以随意对其进行修改。例如下面地例子中，AccessExample拥有id公有字段，如果在某个时刻，我们想要使用int存储id字段，就要修改所有客户端代码。 
```java
public class AccessExample {
    public String id;
}
```
可以使用公有的getter和setter方法来替换公有字段，这样的话可以控制对字段的修改行为。
```java
public class AccessExample {

    private int id;

    public String getId() {
        return id + "";
    }

    public void setId(String id) {
        this.id = Integer.valueOf(id);
    }
}
```
但是也有例外，如果是包级私有类或者私有嵌套类，那么直接暴露成员不会有特别大的影响。
```java
public class AccessWithInnerClassExample {

    private class InnerClass {
        int x;
    }

    private InnerClass innerClass;

    public AccessWithInnerClassExample() {
        innerClass = new InnerClass();
    }

    public int getValue() {
        return innerClass.x;  // 直接访问
    }
}
```
### 抽象类与接口
1. 抽象类  
抽象类和抽象方法都是用关键词abstract声明。如果一个类中有抽象方法，，这个类必须是抽象类。抽象类和普通类最大的区别是，抽象类不可以被实例化，只能被继承。  
```java
public abstract class AbstractClassExample {

    protected int x;
    private int y;

    public abstract void func1();

    public void func2() {
        System.out.println("func2");
    }
}
/****************************************/
public class AbstractExtendClassExample extends AbstractClassExample {
    @Override
    public void func1() {
        System.out.println("func1");
    }
}
/*****************************/
// AbstractClassExample ac1 = new AbstractClassExample(); // 'AbstractClassExample' is abstract; cannot be instantiated
AbstractClassExample ac2 = new AbstractExtendClassExample();
ac2.func1();
```
2. 接口  
接口是抽象类的延申，在java8之前它可以看成一个完全抽象的类，也就是说它不能有任何的方法实现。  
从java8开始，接口也可以实现默认方法，这是因为不可以实现默认方法的接口的维护成本太高了。接口及想要添加新的方法，就要修改所有实现了该接口的类，让他们实现新增的方法。  
接口的成员(字段+方法)默认都是public的，并且不允许定义为private或者protected。从java9开始，允许将方法定义为private，这样就能定义某些复用的代码又不会把方法暴露出去。  
接口的字段都是static和final的。  
```java
public interface InterfaceExample {

    void func1();

    default void func2(){
        System.out.println("func2");
    }

    int x = 123;
    // int y;               // Variable 'y' might not have been initialized
    public int z = 0;       // Modifier 'public' is redundant for interface fields
    // private int k = 0;   // Modifier 'private' not allowed here
    // protected int l = 0; // Modifier 'protected' not allowed here
    // private void fun3(); // Modifier 'private' not allowed here
}
/***********************************/
public class InterfaceImplementExample implements InterfaceExample {
    @Override
    public void func1() {
        System.out.println("func1");
    }
}
/************************************/
// InterfaceExample ie1 = new InterfaceExample(); // 'InterfaceExample' is abstract; cannot be instantiated
InterfaceExample ie2 = new InterfaceImplementExample();
ie2.func1();
System.out.println(InterfaceExample.x);
```
3. 比较  
* 从设计层面上看，抽象类提供了一种IS-A关系，满足里氏替换原则，所有父类可以被子类替换。接口则是一种LIKE-A关系，它只是提供一种实现方法实现契约，并不要求接口和实现接口的类具有IS-A关系。
* 从使用上看，一个类不可以继承多个抽象类，但是可以实现多个接口。
* 接口的字段只能是static和final类型，而抽象类没有这种限制。
* 接口的成员只能是Public的，而抽象类的成员可以有多种访问权限。  
4. 使用选择：  
使用接口
* 需要让不相关的类都实现一个方法，例如不相关的类都可以实现Compareable接口中的compareTo()方法。
* 需要使用多重继承  
使用抽象类  
* 需要在几个相关的类中共享代码
* 需要控制继承来的成员的访问权限
* 需要继承非静态和非常量字段  
很多情况下，接口的使用优先于抽象类。因为接口没有抽象类严格的层次结构要求，可以灵活地为类添加方法。Java8开始可以实现接口默认方法，使得修改接口的成本很低。  
* [Abstract Methods and Classes](https://docs.oracle.com/javase/tutorial/java/IandI/abstract.html)
* [深入理解 abstract class 和 interface](https://developer.ibm.com/zh/articles/l-javainterface-abstract/)
* [When to Use Abstract Class and Interface](https://dzone.com/articles/when-to-use-abstract-class-and-intreface)
* [Java 9 Private Methods in Interfaces](https://www.journaldev.com/12850/java-9-private-methods-interfaces)  
#### super 
* 访问父类的构造函数：可以使用super来访问父类的构造函数，从而委托父类完成初始化工作。子类都需要使用父类构造函数完成初始化，一般调用父类的默认构造函数，如果调用父类其它构造函数，就可以使用super()函数
* 访问父类的成员：如果子类重写了父类的方法，使用super()关键字来使用父类的方法实现。  
```java
public class SuperExample {

    protected int x;
    protected int y;

    public SuperExample(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public void func() {
        System.out.println("SuperExample.func()");
    }
}
/****************************************/
public class SuperExtendExample extends SuperExample {

    private int z;

    public SuperExtendExample(int x, int y, int z) {
        super(x, y);
        this.z = z;
    }

    @Override
    public void func() {
        super.func();
        System.out.println("SuperExtendExample.func()");
    }
}
/*******************************************/
SuperExample e = new SuperExtendExample(1, 2, 3);
e.func();
/*******************************************/
SuperExample.func()
SuperExtendExample.func()
```
### 重写与重载
1. 重写(@Override)
存在于继承体系中，指子类实现了一个与父类在方法声明上完全相同的一个方法。  
为了满足里氏替换原则，重写要满足以下三个限制：
* 子类方法的访问权限必须大于等于父类方法
* 子类方法的返回类型必须是父类方法返回类型或为其子类型
* 子类方法抛出异常类型必须是父类方法抛出类型或为其子类型  
使用@Override注解，可以让编译器帮忙检查是否满足上述三个限制条件。
下面的示例中Subclass为Superclass的子类，Subclass重写了Superclass的func()方法。其中：
* 子类方法的访问权限为public，大于父类的protected。
* 子类的返回类型ArrayList<Integer>是父类返回类型List<Integer>的子类。
* 子类抛出异常为Excpetion，是父类抛出异常throwable的子类。
* 子类重写方法使用@Override注解，可以让编译器帮忙检查是否满足上述三个限制条件
```java
class SuperClass {
    protected List<Integer> func() throws Throwable {
        return new ArrayList<>();
    }
}

class SubClass extends SuperClass {
    @Override
    public ArrayList<Integer> func() throws Exception {
        return new ArrayList<>();
    }
}
```
在调用一个方法时，先看子类有没有满足的方法，再看父类中有没有，是否需要从父类中继承。否则就要对参数进行转型，转成父类之后看看是否有对应方法。总的来说方法调用优先级为：
* this.func(this)
* super.func(this)
* this.func(super)
* super.func(super)
```java
/*
    A
    |
    B
    |
    C
    |
    D
 */


class A {

    public void show(A obj) {
        System.out.println("A.show(A)");
    }

    public void show(C obj) {
        System.out.println("A.show(C)");
    }
}

class B extends A {

    @Override
    public void show(A obj) {
        System.out.println("B.show(A)");
    }
}

class C extends B {
}

class D extends C {
}
/**********************************/

public static void main(String[] args) {

    A a = new A();
    B b = new B();
    C c = new C();
    D d = new D();

    // 在 A 中存在 show(A obj)，直接调用
    a.show(a); // A.show(A)
    // 在 A 中不存在 show(B obj)，将 B 转型成其父类 A
    a.show(b); // A.show(A)
    // 在 B 中存在从 A 继承来的 show(C obj)，直接调用
    b.show(c); // A.show(C)
    // 在 B 中不存在 show(D obj)，但是存在从 A 继承来的 show(C obj)，将 D 转型成其父类 C
    b.show(d); // A.show(C)

    // 引用的还是 B 对象，所以 ba 和 b 的调用结果一样
    A ba = new B();
    ba.show(c); // A.show(C)
    ba.show(d); // A.show(C)
}

```
2. 重载(Overload)  
存在于同一个类中，指一个方法与已存在的方法名相同，但是参数类型，个数，顺序至少有一个不同。  
应当注意的是返回值不同，其它类型都相同的不算重载。
```java
class OverloadingExample {
    public void show(int x) {
        System.out.println(x);
    }

    public void show(int x, String y) {
        System.out.println(x + " " + y);
    }
}
public static void main(String[] args) {
    OverloadingExample example = new OverloadingExample();
    example.show(1);
    example.show(1, "2");
}
```
## 七、 反射
每个类都有一个Class对象，包含有类的相关信息。当编译一个新类时，会生成一个.class文件，该文件内容保存着Class对象。类加载相当于Class对象的加载，类在第一次使用时才动态地加载到JVM中，也可以使用`Class.forName("com.mysql.jdbc.Driver")`这种方式来控制类的加载，该方法会返回一个Class对象。  
反射可以提供运行时类的信息，并且这个类可以在运行时加载进来。甚至在.class文件不存在时也可以加载进来。  
Class和java.lang.reflect一起对反射提供了支持。java.lang.reflect主要包含以下三个类：
* Field：可以使用get()和set()方法来获取和修改Field对象关联的字段。
* Method:可以使用invoke()方法调用与Method对象关联的方法。
* Constructor:可以使用Constructor的newInstance()创建新的对象。
### 反射的优点
* 可扩展性：应用可以使用全限定名创建可扩展对象的实例，来使用外部的用户自定义类。
* 类浏览器和可视化开发环境：一个类浏览器需要可以可以枚举类的成员。可视化开发环境(如IDE)可以从利用反射中可用的类型信息中受益，以帮助程序员编写正确的代码。
* 调试器和测试工具：调试工具需要能够检查一个类里的私有成员。测试工具可以利用反射来自动地调用类里定义地可被发现地API定义，以确保一组测试中有较高地代码覆盖率。
### 反射的缺点  
尽管反射的功能十分强大，但也不能乱用，如果一个功能可以不用反射完成，那就尽量不用。使用反射要注意以下：
* 性能开销：反射涉及到动态类的解析，JVM无法对这部分代码进行优化，因此反射操作比那些非反射的操作效率低得多。避免在经常执行的代码或者运行速度要求较高的程序中使用反射。
* 安全限制： 使用反射技术要求程序必须在一个没有安全限制的环境中运行。如果一个程序必须要在有安全限制的环境中运行，比如applet,那这就是个问题了。
* 内部暴露：由于反射允许一些正常情况不允许的操作（比如访问私有的成员或方法），所以使用反射会导致意料之外的副作用，这可能导致代码功能失调并破坏可移植性。反射代码破坏了抽象性，因此当平台发生改变时，代码的行为也可能随着变化。
[Trail: The Reflection API](https://docs.oracle.com/javase/tutorial/reflect/index.html)
[深入解析 Java 反射（1）- 基础](https://www.sczyh30.com/posts/Java/java-reflection-1/)

## 八、异常
Throwable可以用来表示任何可以作为异常抛出的类，分为两种：Error和Exception。其中Error表示JVM无法处理的错误，Exception分两种情况：
* 受检异常：需要使用try....catch....语句捕获并处理，可以从 异常中恢复
* 非受检异常：是程序运行时的错误，例如0会引发Arithmetic Exception，此时程序异常并且无法恢复。
## 九、泛型  
```java
public class Box<T> {
    // T stands for "Type"
    private T t;
    public void set(T t) { this.t = t; }
    public T get() { return t; }
}
```
* [Java 泛型详解](https://www.cnblogs.com/Blue-Keroro/p/8875898.html)
* [10 道 Java 泛型面试题](https://cloud.tencent.com/developer/article/1033693)

## 十、注解  
java注解是附加在代码中的一些元信息，用于代码编译运行时进行解析和使用，起到说明、配置的功能。注解不会也不能影响到代码的实际逻辑，仅仅是辅助性的作用。  
[注解 Annotation 实现原理与自定义注解例子](https://www.cnblogs.com/acm-bingzi/p/javaAnnotation.html)
## 十一、特性  
### java各版本的新特性
New highlights in Java SE 8

Lambda Expressions
Pipelines and Streams
Date and Time API
Default Methods
Type Annotations
Nashhorn JavaScript Engine
Concurrent Accumulators
Parallel operations
PermGen Error Removed
New highlights in Java SE 7

Strings in Switch Statement
Type Inference for Generic Instance Creation
Multiple Exception Handling
Support for Dynamic Languages
Try with Resources
Java nio Package
Binary Literals, Underscore in literals
Diamond Syntax
[Difference between Java 1.8 and Java 1.7?](https://www.selfgrowth.com/articles/difference-between-java-18-and-java-17)
[Java 8 特性](http://www.importnew.com/19345.html)

### java与C++的区别
* Java是纯粹面向对象的语言，所有对象均继承自java.lang.Object,C++为了兼容C既支持面向对象，也支持面向过程。
* java基于虚拟机实现跨平台特性，C++依赖于特定的平台。
* Java没有指针，它的引用可以理解为安全指针，而C++具有和C一样的指针。
* Java支持自动垃圾回收，而C++需要手动回收
* Java不支持多重继承，只能实现多个接口达到相同的目的，C++支持多继承。
* Java不支持操作符重载，虽然可以对两个String对象实现加法操作，但这是语言内置的操作，不属于操作符重载，而C++可以。
* Java的goto是保留字，但是不可以用。C++可以使用goto
[What are the main differences between Java and C++?](https://cs-fundamentals.com/tech-interview/java/differences-between-java-and-cpp.php)
### JRE or JDK
* JRE:java Run Enviroment,java运行环境的简称，为java的运行提供了所需环境。它是一个JVM程序，主要包括JVM的实现和一些java的基本类库。
* JDK：Java developement Kit,java开发套件，提供了java的开发及运行环境，集成了JRE以及其他的一些工具。比如编译java源码的编译器javac等。 


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