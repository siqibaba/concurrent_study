## 6.1 执行引擎

执行引擎就是将字节码转化为机器码。

![image-20210525063946829](..\imgs\image-20210525063946829.png)

java执行有三种模式

- 解释器执行

  ![image-20210525064636371](..\imgs\image-20210525064636371.png)

  解释执行，就是解释一行代码，执行一下

- 编译器执行

  ![image-20210525064726963](..\imgs\image-20210525064726963.png)

  编译执行，就是先编译完，再执行

- 混合模式（默认）

![image-20210525064610727](..\imgs\image-20210525064610727.png)

​	java默认使用的是混合模式，即有解释执行，又有编译执行。

## 6.2 即时编译器

HotSpot内置了两个即时编译器，C1和C2

- C1

  Client Compiler，适用于执行时间短的对启动性能有要求的程序。比如带界面的程序

- C2

  Service Compiler，适用于执行时间长的，或者对峰值性能有要求的程序。

jdk 7 分层编译

1、解释器执行

2、C1 简单的优化 不开 profiling

3、C1执行方法调用的次数，或者循环回边次数  开profiling（关注次数和回边数）

4、C1 全开 profiling

5、C2 耗时

![image-20210525080611225](..\imgs\image-20210525080611225.png)

**什么是热点代码**

- 被多次调用的方法

- 被多次执行的循环体 OSR

**那么怎么去判断哪些代码是热点代码呢？热点探测**

- 基于采样分析的热点探测

  隔段时间采集栈帧顶部的方法，如果发现一个方法多次被探测到，就是热点方法。但是当一个方法阻塞了，就会一直在栈帧顶端，所以这个不是jvm采用的方式。

- 基于计数器

  - 方法调用计数器

    在一个时间段内，方法调用的次数，比如每10s执行的次数，当达不到某个值时，会统计会衰减

  - 回边计数器

    在一个时间段内，循环体调用的次数，同上

热度衰减

## 6.3 方法内敛

方法A内部调用了方法B，当B的方法体很小的时候，会将B的代码直接在方法A执行。当考虑到方法的重载问题，所以方法内敛只对static ，final，private修饰的方法有效。

热点代码325字节，非热点代码 35字节

## 6.4 逃逸分析

一个变量只在方法体中，不会被方法体外部变量引用，会将这个变量直接分配到栈上，随着方法执行完一起消失。	

**标量替换**

```java
public void test(){
	Worker worker = new Worker();
    worker.setId(1).setPassWord("password");
    int id = worker.getId();
    String passWord = worker.getPassWord();
}
// 标量替换，上面的代码会优化为
public void test(){
	Worker worker = new Worker();
    worker.setId(1).setPassWord("password");
    int id = 1;
    String passWord = "password";
}

```

**同步锁消除**

一个方法内部的锁一直被同一个线程调用，那么就会让这个锁消除。
