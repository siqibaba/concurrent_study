## 1.1 什么是jvm

The Java Virtual Machine is the cornerstone of the Java platform. It is the component of the technology responsible for its hardware- and operating systemindependence, the small size of its compiled code, and its ability to protect users from malicious programs. 

The Java Virtual Machine is an abstract computing machine. Like a real computing machine, it has an instruction set and manipulates various memory areas at run time. It is reasonably common to implement a programming language using a virtual machine; the best-known virtual machine may be the P-Code machine of UCSD Pascal.

![image-20210513205210033](..\imgs\image-20210513205210033.png)



jvm （Java Virtual Machine）java虚拟机，Write Once Run Anywhere 一次编写，到处运行。这里我们一定要注意**The Java Virtual Machine is an abstract computing machine. Like a real computing machine.**既然是抽象的计算机，那么它一定会遵循冯·诺依曼计算机模型。

## 1.2 计算机模型

![image-20210513213029706](..\imgs\image-20210513213029706.png)

根据计算机模型，我们思考一个问题，jvm作为一个java虚拟机，是怎么和计算机模型一一对应的？

输入设备：class文件

输出设备：机器码010010

存储器：存放对象的地方，元空间，堆

运算器：

控制器：



## 1.3 jdk jre jvm

java编程语言，必定绕不开这三个单词，那他们之间究竟是个怎么样的关系呢？![image-20210513214035668](..\imgs\image-20210513214035668.png)



## 1.4 java源文件到class文件

User.java文件

```java
public class User {
    private String name = "DOOM";
    private int age;
    private final double salary = 100;
    private static String address;
    private final static String hobby = "Programming";
    private static Object obj = new Object();

    public void say() {
        System.out.println("person say...");
    }

    public static int calc(int op1, int op2) {
        op1 = 3;
        int result = op1 + op2;
        Object obj = new Object();
        return result;
    }

    public static void main(String[] args) {
        calc(1, 2);
    }
}

```

User.java文件到User.class文件，这中间都经历了什么？

User.java->词法分析器->tokens流->语法分析器->语法树/抽象语法树->语义分析器->注解抽象语法树->字节码生成器->User.class文件

由此可见，User.java文件和User.class文件的信息是对等的。

### 1.4.1 类Class文件

User.class文件，我们用16进制编码打开时

```txt
cafe babe 0000 0034 003a 0a00 0a00 2608
0027 0900 0d00 2806 4059 0000 0000 0000
0900 0d00 2909 002a 002b 0800 2c0a 002d
002e 0700 2f0a 000d 0030 0900 0d00 3107
0032 0100 046e 616d 6501 0012 4c6a 6176
612f 6c61 6e67 2f53 7472 696e 673b 0100
0361 6765 0100 0149 0100 0673 616c 6172
7901 0001 4401 000d 436f 6e73 7461 6e74
5661 6c75 6501 0007 6164 6472 6573 7301
0005 686f 6262 7908 0033 0100 036f 626a
0100 124c 6a61 7661 2f6c 616e 672f 4f62
6a65 6374 3b01 0006 3c69 6e69 743e 0100
...
```

对于这个文件，开始我们一定会不知所云，但是jvm官方一定会有文档对这个编码进行解释说明。官网 

https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html

```txt
ClassFile {
    u4             magic;
    u2             minor_version;
    u2             major_version;
    u2             constant_pool_count;
    cp_info        constant_pool[constant_pool_count-1];
    u2             access_flags;
    u2             this_class;
    u2             super_class;
    u2             interfaces_count;
    u2             interfaces[interfaces_count];
    u2             fields_count;
    field_info     fields[fields_count];
    u2             methods_count;
    method_info    methods[methods_count];
    u2             attributes_count;
    attribute_info attributes[attributes_count];
}
```

对比这文件，我们可以了解一些，比如`cafe babe`是magic

```txt
The magic item supplies the magic number identifying the class file format; it has the value 0xCAFEBABE.
```

后面就不一一介绍了，意义不大。

对于这种文件，只从文件显示内容我们是看不懂得，那么jdk有没有提供工具，帮助我们看懂这些呢？

### 1.4.2 反编译指令

jdk提供了javap指令，帮助我们看懂class文件。`javap -v -p User.class`

![image-20210513225702058](..\imgs\image-20210513225702058.png)



