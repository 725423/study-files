# JVM面试随笔

### java的内存区域（运行时区域）

- java的内存区域分为五大块：**堆区、方法区、虚拟机栈、本地方法栈、程序计数器**

- **按照线程是否共享**：**堆区**和**方法区**是线程共享的，创建进程就创建该区域

- **虚拟机栈**、**本地方法栈**、**程序计数器**是线程私有的，创建线程时该区域才会产生。

- ![在这里插入图片描述](https://img-blog.csdnimg.cn/6e15fac388784c7490f1d1b5fe002f4f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAV1lTQ09ERVI=,size_17,color_FFFFFF,t_70,g_se,x_16)

- **JDK 1.8 之前** ：

  ![Java 运行时数据区域（JDK1.8 之前）](https://guide-blog-images.oss-cn-shenzhen.aliyuncs.com/github/javaguide/java/jvm/java-runtime-data-areas-jdk1.7.png)

  

  

  **JDK 1.8 之后** ：

  ![Java 运行时数据区域（JDK1.8 之后）](https://guide-blog-images.oss-cn-shenzhen.aliyuncs.com/github/javaguide/java/jvm/java-runtime-data-areas-jdk1.8.png)

  

### JVM包括哪几个部分

- JVM 主要由四大部分组成：**ClassLoader（类加载器）**，**Runtime Data Area（运行时数据区，内存分区）**，**Execution Engine（执行引擎）**，**Native Interface（本地库接口）**
- JVM 是执行 Java 程序的虚拟计算机系统，执行过程：**.class文件**——>**ClassLoader（类加载器）**——>**加载到内存中（运行时数据区）**——>**命令解释器（执行引擎:将字节码翻译成特定的操作系统指令集交给 CPU 去执行）**:此过程需要用到**本地 Native 接口（本地库接口）**
- **ClassLoader**：负责加载字节码文件即 class 文件，class 文件在文件开头有特定的文件标示，并且 ClassLoader 只负责class 文件的加载，至于它是否可以运行，则由 Execution Engine 决定。
- **Runtime Data Area**：是存放数据的，分为五部分：Stack（虚拟机栈），Heap（堆），Method Area（方法区），PC Register（程序计数器），Native Method Stack（本地方法栈）。几乎所有的关于 Java 内存方面的问题，都是集中在这块。
- **Execution Engine：执行引擎**，也叫 Interpreter。Class 文件被加载后，会把指令和数据信息放入内存中，Execution Engine 则负责把这些命令解释给操作系统，即将 JVM 指令集翻译为操作系统指令集。
- **Native Interface**：负责调用本地接口的。他的作用是调用不同语言的接口给 JAVA 用，他会在 Native Method Stack 中记录对应的本地方法，然后调用该方法时就通过 Execution Engine 加载对应的本地 lib。原本多用于一些专业领域，如JAVA驱动，地图制作引擎等，现在关于这种本地方法接口的调用已经被类似于Socket通信，WebService等方式取代。
- 为什么需要用到**命令解释器**：字节码文件是**JVM定义的一套指令集规范**，并**不能直接交给底层操作系统去执行**，因此需要特定的命令解释器（执行引擎）**将字节码翻译成特定的操作系统指令集**交给 CPU 去执行



### java程序是怎么运行的

概括来说，写好的 Java 源代码文件经过 Java 编译器编译成字节码文件后，通过类加载器加载到内存中，才能被实例化，然后到 Java 虚拟机中解释执行，最后通过操作系统操作 CPU 执行获取结果。如下图

![img](https://uploadfiles.nowcoder.com/images/20220224/4107856_1645690729850/2B33427D8F51F7F8EADC74E0034C5FC7)



### 本地方法栈有什么用

本地方法栈（Native Method Stacks）与虚拟机栈所发挥的作用是非常相似的，其区别只是虚拟机栈为虚拟机执行Java方法（也就是字节码）服务，而本地方法栈则是为虚拟机使用到的本地（Native）方法服务。



### 没有程序计数器会怎样

没有程序计数器，Java程序中的流程控制将无法得到正确的控制，多线程也无法正确的轮换