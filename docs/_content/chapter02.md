## 2.1 抽象计算机

随着计算机的高速发展，计算机与内存之间的IO会严重影响了计算机的运算效率，此时，聪明的人们，就发明了高速缓存，就是在cpu的模块中增加了一块高速缓存，首先将重要的数据读取到高速缓存中，cpu从高速缓存中读取数据，这极大的提高了cpu的效率。

![image-20210516172209875](..\imgs\image-20210516172209875.png)

但随着技术的发展，单核cpu已经无法满足人们的需求了，这时多核CPU诞生了，但是也会带来新的问题，数据同步问题，每个核心都有自己高速缓存区，这时聪明的人类一定想到了办法解决这些问题，比如缓存一致性协议（MESI，MSI，MOSI、Synapse、Firely、DragonProtocol)等等，每个核心都有自己的缓存区，同时也有共享的主内存模块。

![image-20210516172844754](..\imgs\image-20210516172844754.png)

前面我们已经知道了，jvm是一个抽象的计算机，具有计算机的一些特性。jvm在进行运算时，也会有自己存放运算时产生数据的地方，也就是运行时数据区，对比硬件计算机，jvm中工作的是线程，为了提高jvm的效率，jvm给每个线程分配了线程独有的区域，既有独有的，那么也有线程共享的区域。那么运行时数据区，哪些是线程独有的，哪些是线程共享的呢，我们下面一一道来。

## 2.2 运行时数据区

首先我们看官网说明。

```txt
The Java Virtual Machine defines various run-time data areas that are used during execution of a program. Some of these data areas are created on Java Virtual Machine start-up and are destroyed only when the Java Virtual Machine exits. Other data areas are per thread. Per-thread data areas are created when a thread is created and destroyed when the thread exits.
```

官网的介绍，也印证了上述的猜想，运行时数据区，有些区域是和jvm一起诞生和销毁，这部分区域是线程共享的，还有一部分区域是线程独有的，他们随着线程的创建和销毁。

![image-20210516175036010](..\imgs\image-20210516175036010.png)

### 2.2.1 方法区

先看官网介绍，从官方文档中我们可以了解到：

- 方法区是线程共享的，它在虚拟机启动的时候创建的。

```txt
The Java Virtual Machine has a method area that is shared among all Java Virtual Machine threads. ...

The method area is created on virtual machine start-up. ...
```

- 方法区是描述堆逻辑的一部分，又称作非堆。

```txt
Although the method area is logically part of the heap,...
```

- 方法区存放已被加载的类信息，常量，静态变量，即时编译后的代码等数据

```txt
 It stores per-class structures such as the run-time constant pool, field and method data, and the code for methods and constructors, including the special methods (§2.9) used in class and instance initialization and interface initialization.
```

- 方法区无法满足内存分配要求时，会抛OOM

```txt
If memory in the method area cannot be made available to satisfy an allocation request, the Java Virtual Machine throws an OutOfMemoryError.
```

此时，我们回想一下类的加载步骤

> 1、找到字节码文件
>
> 2、将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构 
>
> 3、在堆内存中，生成一个代码java.lang.class对象，作为方法区这个类各种数据访问的入口。

结合1，2步骤，此时加载的class对象，可以看下图

![image-20210516182712404](..\imgs\image-20210516182712404.png)

**注意：方法区在1.7之前被称作为永久带，在1.8中称之为元空间。**

### 2.2.2 堆

- 堆是jvm管理内存中最大的一块，它也是jvm启动时创建的，是所有线程共享的.
- java对象实例，数组都在jvm上分享

```txt
The Java Virtual Machine has a heap that is shared among all Java Virtual Machine threads. The heap is the run-time data area from which memory for all class instances and arrays is allocated.
The heap is created on virtual machine start-up.
```

### 2.2.3 虚拟机栈

经过上面的分析，类的加载已经完成，后面的链接，初始化也会随之生效，那么接下来，当然是使用了，使用使用谁在用，线程在用，那么一个线程的执行状态是如何维护的呢？一个线程可以执行多少个方法？这些关系是怎么维护的呢？

虚拟机栈，栈是一个数据结构（数据结构：存放数据的结构模型）

- 虚拟机栈是一个线程执行的区域，保存着一个线程中方法调用的状态。它是线程私有的。
- 每一个执行的方法称栈帧。

```java
void a(){
	b();
}
void b(){
    c();
}
void c(){
    
}
```

上面的方法在虚拟机栈中是怎么执行的呢

![image-20210516190233908](..\imgs\image-20210516190233908.png)

#### 2.2.3.1 栈帧

栈帧对应的是每个调用的方法，可以理解为一个方法运行空间。那么栈帧包含哪些信息呢？

- **局部变量表：**方法中定义的变量，以及方法的参数存放在这张表中
- **操作作数栈：**以压栈和出栈的方式存储操作数
- **动态链接：**支持一个方法的动态调用过程，也就是将符号引用变为直接引用。
- **方法返回地址：**方法执行后，只有两种方式可以退出，一种是遇到方法返回的字节码指令，一种是遇到异常，并且这个异常在方法中没有处理。
- **即时信息：**《java虚拟机规范》允许实现增加一些虚拟机规范中没有的信息到方法中，这种信息就是即时信息，又加附加信息。

**动态链接，方法返回地址，即时信息，统称为栈帧信息。**

![image-20210516192951466](..\imgs\image-20210516192951466.png)

### 2.2.4 程序计数器

一个jvm进程中，会有多个线程在执行，而一个线程是否在执行，取决于cpu的时间片切换，当一个线程正在执行时，cpu进行时间片切换，没有执行它了，当下次轮到它执行时，线程是怎么知道自己执行到了哪里呢？

程序计数器就是用来记录线程执行到的位置。

如果执行的是一个java方法，计数器记录的是正在执行的字节码地址。

如果执行的是一个本地方法，这个计数器是空。

### 2.2.5 本地方法栈

如果执行的native类型的方法，这些方法就会在本地方法栈中执行。

那么如果java方法执行的时候调用了本地方法呢？

![image-20210516200343702](..\imgs\image-20210516200343702.png)

### 2.2.6 常量池

常量池我们经常说到静态常量池，运行时常量池，字符串常量池，那这些常量池都存放些什么数据呢？

- 静态常量池：存储字面量和符号引用
- 运行时常量池： 每个类或者每个接口，在jvm中进行run时，在我们所在的内存中开辟一块用来存储静态常量池中部分数据的一块特殊区域。
- 字符串常量池： 存放字符串常量

![image-20210516233028498](..\imgs\image-20210516233028498.png)