```txt
Classfile /E:/myproject/jvm/src/User.class
  Last modified 2021-5-13; size 884 bytes
  MD5 checksum 84811d738266fdde6a2a86a6c68b22b2
  Compiled from "User.java"
public class User
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #10.#38        // java/lang/Object."<init>":()V
   #2 = String             #39            // DOOM
   #3 = Fieldref           #13.#40        // User.name:Ljava/lang/String;
   #4 = Double             100.0d
   #6 = Fieldref           #13.#41        // User.salary:D
   #7 = Fieldref           #42.#43        // java/lang/System.out:Ljava/io/PrintStream;
   #8 = String             #44            // person say...
   #9 = Methodref          #45.#46        // java/io/PrintStream.println:(Ljava/lang/String;)V
  #10 = Class              #47            // java/lang/Object
  #11 = Methodref          #13.#48        // User.calc:(II)I
  #12 = Fieldref           #13.#49        // User.obj:Ljava/lang/Object;
  #13 = Class              #50            // User
  #14 = Utf8               name
  #15 = Utf8               Ljava/lang/String;
  #16 = Utf8               age
  #17 = Utf8               I
  #18 = Utf8               salary
  #19 = Utf8               D
  #20 = Utf8               ConstantValue
  #21 = Utf8               address
  #22 = Utf8               hobby
  #23 = String             #51            // Programming
  #24 = Utf8               obj
  #25 = Utf8               Ljava/lang/Object;
  #26 = Utf8               <init>
  #27 = Utf8               ()V
  #28 = Utf8               Code
  #29 = Utf8               LineNumberTable
  #30 = Utf8               say
  #31 = Utf8               calc
  #32 = Utf8               (II)I
  #33 = Utf8               main
  #34 = Utf8               ([Ljava/lang/String;)V
  #35 = Utf8               <clinit>
  #36 = Utf8               SourceFile
  #37 = Utf8               User.java
  #38 = NameAndType        #26:#27        // "<init>":()V
  #39 = Utf8               DOOM
  #40 = NameAndType        #14:#15        // name:Ljava/lang/String;
  #41 = NameAndType        #18:#19        // salary:D
  #42 = Class              #52            // java/lang/System
  #43 = NameAndType        #53:#54        // out:Ljava/io/PrintStream;
  #44 = Utf8               person say...
  #45 = Class              #55            // java/io/PrintStream
  #46 = NameAndType        #56:#57        // println:(Ljava/lang/String;)V
  #47 = Utf8               java/lang/Object
  #48 = NameAndType        #31:#32        // calc:(II)I
  #49 = NameAndType        #24:#25        // obj:Ljava/lang/Object;
  #50 = Utf8               User
  #51 = Utf8               Programming
  #52 = Utf8               java/lang/System
  #53 = Utf8               out
  #54 = Utf8               Ljava/io/PrintStream;
  #55 = Utf8               java/io/PrintStream
  #56 = Utf8               println
  #57 = Utf8               (Ljava/lang/String;)V
{
  private java.lang.String name;
    descriptor: Ljava/lang/String;
    flags: ACC_PRIVATE

  private int age;
    descriptor: I
    flags: ACC_PRIVATE

  private final double salary;
    descriptor: D
    flags: ACC_PRIVATE, ACC_FINAL
    ConstantValue: double 100.0d

  private static java.lang.String address;
    descriptor: Ljava/lang/String;
    flags: ACC_PRIVATE, ACC_STATIC

  private static final java.lang.String hobby;
    descriptor: Ljava/lang/String;
    flags: ACC_PRIVATE, ACC_STATIC, ACC_FINAL
    ConstantValue: String Programming

  private static java.lang.Object obj;
    descriptor: Ljava/lang/Object;
    flags: ACC_PRIVATE, ACC_STATIC

  public User();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=3, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: aload_0
         5: ldc           #2                  // String DOOM
         7: putfield      #3                  // Field name:Ljava/lang/String;
        10: aload_0
        11: ldc2_w        #4                  // double 100.0d
        14: putfield      #6                  // Field salary:D
        17: return
      LineNumberTable:
        line 1: 0
        line 2: 4
        line 4: 10

  public void say();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=1, args_size=1
         0: getstatic     #7                  // Field java/lang/System.out:Ljava/io/PrintStream;
         3: ldc           #8                  // String person say...
         5: invokevirtual #9                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
         8: return
      LineNumberTable:
        line 10: 0
        line 11: 8

  public static int calc(int, int);
    descriptor: (II)I
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=4, args_size=2
         0: iconst_3
         1: istore_0
         2: iload_0
         3: iload_1
         4: iadd
         5: istore_2
         6: new           #10                 // class java/lang/Object
         9: dup
        10: invokespecial #1                  // Method java/lang/Object."<init>":()V
        13: astore_3
        14: iload_2
        15: ireturn
      LineNumberTable:
        line 14: 0
        line 15: 2
        line 16: 6
        line 17: 14

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=1, args_size=1
         0: iconst_1
         1: iconst_2
         2: invokestatic  #11                 // Method calc:(II)I
         5: pop
         6: return
      LineNumberTable:
        line 21: 0
        line 22: 6

  static {};
    descriptor: ()V
    flags: ACC_STATIC
    Code:
      stack=2, locals=0, args_size=0
         0: new           #10                 // class java/lang/Object
         3: dup
         4: invokespecial #1                  // Method java/lang/Object."<init>":()V
         7: putstatic     #12                 // Field obj:Ljava/lang/Object;
        10: return
      LineNumberTable:
        line 7: 0
}
SourceFile: "User.java"

```

