---
title: java入门
tags: 2020.12.24
notebook: java语法
---
学习方法
 * 多问：思考三分钟没头绪就找人问
 * 多练：亲手写代码

工具介绍
* JDK：java开发工具
* JRE：java运行环境
* JVM：java虚拟机  
* IDE：java开发环境  
JVM用来确保java程序跨平台的，但是本身不跨平台  

编译运行流程
* javac.exe编译.java文件为.class文件
* java.exe运行.class文件

 编码语言规范  
1. 大括号成对、对齐写
2. 左大括号前有空格
3. 代码缩进
4. 方法和程序块之间要有空行
5. 并排语句加空格
6. 运算符两侧加空格

 注释的分类
* 单行注释//
* 多行注释/*   */
* 文档注释 /**   */

常量：
* 在程序执行过程中值不发生改变
* 字符 字符串  数字  布尔类型


数据类型
* int 4字节
* double 8字节
* long 类型定义，后面要加L  


变量的作用域
* 大括号内部的就是代码块
* 大括号内部定义的变量，尽在该大括号内部有效

小类型转大类型是自动的，大类型转小类型是强制的
* 小类型和大类型运算，会先将小类型转换大类型，再计算  
* 强制类型转换可能会损失精度  


标识符
* 定义规则：
  *  不能数字开头
  * 不能是关键字
  * 严格区分大小写

命名规范：
- 类和接口：首字母大写，如果有多个单词，每个单词首字母大写
- 变量和方法： 首字母小写，如果有多个单词，从第二个单词开始首字母大写
- 常量名： 所有字母都大写，多个单词用下划线隔开(_)：MAX_VALUE
- 包名： 全部小写，如果有多级，用点隔开

字符串运算：
* 字符串运算拼接

自增：
- 变量前：先自增再运算
- 变量后，先运算后自增

扩展复制运算的好处：自动强转  

三元运算符：
* 关系式？表达式1：表达式2  

Scanner
- 导包import java.util.Scanner
- 创建对象 Scanner sc = new Scanner(System.in)
- 接收数据 sc.nextInt();

三中循环的区别
* 格式不同
* 初始化语句不同
* 循环体次数执行不同
* 使用场景不同
  
带标号的循环：
` label:for() `  
` break label `  

random类的简单使用：
```java
        Random r =new Random();
        int num = r.nextInt(10);
        System.out.println(num);
```

定义方法：
* 方法必须定义在类中
* 方法之间是平级关系，不能嵌套
* 返回值为void类型时，return可以省略
* return后的返回值类型须与定义的类型一致
* return表示方法结束

方法重载：
* 同一个类中多个方法同名不同参


数组：
* 数组定义格式：
    * 数据类型[] 数组名 = new 数据类型[长度]
    * 数据类型[] 数组名 = new 数据类型[] {元素1，元素2，元素3}
    * 数据类型[] 数组名 = {元素1，元素2，···};
* 数组的访问方式：数组名[索引]
* 数组的遍历：
    * 未赋值的元素有默认值0
    * 数组长度：数组名.length
* 数组的初始化：
    * 只指定长度，系统给出默认值
    * 静态初始化：给出初始化值，由系统决定数组长度 
* java内存的分配：
    * 方法区：储存可运行的class文件，包含方法，静态成员，常量等
    * 栈： 方法运行时使用的内存，特点是“先进后出”，即最先进入栈区的方法最后出栈，比如main方法
    * 堆：存储new出来的数组或对象
    * 本地方法栈
    * 寄存器

空指针异常：
* 数组引用存储的值为null而非数组的地址值时

面向对象：
 * 类与对象：对象是类的具象化

* 变量使用规则：遵循就近原则，方法内部有值就选内部值，没有就选择赋值。先找局部，后找成员。


* 局部变量和成员变量的区别
    * 定义位置、无默认初始化值、作用范围（类中）、内存位置（栈）、生命周期
        * 成员位置：类中方法外
        * 局部位置：方法参数列表、方法体中

* 封装


* private修饰的成员只能在类中访问。
* public可以任意使用
* 添加公共的访问方法

this关键字：
* 表示本类对象的引用，本质是一个对象
* 每个对象都有一个this方法，谁调用就指向谁 
    * 使用this.可以指向成员变量

* 构造方法：也叫构造器，是用来创建对象的方法
    * 格式：修饰符 构造方法名 （参数表） {方法体}
    * 构造方法名与类名同名（包括大小写）
    * 没有返回值（但是里边可以写return;）
    * 没有返回值类型（void都没有）
    * 注意事项：
        * 如果没有，系统会默认提供
        * 如果已经提供，系统不再默认构造
        * 构造方法可以重载

