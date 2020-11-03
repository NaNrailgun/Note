## JVM整理

**JDK 1.8 之前：**

![img](https://snailclimb.gitee.io/javaguide/docs/java/jvm/pictures/java%E5%86%85%E5%AD%98%E5%8C%BA%E5%9F%9F/JVM%E8%BF%90%E8%A1%8C%E6%97%B6%E6%95%B0%E6%8D%AE%E5%8C%BA%E5%9F%9F.png)

**JDK 1.8 ：**

![img](https://snailclimb.gitee.io/javaguide/docs/java/jvm/pictures/java%E5%86%85%E5%AD%98%E5%8C%BA%E5%9F%9F/2019-3Java%E8%BF%90%E8%A1%8C%E6%97%B6%E6%95%B0%E6%8D%AE%E5%8C%BA%E5%9F%9FJDK1.8.png)

线程私有：程序计数器、虚拟机栈、本地方法栈

线程共享：堆、方法区、直接内存

### 程序计数器

字节码解释器通过改变程序计数器去依次读取指令，实现代码流程的控制。

程序计数器是用来记录线程运行位置的，当发生线程切换时能够通过程序计数器保存线程的运行位置。

### 虚拟机栈

虚拟机栈存储了栈帧，每一次调用一个方法的时候就会往虚拟机栈里面压入一个栈帧，当方法结束的时候，这个栈帧就会从虚拟机栈里弹出。栈帧存储了局部变量表，局部变量表存储了基本数据类型和对象引用

- **`StackOverFlowError`：** 栈深度溢出。
- **`OutOfMemoryError`：** 栈拓展失败。

如果线程请求的栈深度超过虚拟机所允许的深度抛StackOverflowError，如果虚拟机栈的容量是允许动态拓展的时候，当栈拓展时无法申请到足够的内存抛OutOfMemoryError，如果其他线程被创建的时候虚拟机栈申请不到足够空间的时候也会抛OutOfMemoryError

### 本地方法栈

虚拟机栈执行Java方法，本地方法栈执行的是Native方法，HotSpot将本地方法栈和虚拟机栈合并了。

在栈深度溢出时抛StackOverflowError，在栈拓展失败的时候抛OutOfMemoryError

### 堆

Java中，几乎所有的对象都在堆上分配，但有些对象经过逃逸分析发现没有发生逃逸的话就会直接在栈上分配。1.8之前，堆分为新生代、老年代、永久代。1.8的时候永久代被废除，取而代之的是元空间。

新生代还分为：伊甸园、from survivor、to survivor

1. **`OutOfMemoryError: GC Overhead Limit Exceeded`** ： 当JVM花太多时间执行垃圾回收并且只能回收很少的堆空间时，就会发生此错误。
2. **`java.lang.OutOfMemoryError: Java heap space`** :假如在创建新的对象时, 堆内存中的空间不足以存放新创建的对象, 就会引发`java.lang.OutOfMemoryError: Java heap space` 错误。(和本机物理内存无关，和你配置的内存大小有关！)

### 方法区

方法区是Java虚拟机规范规定的，永久代是HotSpot对方法区的一个实现，方法区和永久代的关系好比接口和实现类。

方法区用于存储已被**虚拟机加载的类信息、常量、静态变量**等数据。

当方法区无法满足新的内存分配需求的时候抛OutOfMemoryError

为什么使用元空间代替永久代？

1.永久代有一个jvm本身设置的大小上限，无法进行调整。元空间使用的是直接内存吗，受本机可用内存限制，虽然元空间还是有内存溢出的风险，但比原来出现的机率要小很多。当你元空间溢出时会得到如下错误： `java.lang.OutOfMemoryError: MetaSpace`

2.因为元空间可以做到只受系统内存大小的限制，所以能加载的类就更多了。

运行时常量池

运行时常量池是方法区的一部分，用来存放编译期生成的各种字面量和符号引用，这部分数据会在类加载后存放到方法区的运行时常量池里。

1. **JDK1.7之前运行时常量池逻辑包含字符串常量池存放在方法区, 此时hotspot虚拟机对方法区的实现为永久代**
2. **JDK1.7 字符串常量池被从方法区拿到了堆中, 这里没有提到运行时常量池,也就是说字符串常量池被单独拿到堆,运行时常量池剩下的东西还在方法区, 也就是hotspot中的永久代** 。
3. **JDK1.8 hotspot移除了永久代用元空间(Metaspace)取而代之, 这时候字符串常量池还在堆, 运行时常量池还在方法区, 只不过方法区的实现从永久代变成了元空间(Metaspace)**

### 直接内存

JDK1.4 中新加入的 **NIO(New Input/Output) 类**，引入了一种基于**通道（Channel）** 与**缓存区（Buffer）** 的 I/O 方式，它可以直接使用 Native 函数库直接分配堆外内存，然后通过一个存储在 Java 堆中的 DirectByteBuffer 对象作为这块内存的引用进行操作。这样就能在一些场景中显著提高性能，因为**避免了在 Java 堆和 Native 堆之间来回复制数据**。

本机直接内存的分配不会受到 Java 堆的限制，但是，既然是内存就会受到本机总内存大小以及处理器寻址空间的限制。
