![image-20210513231015048](..\imgs\image-20210513231015048.png)

![image-20210513231121181](..\imgs\image-20210513231121181.png)

![image-20210513231253984](..\imgs\image-20210513231253984.png)

## 1.5 类加载机制

class字节码文件是怎么加载到虚拟机中的呢？什么是类加载机制呢？

类加载机制是指将字节码文件读入到内存中，同时我们生成数据访问入口的一种机制。![image-20210513233443049](..\imgs\image-20210513233443049.png)

根据类加载机制的定义，我们可以知道，类加载机制最终的产品是数据访问入口。类加载机制是将字节码文件读入到内存中，那么这个字节码文件怎么获取呢？也就是说类加载器怎么加载class文件？

**加载.class文件方式**

- 本地系统直接加载

- 通过网络下载

  案例：微信小程序

- 归档文件加载

  案例：jar包

- 数据库加载

  案例：jsp应用从专有数据库中提取文件

- 将java源文件动态编译成.class文件，也就是运算是计算而成

  案例：动态代理生成

- 加密文件中获取

  案例：防class文件被反编译措施

一个.class文件到数据访问入口经历哪些步骤呢？

![image-20210514072855088](..\imgs\image-20210514072855088.png)

### 1.5.1 加载

加载=查找和导入文件

1. **通过一个类的全限定名去获取二进制字节码流。**（由上面可知我们不一定从字节码文件中获取，还有好多种方式），那么是不是需要一个寻找器来找这些二进制字节码流呢？java中有一段代码块，实现了通过全限定名获取二进制字节码流这个动作，并把这个动作放到jvm外面去实现，根据应用程序需要来获取相应的字节码流。实现这个动作的代码块就是类加载器。
2. **将字节码中所代表的静态存储结构加入到方法区中运行时数据结构。**
3. **在内存中生成一个代表这个类的java.lang.Object对象，作为方法区中这个类各种数据访问入口。**

经历完加载过程后，我们的方法区和堆中就有了数据了

- **方法区：类信息，静态变量、常量**
- **堆：代表被加载类的java.lang.Class对象**

注意：即时编译之后的热点代码并不在这个阶段进入方法区

### 1.5.2 链接

#### 1.5.2.1 验证

验证的目的是什么呢？

1. 保证字节码流中包含的信息完全符合当前虚拟机要求。
2. 保证字节码流中的信息不会危害虚拟机自身安全，导致虚拟机崩溃。

那么验证都验证些什么呢？

- 文件格式验证

  文件格式验证是验证字节码文件是否符合Class文件规范，是否能在当前虚拟机运行。该验证是为了保证字节码文件能够正确的解析并存储到方法区内，这个阶段是基于二进制流进行验证的，只有经过该阶段，文件信息才能加载到方法区中。

  案例：是否以16进制cafe babe开头，版本号是否正确

- 元数据验证

  对元数据信息进行语义校正（java语法的校正），保证不存在不符合java语法的信息。

  案例：是否有父类，是否继承了final类，是否实现了所有的抽象方法。

- 字节码验证

  进行数据流和控制流分析，确定程序语义是合法的，符合逻辑的。对类的方法进行校验分析，保证方法在运行时不会做出危害虚拟机的行为。

  案例：运行检查，栈数据类型和操作码操作参数吻合（栈空间只有4个字节，但我们实际需要远远大于4个字节，这个字节码就有问题了），跳转到合理地方。