继承
* java中子类只可以继承父类的public成员
* 继承的使用场景：
    * 多个类中存在相同的属性和行为

* 添加公共的方法
* privat、默认、protect、public

继承的缺点：
* 打破了父类的封装性
* 高耦合性，低内聚

java中变量使用规则：
* 局部位置  本类成员位置  父类成员位置

super:父类内存空间的表示
this:本类

定义重名方法的情况：
1. 扩展父类功能
2. 父类方法已过时，需要重新实现
 
子类初始化对象时，要先调用父类默认构造函数，如果父类不存在无参构造函数，需要手动调用有参构造函数

方法重写：
* 必须同名同参


多态：
*  要有继承或实现的关系
* 要有方法重写
* 父类引用指向子类对象
* 多态使用场景：父类型作为形参的数据类型
虚拟方法表用子类重写的方法覆盖父类的方法


抽象类
* 抽象类是有抽象方法的类
* 抽象方法是只有方法声明，没有方法体的方法
* 抽象类用于实现暂不清楚需求的方法，将具体实现放到子类中
* 抽象类特点：
    * 修饰符：abstract
    * 不能实例化，只能在子类中实例化 new
    * 抽象类子类的两个选择：
        * 定义成抽象类
        * 重写父类的抽象方法

* 抽象类成员特点
    * 可以是普通变量，也可以是成员常量
    * 成员方法：可以有普通方法，也可以有抽象方法
    * 构造方法：像普通类一样构造方法，且可以重载
抽象类中的成员比普通类多了一种抽象方法

子类一般有两个构造方法，子类的空参构造访问父类的空参构造，子类的全参构造访问父类的全参构造。

final关键字：
* 修饰类：不能被继承
* 修饰方法：不能被重写，不能与abstract共存
* 修饰变量：最终变量，即常量，不能修改。不建议修饰引用类型，因为仍然可以通过引用修改，意义不大。

static:
* 修饰成员变量被本类所有对象共享
* 不想被修改可以使用final关键字
* 修饰成员方法： 
    * 静态方法中没有对象this，所以不能访问非静态成员
    * 使用场景： 只需要访问静态成员，不需要访问对象状态，所需参数由参数列表提供

接口：
* 接口概述：接口用于描述类的功能而不给出具体实现，类要遵从接口提供的同意规则进行定义，所以接口时对外提供的一组规则、标准
    * 定义： public interface 接口名 {}
    * 接口成员由默认修饰符pubblic abstract
* 接口创建对象的特点：
    * 不能实例化
    * 子类可以是抽象类，也可以是普通类
    * 接口与接口是继承关系
    * 接口中定义的是扩展功能，父类中定义共性内容
* 接口成员特点：
    * 成员变量默认修饰public static final
    * JDK8可以写静态方法，也可以非静态方法。非静态方法要加关键字default
    * JDK9多了private方法
    * 没有构造方法，因为不可实例化

API
* 基本概念：
    * JAVA提供的类使用说明书
    * 模块 包  类
    * javsSE包含核心库
* API使用：
    * Object类：所有类的顶层基类  
    * java.lang包下的类不需要导包，直接使用
    * 成员方法： hashCode toString(默认打印地址值) equals getClass(获取字节码文件)
        * 地址值：包名+类名+对象哈希码值无符号十六进制
        * 实际开发中一般会重写toString和equals方法
* Scanner
    * hasNext:判断是否还有下一个输入项
    * next：获取下一个输入项
    * hasNextInt判断录入是否是整数
    * next结束标记空白字符（回车、换行、tab）

* String
    * 指定字节数组转成字符串
    * 指定字符数转换字符串
    * indexOf subString length charAt lastIndexOf
    * 成员方法 valueOf replace(old,new) getBytes toCharArray split trim去除字符串两端空白字符
    * stringBuilder stringBuffer 可变字符序列
        * stringBuilder() 空白构造
        * stringBuilder(str) 参数构造
        * append方法：添加字符串并返回自身
* Date Calendar
    * Date构造一个日期对象，系统时间，精确到ms
    * 成员方法 getTime日期对象转换时间精确到ms值
    * get(filed)
基本类型的包装类：

* 基本类型不是对象，因此java提供了对应的包装类
    * 装箱：基本类型转包装类型
    * 拆箱
    * 成员方法 statuc 基本类型 parsexxx(string) 将字符串类型转换成对应基本类型
    Interger c = 20;