- 符号引用验证

  这是最后一个阶段验证，发生在将符号引用转化为直接引用阶段（解析阶段），可以看做对类自身以外的信息进行匹配校验。符号引用的目的就是保证解析阶段正常执行。

  案例：常量池中描述的类是否存在，访问的方法是否有足够的权限。

如果我们可以很自信的确定自己的代码没有问题，可以将验证阶段关掉`-Xverify:none取消验证`

#### 1.5.2.1 准备

为类变量分配内存并设置该内变量的初始值。注意这里的初始值，不是类变量定义的值，而是每个类型对应的默认值，比如int类型，默认值是0。

注意：

- 这里不给final修饰的static变量赋值
- 这里不给实例变量分配（实例变量会和java对象一起分配到堆中）

**constantValue**

这里注意一下这个变量

![image-20210514230430763](..\imgs\image-20210514230430763.png)

![image-20210514230518679](..\imgs\image-20210514230518679.png)

只有被final static 修饰的基本类型和String类型的变量，才会有这个属性，那么这个属性究竟表达了什么意思呢？

在准备阶段的时候，具有constantValue的变量会在准备阶段，根据constantValue为它赋相应的值。

那么为什么只有被final+static的基本类型才能有constantValue这个属性呢？

因为在准备阶段，只有基本类型和string才能取的到值，其他类型还未为其分配内存。

#### 1.5.2.3 解析

将符号引用转化为直接引用。

符号引用：用一组符号来描述目标，可以是字面量。引用的目标可能还为加载到内存中。

直接引用：直接指向目标的指针、相对偏移量或者间接指向目标的句柄

解析阶段是将常量池中符号引用替换直接引用的过程。解析过程主要针对类或接口、字段、类方法、接口方法、方法类型、方法句柄和调用限定符7类符号引用进行。

直接引用和虚拟机的内存布局有关，所以同一个直接引用在不同的虚拟机上的值是不相同的。有了直接引用，那么引用对象一定在内存中。

同一个符号引用进行多次解析是很常见的，那么虚拟机做了肯定会做了优化，虚拟机会将第一次解析结果存放到缓存中，避免多次解析。第一次解析成功，后面的解析就会因为一直是成功的。同理，如果第一次解析失败，后面的也会认为解析失败。

### 1.6 初始化

初始化阶段，阶段就是执行构造方法的过程。

根据程序员的主观计划去初始化类变量和其他资源，比如赋值。

类变量初始值设定方式的方式：

- 声明类变量指定初始值
- 在静态代码块中指定类变量初始值。（按照程序员的逻辑，类变量应放在静态块之前，因为执行代码会根据编码顺序执行的，顺序错了，可能会影响你的业务）

jvm初始化步骤：

- 这个类没有被加载和连接，程序先加载和连接
- 这个类有父类，先加载其父类
- 这个类有初始化语句，系统依次执行这些初始化语句

## 1.7 使用

现在思考一个问题，我们的类被初始化，那么什么时候会被初始化执行呢？初始化的动机是什么？

**主动引用**

只有当类主动使用的时候，才会引起类的初始化。类的主动使用有六种：

- 创建类的实例，也就是new的方式
- 访问某个类或接口的静态变量，或者对该变量赋值
- 调用某个类的静态方法
- 反射
- 访问某个子类，其父类也会被初始化
- java虚拟机启动时被标明的类，也就是通过java.exe运行的主类

**被动引用**

- 引用父类的类变量，只会初始化父类，不会初始化子类
- 定义类数组，不会初始化
- 引用类的static final修饰的基本类型+string变量不会出事

## 1.8 卸载

类在使用完，满足下面条件，就可以被卸载

- 该类的所有实例都被回收，堆中不存在任何关于此类的实例
- 该的类的加载器classload被回收
- 该类对应的java.lang.Class对象没有被任何地方引用，在任何地方通过反射都无法访问该类。

满足上面三个情况，类才可以被卸载，类卸载，就是方法区清空类信息，java类的整个生命周期就结束了。但一般情况下，启动类加载器加载的类是不会被卸载的，而我们两种基础类型类加载器在极少的情况下才会被卸载。

## 1.9 类加载器

前面我们一直提到类加载器，类加载器到底是个什么？

- 类加载负责读取java字节代码，并转换成java.lang.Class类的一个实例代码块。
- 类加载器还可以确定类在虚拟机中的唯一性。

一个类在同一个类加载器中是具有唯一性的，在不同的加载器中，是允许同全限定名类存在的。这两个类是无法通过instance of 和equals等方式进行校验的。

那么类加载器有哪些呢？

- BootStrap ClassLoader 用来加载载$JAVA_HOME中jre/lib/rt.jar包中的class，或者xbootclasspath指定的jar包。由c++实现的，而不是classloader子类。
- Extension ClassLoader 用来加载java平台扩展的一些jar包，包或$JAVA_HOME中jre/lib/*.jar所有jar包中的class或者-Djava.ext.dirs指定目录下的jar包
- App CLassLoader 用来加载classpath指定的jar包，以及Djava.class.path所指定目录下的类和jar包
- Customer ClassLoader 通过java.lang.ClassLoader子类自定义加载class，属于应用程序根据自身需要自定义类加载器，如tomcat，jboss都会根据j2ee规范，实现自定义类加载器。

![image-20210515080105148](..\imgs\image-20210515080105148.png)

**为什么我们的类加载器需要分层呢**？

同一个全限定名下的类，没有分层类加载器，我们不知道需要加载哪一个。同理，核心类也可能被串改，比如我们自己定义一个java.lang.String的类，其他地方引用String的类，是不是就会可能加载我们自己定义的类，那么就会对程序造成安全。所以这个时候，我们想到了，能不能对类加载器做一个信任区分呢？这样三种基础的不同信任级别的类加载器就出现了。最可信的类加载器，当然是java核心API的类加载器，其次是安装的拓展类的类加载器，然后是我们本地类的类加载器。

```java
public static void main(String[] args) {
    User user = new User();
    System.out.println(user.getClass().getClassLoader());
    System.out.println(user.getClass().getClassLoader().getParent());
    System.out.println(user.getClass().getClassLoader().getParent().getParent());
    System.out.println(new String().getClass().getClassLoader());
}
```

![image-20210516084335313](..\imgs\image-20210516084335313.png)

至于最后两个为什么是null，因为后面两个加载器是BootStrap classLoader ，c++层面代码，java看不到。

**jvm类加载器的三种机制**

- **全盘机制**

  当一个类加载器加载某个class时，该Class所引用和依赖的的所有Class都会被其加载，除非显示其他类使用另一个加载器加载。

- **父类委托机制（双亲委派机制）**

  当子类加载器没有加载过目标类时，先委托其父类加载器加载，只有在父类加载器找不到目标类的字节码文件才从子类加载器加载。

  ![image-20210516090654806](..\imgs\image-20210516090654806.png)

  双亲委派只是java推荐的机制，不是强制机制。

  我们可以继承ClassLoader类实现自己类加载器，如果想要双亲机制，就重写findClass方法，如果想打破双亲委派机制，就重写loadClass方法。

  **为什么需要双亲委派机制呢？**

  首先，我们得知道，不同的加载器加载的相同的全限定名的两个类是不同的类的，如果没有双亲委派机制，一个类，既可能在子类加载器被加载一次，又可能在父类加载器再被加载一次，而加载出来的类，却不是同一个类，是不能instand of 和equals的，那么在使用的时候，我们本以为是同一个类，其实却不是，影响了程序的运行。

  **为什么打破双亲委派机制呢？**

  奇了怪了，上面说需要双亲委派机制，下面说要打破。

  任何事情有好的一面也有坏的一面，双亲机制也会带了一些问题，比如java.lang.Driver，jdk提供了规范接口，提供实现的实际是数据厂商，数据库厂商总不能把自己的实现类放在jdk目录里面吧。

  所以这个时候就需要打破双亲委派了，java提供了几种打破双亲委派机制。

  **SPI：**jdk提供接口，供应商提供服务

  spi（Service Provider Interface），jdk提供一个接口，并且还针对这个接口做了调用逻辑，但是并未实现，供应商在在自己的产品包中，META_INFO/service中注册实现类的信息，以提供核心类的使用。

  ![image-20210516094046634](..\imgs\image-20210516094046634.png)

  **OSGI：**代码热部署，热替换，OSGI实现热部署，是自定义的类加载器实现的。每个模块都有自己的类加载器，当需要更换程序模块时，就将该模块连同类加载器一起换掉。

- **缓存机制**

  缓存机制，保证我们所有加载过的Class，在内存中缓存，当我们需要Class，会先从缓存中读取，缓存中不存在，才会读取二进制字节码文件，并转换成Class对象，存在缓存区。这也是为什么我们代码更改，需要重新启动jvm。也就是说，对于同一个类加载器，相同的全限定名，只会加载一次，也就是只会调用一个loadClass方法。