# 集合：存储多个数据的容器

## 单列集合 List set
list
* 特点：可重复、有序（存取顺序相同）
* add方法添加 get获取 size大小
* ArrayList  
增强for循环：
* for (数据类型 变量名:数组或集合对象){循环体 变量即元素}
* 增强for是迭代器的简写

迭代器
* Iterator it = list.iterator()
* 并发修改：遍历时修改会冲突
* 列表迭代器

泛型：一般只和集合类结合使用
* 更安全
* 避免了类型转换

Collections工具类：
* sort max reverse shuffle(随机置换)

set集合：
* 不可重复、无序
* Set<Integer> s = new HashSet<>();
* Set保证元素的唯一性依赖equals()和hashCode()两个方法

Map
* 双列集合
* put将元素添加 key:keySet()
    * 新值添加返回null，重复添加返回旧值，新值覆盖旧值

* Map<Integer,String> m = new HashMap<>(); 
* key = m.keyset();


java常用工具__IO流
* 基本概念
* 异常概述
    * exception：合理的需要捕获的异常，可以预料，需要解决
    * error:合理的，不需要捕获的异常，比如栈溢出，不可预料
    * JVM默认的异常处理方式：打印异常并终止程序
        * try catch：捕获自己处理
        * throws：抛出交给程序员处理
            * 不处理，接着抛，抛给JVM
        * finally里的代码用于释放资源，一般情况下一定会执行
        
* IO流的概述：java中传输数据的方式
    * 字节流(stream)、字符流(reader writer)
        * InputStream字节输入流顶层抽象类 OutputStream
        * File(普通) Buffer(高效)
    * 读数据、写数据
    * File类：
        * 代表某个文件或者文件夹
        * 构造方法：
            * File(String pathname)
            * File(String parent,String child)
            * File(File parent,String child)
        * 成员方法：
            * createNewFile():创建文件,不存在则创建并返回true，否则返回false
            * mkdir()和mkdirs():创建目录(多级)
            * isDirectory()判断对象是否为目录
            * isFile()
            * exits()
            * getAbsolutelyPath()获取绝对路径
            * getPath()获取(当前项目目录)相对路径
            * list获取指定目录下所有文件（夹）名称数组
            * getName()获取文件名
    * 字符流读数据
        * 一次读一个字符
        ```java
            Reader reader = new FileReader("files/f1");
            int ch;
            while((ch = reader.read())!= -1){
            System.out.println(ch);
            }
        
        ```
        * 一次读一个数组，返回读取的有效长度 read(ch)
    * 字符流写数据：
        ```java
        Writer write = new FileWriter("1.txt")
        reader.write(ch,0,2);
        ```
    * 字符流拷贝文件
        ```java
        Writer writer = new FileWriter("files/1.txt");
        Reader reader = new FileReader("files/f1");
        char[] ch = new char[2048];
        while ((reader.read(ch)) != -1) {
            writer.write(ch);
        }

        reader.close();
        writer.close();
        
        ```
    * 字符缓冲流读写文件
        ```java
        BufferedReader br = new BufferedReader(new FileReader("files/f1"));
        BufferedWriter bw = new BufferedWriter(new FileWriter("files/1.txt"));

        String str;
        while((str = br.readLine())!= null) {
            bw.write(str);
            bw.newLine();
        }
        br.close();
        bw.close();
        ```
    * 字节流读写文件
        ```java
        FileInputStream fr = new FileInputStream("files/a.png");
        FileOutputStream fw = new FileOutputStream("files/b.png");
        int len ;
        while((len = fr.read())!= -1) {
            fw.write(len);
        }
        ```
    * 纯文本文件用字符流，其他用字节流


## java高级__反射
反射：在程序运行期间，根据类的字节码文件获取并使用类。
* 反射的作用：
    * 分析类：加载并初始化一个类
    * 查看并使用对象
    * 类加载的时机：
        * 创建类的实例
        * 访问类的静态成员
        * 初始化类的子类
        * 反射方式创建类的初始化对象（使用类的正名）
    * 获取class对象的三种方式：
        * object类的getClass方法 class clazz = 对象名.getClass();
        * 类的静态属性 class clazz = 类名.clazz
        * Class类的静态方法 Class clazz = Class.forName("类的正名");
    * 反射获取构造器方法并使用 getConstructors()
    * 反射获取成员方法并使用 getMethod() getMethods()
    * 反射获取成员变量并使用



    