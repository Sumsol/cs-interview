# Java 虚拟机和并发

## 虚拟机

- [Java 虚拟机](https://cyc2018.github.io/CS-Notes/#/notes/Java%20虚拟机)

### 内存区域

#### 运行时数据区

##### 图

######  

##### 线程私有区域

###### 程序计数器

####### 当前线程所执行的字节码的行号指示器

####### 对于Java方法，记录正在执行的虚拟机字节码指令的地址；对于Native方法，则为空

####### 唯一无OutOfMemoryError异常的区域

###### 虚拟机栈

####### 方法执行的内存模型

######## 每个方法执行时创建一个栈帧

######## 栈帧用于存储

######### 局部变量表

########## 存放编译期可知的基本数据类型、对象引用、returnAddress类型。

######### 操作数栈

######### 动态链接

######### 方法出口

######## 方法的调用和完成对应栈帧在虚拟机栈中的出栈和入栈

####### 两种异常

######## StackOverflowError异常

######### 线程请求的栈深度大于虚拟机允许的深度

######## OutOfMemoryError异常

######### 若虚拟机栈可动态扩展，却无法申请到足够内存

###### 本地方法栈

####### 与虚拟机栈的作用非常相似，只不过本地方法栈是为Native方法服务

##### 线程共享区域

###### 堆

####### Java虚拟机管理内存中最大的一块

####### 所有对象实例及其数组都在堆上分配内存

####### 垃圾收集器管理的主要区域（“GC堆”）

####### 无需连续内存空间，可动态扩展

######## -Xmx:JVM最大可用内存

######## -Xms:JVM初始化内存大小

####### OutOfMemoryError异常

######## 堆中没内存可分配且无法扩展

###### 方法区

####### 存储已被JVM加载的

######## 类信息

######## 常量

######## 静态变量

######## 即时编译器编译后的代码

####### hotspot用永久代实现方法区

######## 像堆一样管理，省去为方法区编写内存管理代码的工作

######## 容易遇到内存泄漏，使极少数方法在不同虚拟机下有不同表现

######## JDK 7 将永久代中的字符串常量池、静态变量等移至Java堆

######## JDK 8 完全弃用永久代，将剩余内容（主要是类信息）移至元空间（直接内存）

####### 无需连续内存，可扩展，可不实现垃圾收集

####### 内存回收的目标主要针对常量池的回收和对类的卸载，但效果一般

####### OutOfMemoryError异常

####### 运行时常量池

######## Class文件常量池（存放编译期生成的字面量和符号引用）在类加载后放入该区域

######## 动态性

######### 除了编译期，运行期也可将新常量放入池中，如String类的intern()方法

########  

#########  

##### 直接内存

不是虚拟机运行时数据区的一部分

###### NIO可以使用Native函数库直接分配堆外内存，通过堆中的DirectByteBuffer对象作为该内存的引用进行操作

###### 避免了在 Java 堆和 Native 堆之间来回复制数据

#### HotSpot虚拟机对象探秘

##### Java对象创建

###### 图

#######  

###### 类加载检查

####### new -> 常量池中是否有该类的符号引用 -> 是否已类加载

###### 分配内存

####### 分配方式

######## 指针碰撞

######### 用过和没用过的内存各放一端，中间用分界值指针隔开

######### 场合：没内存碎片

######### GC收集器：Serial、ParNew

######## 空闲列表

######### 维护列表来记录空闲内存

######### 场合：大量内存碎片

######### GC收集器：CMS

###### 初始化零值

####### 虚拟机将分配到的内存空间都初始化为零值

###### 设置对象头

####### 虚拟机设置对象的哈希码、GC 分代年龄、锁状态标志等信息

###### 执行init方法

####### 虚拟机执行 <init> 方法进行初始化

##### 对象的访问定位

###### 句柄

#######  

###### 直接指针

#######  

### JDK 监控和故障处理工具

#### JDK 命令行工具

##### jps：虚拟机进程状况工具

###### 列出正在运行的 JVM 进程，显示本地虚拟机唯一 ID (LVMID) 和执行主类名

######  

##### jstat：虚拟机统计信息监视工具

###### 监视虚拟机各种运行状态信息，显示本地或远程虚拟机进程中的类装载、内存、垃圾收集、JIT编译等运行数据

######  

##### jinfo：Java 配置信息工具

###### 实时查看和调整虚拟机各项参数

###### jinfo vmid

####### 查询当前 jvm 进程的全部参数和系统属性

###### jinfo -flag name vmid 查询对应名称参数的具体值

```

C:\Users\SnailClimb>jinfo -flag MaxHeapSize 17340
-XX:MaxHeapSize=2124414976

C:\Users\SnailClimb>jinfo -flag PrintGC 17340
-XX:-PrintGC
```

###### -flag [+|-] name 或 -flag name=value 修改运行期可写的虚拟机参数值

```

C:\Users\SnailClimb>jinfo -flag PrintGC 17340
-XX:-PrintGC

C:\Users\SnailClimb>jinfo -flag +PrintGC 17340

C:\Users\SnailClimb>jinfo -flag PrintGC 17340
-XX:+PrintGC
```

##### jmap：Java 内存映象工具

###### 生成 Java 堆转储快照（heapdump 文件）

此外：
- 使用“-XX:+HeapDumpOnOutOfMemoryError” 参数，可让虚拟机在 OOM 时自动生成 dump 文件。
- Linux 下 kill -3 发送进程退出信号也能拿到 dump 文件。

######  

##### jhat：虚拟机堆转储快照分析工具

###### 用来分析 heapdump 文件，内置了一个 HTTP/HTML 服务器，可在浏览器上查看分析结果

##### jstack：Java 堆栈跟踪工具

###### 生成虚拟机当前时刻的线程快照 (threaddump 或 javacore 文件) 

###### 线程快照

####### what

######## 当前虚拟机内每一条线程正在执行的方法堆栈的集合

####### why

######## 定位线程长时间出现停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待等

######  

### 垃圾收集 GC

只针对Java堆和方法区。程序计数器、虚拟机栈、本地方法栈3个区域随线程而生，随线程而灭，当方法或线程结束时，内存随之回收，无需考虑回收问题。

#### 对象已死？

##### 判断方法

###### 引用计数法

####### 引用计数器，引用加1，失效减1，为0不再使用

####### 缺点

######## 会产生循环引用，无法回收

```
public class ReferenceCountingGc {
    Object instance = null;
	public static void main(String[] args) {
		ReferenceCountingGc objA = new ReferenceCountingGc();
		ReferenceCountingGc objB = new ReferenceCountingGc();
		objA.instance = objB;
		objB.instance = objA;
		objA = null;
		objB = null;
	}
}
```

###### 可达性分析算法

####### 判读GC Roots到某对象是否引用链可达

####### 可做GC Roots的对象

除堆外被其他数据区引用的对象都可做GC Roots


######## 虚拟机栈中引用的对象

######## 方法区中类静态属性引用的对象

######## 方法区中常量引用的对象

######## 本地方法栈中JNI（Native方法）引用的对象

######## 被同步锁持有的对象

######## Java虚拟机内部的引用 

######## JMXBean、 JVMTI中注册的回调、 本地代码缓存

####### 图

########  

##### 引用

###### 强引用

####### Object obj = new Object()

####### 引用在，不回收

###### 软引用 (SoftReference) 

####### 将OOM前，把这些对象回收，若仍OOM，才抛出异常

####### 可以加快回收速度，维护系统的运行安全，防止内存溢出等问题的产生

###### 弱引用 (WeakReference)

####### 下一次GC则清除

###### 虚引用 (PhantomReference)

####### 仅为了回收时收到系统通知, 跟踪对象被垃圾回收的活动

##### finalize()

###### 可达性分析后标记一次，还需判定是否执行finalize()

###### 覆盖finalize()的对象可再被引用，实现自救

###### finalize()只被系统调用一次，因此自救只有一次

###### 尽量避免使用，try-finally等更好

##### 回收方法区

###### 方法区中进行GC的性价比较低

###### 回收内容

####### 废弃常量

######## 常量池中不再引用的字面量、符号引用

####### 不再使用的类

######## 该类的所有实例已被回收

######## 该类的类加载器已被回收

######## 对应的java.lang.Class对象没被反射引用

#### 垃圾收集算法

##### 分代收集

###### 收集器将Java堆分成不同区域，根据回收对象的年龄放入

###### 新生代

####### GC时大批对象死去 -> 复制算法

###### 老生代

####### 对象存活几率高，没额外空间分配担保 -> 标记-清除 或 标记-整理

###### 跨代引用

####### 若新生代存在跨代引用→存活晋升到老生代→跨代引用消除

####### 新生代上建立记忆集，标示出存在跨代引用的那一小块老年代

####### Minor GC 只将存在跨代引用的一小块老年代内存加入GC Roots扫描

##### 复制

将内存划分为大小相等的两块，每次只使用其中一块，当这一块内存用完了就将还存活的对象复制到另一块上面，然后再把使用过的内存空间进行一次清理。

###### 内存分两块，存活对象复制到另一块，使用空间一次清理

###### 不足

####### 内存缩小一半，空间浪费太多

###### Appel式回收

新生代内存分为一大块Eden空间和两小块Survivor空间。GC时，将Eden和Survivor中存活对象复制到另一块Survivor中，最后清掉Eden和使用过的Survivor。

####### 默认Eden和Survivor大小比例为 8 : 1 

######## 只浪费10%，一般98%的对象是可回收的

####### Survivor空间不足时，依赖老生代进行分配担保

###### 图

#######  

##### 标记-清除

- 标记出所需回收对象，统一回收所有标记对象；
- 或标记存活的对象，统一回收所有未标记对象。

###### 回收后会合并相邻空闲分块 -> 空闲链表

###### 不足

####### 效率问题

######## 效率随对象增多而降低

####### 空间问题

######## 产生大量不连续的内存碎片

###### 图

#######  

##### 标记-整理

标记过程与“标记-清除”一样，让所有存活对象向内存空间一端移动，然后直接清除边界以外内存。

###### 在老年代存在大量存活对象，移动时需暂停应用程序（STW）

###### 解决标记-清除算法存在空间碎片化会影响应用程序吞吐量的问题

###### 与标记-清除对比

####### 关注吞吐量的Parallel Scavenge收集器基于标记-整理

####### 关注延迟的CMS收集器则是基于标记-清除

###### “和稀泥式”方案

####### 平时采用标记-清除，内存碎片化程度大时，采用一次标记-整理

###### 图

####### 

#### 垃圾收集器

##### 图

######  

####### Serial + Serial Old : 单线程、客户端

####### ParNew + CMS : 多线程、服务端、停顿时间短

####### Parallel Scavenge + Parallel Old ：多线程、高吞吐量

##### Serial收集器

###### 单线程收集器，GC时会暂停其他所有工作线程

###### 适用于运行在客户端环境下的虚拟机

###### 简单而高效

####### 内存受限环境，额外内存消耗最小

####### 单核CPU环境，没有线程交互开销，拥有最高单线程收集效率

###### 图

#######  

##### ParNew收集器

###### Serial收集器的多线程并行版本

- 并行：多条GC线程并行工作，用户线程仍处于等待。
- 并发：用户线程与GC线程同时执行。

###### JDK 7之前，是运行在服务端模式下首选新生代收集器

###### 除Serial收集器以外，唯一能与CMS收集器配合工作

###### 单核CPU环境下比Serial收集器差，多核CPU环境才高效

###### 图

#######  

##### Parallel Scavenge收集器

###### 与ParNew一样是并行收集的多线程收集器

###### 对比

####### Parallel Scavenge收集器：达到一个可控制的吞吐量

######## 高吞吐量，适合后台运算、无需太多交互的分析任务

吞吐量 = 运行用户代码时间 / (运行用户代码时间 + 垃圾收集时间)

####### CMS等收集器：缩短垃圾收集时用户线程停顿时间

######## 停顿时间短，适合需要用户交互、保证响应质量的程序

###### 参数

####### -XX:MaxGCPauseMillis

######## 控制最大垃圾收集停顿时间

####### -XX:GCTimeRatio

######## 直接设置吞吐量大小

####### -XX:UseAdaptiveSizePolicy

######## 开启GC自适应调节策略

虚拟机动态调节参数以提供最合适的停顿时间或最大的吞吐量。

###### 图

#######  

##### Serial Old收集器

###### Serial收集器的老年代版本，单线程，标记-整理算法

###### 供客户端模式下的HotSpot使用

###### 用途

####### JDK 5之前，与Parallel Scavenge收集器搭配使用

#######  CMS 收集器的后备预案，在并发收集发生 Concurrent Mode Failure 时使用。

###### 图

#######  

##### Parallel Old收集器

###### Parallel Scavenge收集器的老年代版本，多线程，标记-整理算法

###### 注重吞吐量或CPU资源稀缺的场合，优先考虑Parallel Scavenge加Parallel Old收集器

###### 图

#######  

##### CMS收集器

###### 以获取最短回收停顿时间为目标

###### 基于标记-清除算法

初始标记、重新标记需要停顿，并发标记、并发清除与用户程序同时并发

####### 初始标记

######## 只标记GC Roots直接关联的对象

####### 并发标记

######## 从GC Roots直接关联对象开始遍历整个对象图

####### 重新标记

######## 修正并发标记期间，因用户程序运作导致的标记变动

####### 并发清除

######## 清掉标记阶段判断的已死亡对象

###### 三个缺点

####### 对CPU资源敏感

######## 并发阶段虽不会停顿，但会占用部分线程使吞吐率降低

####### 无法处理浮动垃圾

######## 浮动垃圾：GC并发阶段产生的垃圾，只能下次GC时清除

######## 因此CMS收集器需要预留足够空间供用户线程使用

######## 预留内存不足时会出现Concurrent Mode Failure，需启动Serial Old收集器重新GC

####### 产生大量空间碎片

######## 往往出现老年代有很多剩余空间却找不到足够大的连续空间，不得不提前触发Full GC

###### 图

#######  

##### Garbage First收集器（JDK 9）

###### 应用

####### 面向服务端的GC

###### 特点

####### 可预测停顿时间模型 

######## 能指定M毫秒的时间片段内，GC时间不超过N毫秒

####### 分Region的内存布局

######## Java堆分为多个大小相等的Region

######## 每个Region可扮演新生代的Eden、Survivor或老年代空间

######## Humongous Region

######### 专门存储大对象

大对象：大小超过一个Region容量一半的对象。

######### 超级大对象存储在连续的Humongous Region中

超级大对象：超过一个Region容量的对象。

######### 作为老年代的一部分

######## 单次回收的最小单元

######### 每次GC都是Region大小的整数倍

######## 图

#########  

####### 优先级区域回收方式

######## 维护一个优先级列表，根据设定的允许停顿时间，优先回收价值最大的Region

价值：回收所获得的空间大小以及回收所需时间的经验值

###### 细节问题

####### 跨Region引用问题

######## 每个Region维护一个记忆集，记录别的Region指向自己的指针

####### 与用户线程并发问题

######## 通过原始快照(SATB)算法修正用户程序导致的标志变动 

######## Region中两个TAMS指针划分GC时新分配对象的区域，不纳入回收

####### 可靠的停顿预测模型

######## 基于衰减均值理论

衰减均值比平均值更准确地代表“最近的”平均状态。

######### 记录GC每个步骤的成本，并分析平均值，标准偏差，置信度等

###### 基于标记-整理和标记-复制 

除并发标记外，其余阶段要停顿

####### 初始标记

######## 标记GC Roots的直接关联对象，并修改TAMS指针的值

####### 并发标记

######## 对堆中对象进行可达性分析

####### 最终标记

######## 修正用户程序导致的标志变动，即处理并发标记阶段遗留的少量SATB记录

####### 筛选回收

######## 对每个Region的价值和成本排序，根据用户期望停顿时间制定回收计划

######## 决定回收Region将存活对象复制到空Region中，然后清除

####### 图

########  

###### 与CMS收集器对比

####### 优点

######## 可指定最大停顿时间、分Region的内存布局、按收益动态确定回收集等

######## 不产生内存空间碎片

####### 缺点

######## 内存占用高

######### G1卡表复杂且每个Region一个，CMS卡表简单且只有一份

- 记忆集记录chong非 GC 区域指向 GC 区域的指针。
- 卡表是指以“卡精度”实现的记忆集。
- 卡表是一个字节数组，每个元素代表一个区域，将存在跨代指针的区域标识为1，称元素变脏。


######## 额外负载高

######### G1要写前屏障和后屏障，CMS只写前屏障

- 通过写屏障技术维护卡表状态的。
- 写屏障是在虚拟机层面对“引用类型字段赋值”的AOP切面， 在引用对象赋值时产生一个环形（Around） 通知。
- 在赋值前的部分的写屏障叫作写前屏障（Pre-Write Barrier） ， 在赋值后的则叫作写后屏障（Post-Write Barrier） 。

####### 小内存应用用CMS，大内存应用用G1

内存分界为6～8GB之间。

#### 内存分配和回收策略

##### 对象优先在Eden分配

###### Eden区没有足够空间分配时，发起一次Minor GC

##### 大对象直接进入老年代

大对象指需要大量连续内存空间的对象，如很长的字符串及其数组。

###### 大对象容易导致明明还有不少内存却提前触发GC，以获取足够的连续空间

###### -XX: PretenureSizeThreshord

####### 大于设定值的对象直接分配在老年代，避免在Eden和Survivor之间来回复制

##### 长期存活的对象进入老年代

###### 虚拟机为每个对象定义一个对象年龄计数器

###### 对象在Eden区诞生，第一次Minor GC存活后进入Survivor，年龄为1

###### 在Survivor每熬过一次Minor GC年龄加1，到一定年龄后晋升到老年代

###### -XX：MaxTenuringThreshold

####### 设置对象晋升老年代的年龄阈值，默认15

##### 动态对象年龄判断

###### 不是永远要求年龄大于-XX : MaxTenuringThreshold才能进入老年代

###### 若Survivor中相同年龄所有对象大小总和大于该空间的一半，>=该年龄对象进入老年代

##### 空间分配担保

###### Minor GC前，老年代最大可用连续空间 > 新生代所有对象总空间？

####### 是

######## 这次Minor GC确保是安全的

####### 否

######## -XX:HandlePromotionFailure设置是否允许担保失败？

######### 是

########## 老年代最大可用连续空间 > 历次晋升老年代对象平均大小？

########### 是

############ 冒险Minor GC

########### 否

############ Full GC

######### 否

########## Full GC

#### Full GC 触发条件

##### 调用 System.gc()

只是建议虚拟机执行 Full GC，但是虚拟机不一定真正去执行。

##### 老年代空间不足

##### 空间分配担保失败

##### JDK 1.7 及以前的永久代空间不足

当系统中要加载的类、反射的类和调用的方法较多时，永久代可能会被占满，在未配置为采用 CMS GC 的情况下也会执行 Full GC。

##### Concurrent Mode Failure

CMS GC 过程中浮动垃圾过多导致暂时性的空间不足

### 类加载机制

- JVM把类数据从Class文件加载到内存，经校验、转换解析、初始化形成Java类型

- 类型的加载、连接和初始化过程都在程序运行期间完成

#### 类加载的过程

包括加载、验证、准备、解析和初始化这5个阶段。


##### 类的生命周期 

######  

为支持运行时绑定特性(动态绑定)，解析阶段在某种情况下可在初始化阶段之后开始。

##### 加载

“加载”是“类加载”过程的一个阶段，不要混淆。

###### 三个动作

####### 通过类的完全限定名称获取定义该类的二进制字节流

####### 将该字节流表示的静态存储结构转换为方法区的运行时存储结构

####### 在内存中生成一个代表该类的 Class 对象，作为方法区中该类各种数据的访问入口

###### 二进制字节流来源

####### Class文件

######## javac编译

####### ZIP --> JAR、EAR、WAR格式的基础

####### 网络

######## Web Applet

####### 运行时计算

######## 动态代理技术

####### 其他文件

######## JSP应用

####### 数据库

####### 加密文件

######## 防Class文件被反编译

##### 连接

###### 验证

####### 确保 Class 文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。

###### 准备

####### 为类变量（静态变量）分配内存并设置初始值

######## 分配内存

######### 分配内存的不包括实例变量

实例变量会在对象实例化时随对象分配到Java堆。

######### 使用方法区内存

JDK 8后，类变量随Class对象一起存放在Java堆中。

######## 初始值

######### 通常为数据类型的零值

```
public static int value = 123;
```
value在准备阶段后的初始值为0，而不是123。

######### 若类变量是常量，初始值不是零值

```
public static final int value = 123;
```
常量value在准备阶段后的初始值为123，而不是0。

###### 解析

####### 将常量池内的符号引用替换为直接引用的过程

对类或接口、字段、类方法、接口方法、方法类型、方法句柄和调用限定符7类符号引用进行解析，得到类或者字段、方法在内存中的指针或者偏移量。

##### 初始化

###### 初始化阶段就是执行类构造器<clinit>()方法的过程

###### <clinit>()

<clinit>()不是Java代码中直接编写的方法， 是Javac编译器的自动生成物。

####### <clinit>由所有类变量的赋值动作和静态语句块中的语句合并产生，根据出现顺序初始化

静态语句块中只能访问在其之前定义的类变量，之后定义的只能赋值，不能访问
```
public class Test {
	static {
		i = 0; // 给变量赋值可以正常编译通过
		System.out.print(i); // 这句编译器会提示“非法向前引用”
	}
    static int i = 1;
}
```

####### 父类<clinit>()先于子类<clinit>()执行 

例如，父类的静态语句块先于子类的类变量赋值
```
static class Parent {
    public static int A = 1;
    static {
        A = 2;
    }
}

static class Sub extends Parent {
    public static int B = A;
}

public static void main(String[] args) {
     System.out.println(Sub.B);  // 2
}
```

####### 若类中无静态语句块和类变量赋值操作，则不生成<clinit>()

接口中不可使用静态语句块，但可有lei变量赋值操作，故可生成<clinit>()

####### 执行接口的<clinit>()无需先执行父接口的<clinit>()

因为仅当父接口中变量被使用时才会初始化。

####### 多个线程同时初始化一个类，只有一个线程执行<clinit>()，其他线程阻塞

那条线程退出<clinit>()后，其他线程不再进入<clinit>()。同一类加载器下，一个类只会初始化一次。

###### 类的初始化时机

####### 主动引用

触发类初始化

######## 遇到new、getstatic、putstatic 或 invokestatic 字节码指令

######### new实例化对象

######### 设置或读取类的静态字段

########## 被final修饰、已在编译期把结果放入常量池的静态字段除外。

######### 调用类的静态方法

######## 对类进行反射调用 

######## 初始化时先初始化父类

接口仅在真正用到父接口时才初始化。

######## 虚拟机启动时，会先初始化要执行的主类（包含main()方法的类）

######## 实现了有默认方法的接口，先初始化该接口

######## 使用JDK 7的动态语言支持时

如果一个java.lang.invoke.MethodHandle实例最后的解析结果为REF_getStatic、 REF_putStatic、 REF_invokeStatic、 REF_newInvokeSpecial四种类型的方法句柄， 并且这个方法句柄对应的类没有进行过初始化， 则需要先触发其初始化。

####### 被动引用

不触发类初始化

######## 通过子类引用父类的静态字段，子类不初始化

```
System.out.println(SubClass.value); // value字段在SuperClass中定义
```

######## 通过数组定义来引用类，此类不初始化

```
SuperClass[] sca = new SuperClass[10];
```

######## 调用类中常量时不触发初始化

常量会在编译阶段就存入调用类的常量池中。
```
System.out.println(ConstClass.HELLOWORLD);
```

#### 类加载器

实现“通过一个类的全限定名来获取描述该类的二进制字节流”这个动作的代码叫“类加载器”。

##### 类与类加载器

###### 两个类相等，需要类本身相等，并且使用同一个类加载器进行加载。

这里的相等，包括类的 Class 对象的 equals() 方法、isAssignableFrom() 方法、isInstance() 方法的返回结果为 true，也包括使用 instanceof 关键字做对象所属关系判定结果为 true。

```

Object obj = myLoader.loadClass("org.fenixsoft.classloading.ClassLoaderTest").newInstance();
System.out.println(obj instanceof org.fenixsoft.classloading.ClassLoaderTest);
}  // false
```
第一个ClassLoaderTest由自定义加载类加载，第二个ClassLoaderTest由JVM应用程序类加载器加载，虽然来自同一Class文件，但是是两个独立的类。

##### 双亲委派模型

###### JVM角度

####### 启动类加载器

######## C++语言实现，是虚拟机自身一部分

####### 其他类加载器

######## Java语言实现，独立于虚拟机，继承自抽象类java.lang.ClassLoader

###### Java开发人员角度

####### 启动类加载器

######## <JAVA_HOME>\lib目录 或 -Xbootclasspath参数所指定路径

######## 无法被Java程序直接引用

####### 扩展类加载器

######## 由sun.misc.Launcher$ExtClassLoader实现的

######## <JAVA_HOME>\lib\ext目录 或 java.ext.dirs系统变量所指定路径

######## 开发者可以直接使用扩展类加载器

####### 应用程序类加载器

######## 由sun.misc.Launcher$AppClassLoader来实现

######## 用户类路径（ClassPath）上所有的类库

######## 开发者可以在代码中直接使用。若程序中无自定义类加载器，默认为此类加载器。

####### 自定义类加载器

######## 增加除磁盘之外的Class文件来源

######## 通过类加载器实现类的隔离、重载等功能

###### 工作过程

####### 加载一个类，首先请求委托父加载器去完成

因此所有加载请求都应该传递到最顶层的启动类加载器。

####### 父加载器无法加载时，子加载器才尝试加载

###### 好处

####### Java 类随着它的类加载器一起具有一种带有优先级的层次关系

例如类java.lang.Object， 它存放在rt.jar之中， 无论哪一个类加载器要加载这个类， 最终都是委派给处于模型最顶端的启动类加载器进行加载， 因此Object类在程序的各种类加载器环境中都能够保证是同一个类。 

###### 实现

####### java.lang.ClassLoader的loadClass()方法

```
public abstract class ClassLoader {
    // 用于委派的父类加载器
    private final ClassLoader parent;

    public Class<?> loadClass(String name) throws ClassNotFoundException {
        return loadClass(name, false);
    }
    
    protected synchronized Class<?> loadClass(String name, boolean resolve) throws ClassNotFoudException
    {
        // 首先，检查请求的类是否已经被加载过了
        Class c = findLoadedClass(name);
        // 若没有则调用父加载器的loadClass()方法
        if (c == null) {
            try {
                if (parent != null) {
                    c = parent.loadClass(name, false);
                } else {
                    // 若父加载器为空则默认启动类加载器作父加载器
                    c = findBootstrapClassOrNull(name);
                }
            } catch (ClassNotFoundException e) {
                // 如果父类加载器抛出ClassNotFoundException
                // 说明父类加载器无法完成加载请求
            }
            if (c == null) {
                // 在父类加载器无法完成加载时
                // 再调用本身的findClass方法来进行类加载
                c = findClass(name);
            }
        }
        if (resolve) {
            resolveClass(c);
        }
        return c;
    }
}
```

###### 图 

除了顶层的启动类加载器外， 其余的类加载器都应有自己的父类加载器。 通常使用组合（Composition） 关系来复用父加载器的代码，而不是以继承（Inheritance） 的关系来实现的。

## 并发

- [Java 并发](https://cyc2018.github.io/CS-Notes/#/notes/Java%20并发)

### 基础

#### 线程生命周期

#####  

#####  

#### 死锁

##### 避免死锁

###### 破坏互斥条件

###### 破坏请求与保持条件

####### 一次性申请所有资源

###### 破坏不剥夺条件

####### 申请不到，主动释放已占有资源

###### 破坏循环等待条件

####### 顺序申请资源，反序释放

##### 手写死锁

```
public class DeadLock {
    private static final Object resource1 = new Object();
    private static final Object resource2 = new Object();

    public static void main (String[] args) {
        new Thread(() -> {
            synchronized(resource1) {
            	// Thread.sleep(1000);
            	synchronized(resource2) {
            		System.out.println("get resource2");
            	}
            }
        }).start();
        new Thread(() -> {
            synchronized(resource2) {
            	// Thread.sleep(1000);
            	synchronized(resource1) {
            		System.out.println("get resource1");
            	}
            }
        }).start();
    }
}
```

#### 生产者-消费者与队列

##### 使用synchronized实现生产者消费者问题

```java
public  class ProducerConsumer {
    private static final int MAX_SIZE = 10;
    private final LinkedList<Object> list = new LinkedList<>();

    public void produce() {
        synchronized (list) {
            while (list.size() == MAX_SIZE) {
                list.wait();
            }
            list.add(new Object());
            list.notifyAll();
        }
    }

    public void consume() {
        synchronized (list) {
            while (list.size() == 0) {
                list.wait();
            }
            list.remove();
            list.notifyAll();
        }
    }
    
    public static void main(Stirng[] args) {
    	ProducerConsumer product = new ProducerConsumer();
        
        Thread producer = new Thread(() -> {
        	while(true) {
                product.produce();
                // Thread.sleep(1000);
            }
        });
        Thread consumer = new Thread(() -> {
        	while(true) {
                product.consume();
                // Thread.sleep(1000);
            }
        });
        
        producer.start();
        consumer.start();
    }
}
```

##### 使用信号量解决生产者-消费者问题

生产值-消费者（有界缓冲区）问题：两个进程共享一个固定大小的缓冲区。
生产者将信息放入缓冲区，消费者从缓冲区取出信息。缓冲区属于临界资源。

```
private LinkedList<Object> list = new LinkedList<Object>();
private static final int N = 10;
// 互斥量
private final Semaphore mutex = new Semaphore(1);
// 空位
private final Semaphore empty = new Semaphore(N);
// 满位
private final Semaphore full = new Semaphore(0);

public void producer() {
    while(true) {
    	empty.acquire();
        mutex.acquire();
        list.add(new Object());
        mutex.release();
        full.release();
        
        // Thread.sleep(1000);
    }
}

public void consumer() {
    while(true) {
    	full.acquire();
        mutex.acquire();
        list.remove();
        mutex.release();
        empty.release();
        
        // Thread.sleep(1000);
    }
}
```

##### 使用 BlockingQueue 实现生产者消费者问题

```
public class ProducerConsumer {

    private static BlockingQueue<String> queue = 
    	new ArrayBlockingQueue<>(5);

    private static class Producer extends Thread {
        @Override
        public void run() {
            try {
                queue.put("product");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.print("produce..");
        }
    }

    private static class Consumer extends Thread {

        @Override
        public void run() {
            try {
                String product = queue.take();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.print("consume..");
        }
    }
}
```

###### 同一时刻只允许一个线程插入或删除元素

###### BlockingQueue 

####### FIFO队列

######## LinkedBlockingQueue

######### 无界队列

######## ArrayBlockingQueue

######### 固定长度

####### 优先队列

######## PriorityBlockingQueue

#### 使用线程的方法

##### 实现 Runnable 接口

```
public class MyRunnable implements Runnable {
	@Override
    public void run() {
    	// ...
    }
}
```
``` 
public static void main (String[] args) {
    Thread thread = new Thread(new MyRunnable());
    thread.start();
}
```

##### 实现 Callable 接口

```
public class MyCallable implements Callable<Integer> {
    public Integer call() {
        return 123;
    }
}
```

```
public static void main(String[] args) throws ExecutionException, InterruptedException {
    MyCallable mc = new MyCallable();
    FutureTask<Integer> ft = new FutureTask<>(mc);
    Thread thread = new Thread(ft);
    thread.start();
    System.out.println(ft.get());
}
```

##### 继承 Thread 类

```
public class MyThread extends Thread {
	public void run() {
    	// ...
    }
}
```
```
public static void main(String[] args) {
	MyThread thread = new MyThread();
    thread.start();
}
```

##### 实现接口 vs 继承 Thread

###### Java 不支持多重继承，继承了 Thread 类就无法继承其他类，但可实现多个接口

###### 类可能只要求可执行就行，继承整个 Thread 类开销过大

### Java内存模型（JMM）

Java 内存模型试图屏蔽各种硬件和操作系统的内存访问差异，以实现让 Java 程序在各种平台下都能达到一致的内存访问效果。

#### 内存

##### 主内存

###### 所有变量

##### 工作内存

###### 保存了该线程使用的变量的主内存副本拷贝

###### 存储在高速缓存或者寄存器

#####  

######  

#### 内存间交互操作

#####  

#### volatile

volatile变量在各个线程中的工作内存中不存在一致性问题，但Java运算并非原子操作，因此volatile变量的运算在并发下是不安全的

##### 保证对所有线程的可见性

###### read、load、use必须连续出现，assign、store、write也必须连续出现 

##### 不能保证原子性

###### 以上操作只保证连续，但不是原子的，中间可穿插

##### 禁止指令重排序优化

重排序是指cpu允许多条指令不按程序顺序分开发送对应电路单元处理。

###### 多执行一个内存屏障指令，后面指令不能重排到内存屏障之前

#### 原子性、可见性与有序性

##### 原子性（Atomicity）

###### read、load、assign、use、store、write使得基本数据类型的访问读写具备原子性

###### lock和unlock操作使得synchronized块之间的操作具备原子性

##### 可见性（Visibility）

定义：一个线程修改共享变量的值，其他线程能够立即得知这个修改。

实现方式：新值同步回主内存，变量读取前从主内存刷新。

###### volatile

####### 保证新值立即同步到主内存，变量使用前立即从主内存刷新

###### synchronized

####### lock时会清空工作内存中的值，重新从主内存读取

####### unlock前把变量同步回主内存

###### final

####### final变量一旦初始化，其他线程即可访问

##### 有序性（Ordering）

定义：在本线程内观察，所有操作都是有序的。在一个线程观察另一个线程，所有操作都是无序的。因为指令重排序不会影响单线程执行，却影响多线程并发执行。

###### volatile

####### 禁止指令重排序

###### synchronized

####### 一个变量同一时刻只允许一条线程对其lock

#### 先行发生原则

“→”表示先行发生于

##### 程序次序规则

###### 一个线程内，写在前面的操作 → 写在后面的操作

##### 管程锁定规则

###### 同一锁，unlock操作 → lock操作

##### volatile变量规则

###### 对volatile变量，写操作 → 读操作

##### 线程启动规则

###### Thread.start() → 线程的所有操作 

##### 线程中断规则

###### Thread.interrupt() → Thread.interrupted()

##### 线程终止规则

###### 线程的所有操作 →  终止检测Thread.join()、Thread.isAlive()

##### 对象终结规则

###### 一个对象，初始化完成 → finalize()开始

##### 传递性

###### A操作 → B操作，B操作 → C操作，则A操作 → C操作

#### 协程

- 一个线程含多个协程；
- 用户态，函数实现切换；
- 串行执行。

##### 背景

###### 每个请求执行时间很短、 数量很多时，切换开销可能接近计算开销，严重浪费

###### 内核线程调度从用户态转换到内核态，需要响应中断、保护和恢复现场的成本开销

##### 纤程（FIber）

用户可选择自行控制执行过程或调度器中的一个或多个

###### 执行过程

####### 维护执行现场，保护、恢复上下文状态

###### 调度器

####### 负责编排所有要执行代码的顺序

##### Quasar协程库

###### 不依赖JVM

###### 在字节码层面对调用函数中所有局部变量进行保存和恢复。

### 线程安全

多个线程不管以何种方式访问某个类，并且在主调代码中不需要进行同步，都能表现正确的行为。

#### 不可变

不可变的对象一定是线程安全的，不需要再采取任何线程安全保障措施。


##### final修饰的基本数据类型

##### 带有状态的变量都声明为final的对象

##### java.lang.String类

##### 枚举类型

##### java.lang.Number的部分子类

###### Long和Double等数值包装类型

###### BigInteger和BigDecimal等大数据类型

#### 实现方法

##### 互斥同步

共享数据同一时刻只被一个线程使用

###### synchronized

####### monitorenter

######## 锁计数器加1

####### monitorexit

######## 锁计数器减1

###### ReentrantLock

####### 等待可中断

######## 持有锁的线程长期不释放锁时，正等待的线程可放弃等待，改做其他事

####### 公平锁

######## 多个线程等待同一锁时，按照申请锁的时间顺序获得锁

####### 锁绑定多个条件

######## ReentrantLock对象可以同时绑定多个Condition对象

###### 问题

####### 线程阻塞、唤醒→性能降低

####### 悲观的并发策略

##### 非阻塞同步

依靠硬件保证从语义看需要多次操作的行为只通过一条处理器指令就能完成。

###### 乐观的并发策略

####### CAS

####### 版本号机制

######## MVCC

###### CAS(Compare-and-Swap)原子操作

####### 指令执行时，当且仅当内存地址V的值等于旧预期值A，才用新值B更新V的值

####### AtomicInteger 的 compareAndSet() 和 getAndIncrement() 等使用了 Unsafe 类的 CAS 操作

####### 缺点

######## “ABA”问题

变量V初次读值为A，期间改为B，赋值检查时又变为A，导致误认为变量没变过。

######### AtomicStampedReference 通过控制变量值的版本来保证CAS的正确性

######## 自旋时间长时开销大

##### 无同步方案

如果一个方法本不涉及共享数据，那么无需任何同步措施保证正确性。

###### 可重入代码

可在代码执行的任何时刻中断它，去执行另一段代码，返回控制权后，原程序不会出错。

####### 可重入代码 => 线程安全

####### 如果一个方法，输入相同的数据，都返回相同的结果，则满足可重入性

###### 线程本地存储

####### ThreadLock类

######## 每个Thread对象有个ThreadLocalMap对象

######### 键：ThreadLocal.threadLocalHashCode

######### 值：本地变量

### synchronized

保证被它修饰的方法或者代码块在任意时刻只能有一个线程执行。

#### 了解

##### 早期版本，重量级锁，用户态到内核态，效率低

##### JDK1.6后，锁优化

#### 使用方法

##### 修饰实例方法

###### 锁是当前实例对象

##### 修饰静态方法

###### 锁是当前类的Class对象

##### 修饰代码块 

###### 锁是括号里配置的对象

#### 底层原理

##### 每个对象有一个监视器锁(monitor)

实例对象或Class对象

###### monitor只能被一个线程占有 

##### 同步块

###### monitorenter指令

####### monitor进入数为0时, 进入数置1，线程获得所有权

####### 该线程已占有该monitor，重入，monitor进入数+1

####### 该monitor被其他线程占用，阻塞

###### monitorexit指令

####### 执行monitor进入数-1，为0，退出monitor

##### 同步方法

###### ACC_SYNCHRONIZED 标识符

####### 设置该标识的方法获得monitor

#### 与Reentrantlock的区别

##### 两者都是可重入锁

##### synchronized 依赖于 JVM 而 ReentrantLock 依赖于 API

##### ReentrantLock高级功能

###### 等待可中断

###### 公平锁

###### 可绑定多个Condition对象，选择性通知

##### 性能上已差异不大，优先选synchronized

#### 锁优化

##### 自旋锁

挂起线程和恢复线程需要转入内核态中完成，影响性能。当共享数据锁定时间很短时，让线程执行一个忙循环（自旋）。

###### 避免了线程切换的开销，但占用处理器时间

###### 锁占用时间很短时，自旋等待效果好，反之，则浪费处理器时间

###### 自旋必须限定次数，超过次数则挂起，默认10次

###### 自适应自旋锁

自旋时间不固定，由上一次同一锁上的自旋时间及锁拥有者的状态决定

##### 锁消除

对于被检测出不可能存在竞争的共享数据的锁进行消除。

###### 堆上所有数据不会逃逸出去而被其他线程访问到，则无需同步加锁。

##### 锁粗化

若探测到一串零碎操作对同一对象加锁，则将加锁范围扩展到整个操作序列。

###### 一系列连续频繁的互斥同步会导致不必要的性能损耗。

##### 偏向锁

偏向第一次获取锁对象的线程，之后该线程每次进入该锁相关同步块时，不再进行同步操作。

###### 消除数据在无竞争情况下的同步原语，进一步提高程序性能

##### 轻量级锁

先采用 CAS 操作进行同步，如果 CAS 失败了再改用互斥量进行同步。

###### 如果没有竞争，使用CAS操作避免了互斥量的开销。

###### 如果存在竞争，比重量级锁增加了CAS操作，因此更慢。

###### 不是用来代替重量级锁，而是在没竞争时，减少重量级锁互斥量产生的性能损耗。

#####  

######  

######  

### ThreadLocal

#### 将Static和单例等共享变量转化为线程私有，从而无需同步保证线程安全

#### 源码

##### Thread类

```
public class Thread implements Runnable {
	......

	ThreadLocal.ThreadLocalMap threadLocals = null;

	ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
    
	......
}
```

##### ThreadLocal类

```
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}

ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}

void createMap(Thread t, T firstValue) {
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}
```

##### ThreadLocalMap

```
public class ThreadLocal<T> {
	........
    
    static class ThreadLocalMap {
    
        static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;
 
            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }
        
        ........
    }
    
    .......
}
```

#### 内存泄露问题

##### ThreadLocalMap 的 key 为 ThreadLocal 的弱引用

##### GC 时 Key 被清理，value 不被清理，会产生内存泄露

##### set(), get(), remove() 会清除 key 为 null 的记录，但最好用完调用 remove()

### 线程池

#### 好处

##### 降低资源消耗

###### 无需重复创建消耗线程

##### 提高响应速度

###### 无需等待线程创建

##### 提高线程可管理性

###### 线程池统一分配、调优和监控

##### 提供更多强大的功能

###### 线程池具备可扩展性

#### 创建线程池

##### ThreadPoolExecutor构造方法实现

##### 工具类Executors实现

###### FixedThreadPool 

使用无界队列 LinkedBlockingQueue（容量为Intger.MAX_VALUE），可能堆积大量的请求，从而导致OOM
```
   /**
     * 创建一个可重用固定数量线程的线程池
     */
    public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }
```

####### 使用有限的线程集来执行所有提交的任务

###### SingleThreadExecutor

使用无界队列 LinkedBlockingQueue（容量为Intger.MAX_VALUE），可能堆积大量的请求，从而导致OOM
```
   /**
     *返回只有一个线程的线程池
     */
    public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }
```

####### 线程数为1的FixedThreadPool，所有任务使用相同的线程

###### CachedThreadPool

SynchronousQueue队列的容量为0
```
    /**
     * 创建一个线程池，根据需要创建新线程，但会在先前构建的线程可用时重用它。
     */
    public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }
```

####### 为每个任务创建一个线程

#### Executor框架

##### execute()和submit()

###### execute()用于提交不需要返回值的任务

```
public void execute(Runnable command) {
    ...
}
```

###### submit()提交时返回一个Future对象，Future的get()获得返回值，cancel(true)中断线程

```
public Future<?> submit(Runnable task) {
	if (task == null) throw new NullPointerException();
    RunnableFuture<Void> ftask = newTaskFor(task, null);
    execute(ftask);
    return ftask;
 }
```

##### Runnable和Callable

###### Runnable无返回值

```
public interface Runnable {
   /**
    * 被线程执行，没有返回值也无法抛出异常
    */
    public abstract void run();
}
```

###### Callable有返回值

```
public interface Callable<V> {
    /**
     * 计算结果，或在无法这样做时抛出异常。
     * @return 计算得出的结果
     * @throws 如果无法计算结果，则抛出异常
     */
    V call() throws Exception;
}
```

##### Executor

######  

##### 使用示意图 

######  

#### ThreadPoolExecutor

```
public class ThreadPoolExecutorDemo {
    private static final int CORE_POOL_SIZE = 5;
    private static final int MAX_POOL_SIZE = 10;
    private static final int QUEUE_CAPACITY = 20;
    private static final Long KEEP_ALIVE_TIME = 1L;

    public static void main(String[] args) {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
                CORE_POOL_SIZE,
                MAX_POOL_SIZE,
                KEEP_ALIVE_TIME,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<>(QUEUE_CAPACITY),
                new ThreadPoolExecutor.CallerRunsPolicy()
        );

        for (int i = 0; i < 10; i++) {
            executor.execute(() ->
                System.out.println(Thread.currentThread()));
            
            Future<String> future = executor.submit(() ->
                    Thread.currentThread().getName());

            try {
                System.out.println(future.get());
            } catch (InterruptedException | ExecutionException e) {
                e.printStackTrace();
            }
        }

        executor.shutdown();
    }
}

```

##### 构造函数参数

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler) {
    if (corePoolSize < 0 ||
        maximumPoolSize <= 0 ||
        maximumPoolSize < corePoolSize ||
        keepAliveTime < 0)
        throw new IllegalArgumentException();
    if (workQueue == null || threadFactory == null || handler == null)
    throw new NullPointerException();
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}
```

###### corePoolSize

####### 最小可同时运行的线程数

###### maximumPoolSize

####### 队列容量满时，同时运行的线程数变最大线程数

###### workQueue

####### 当前运行线程数到达核心线程数时，新任务存入队列

###### keepAliveTime

####### 池中线程数大于corePoolSize且无新任务提交，核心线程外线程等待keepAliveTime后销毁

###### unit

####### keepAliveTime的时间单位

###### ThreadFactory

####### 线程工厂，用来创建线程，一般默认即可

###### handler

####### 饱和策略

##### 饱和策略

当前同时运行的线程数达到最大线程数且队列也已满时，所需的策略

###### ThreadPoolExecutor.AbortPolicy

####### 抛出 RejectedExecutionException来拒绝新任务的处理。

###### ThreadPoolExecutor.CallerRunsPolicy

####### 调用执行自己的线程运行任务。

###### ThreadPoolExecutor.DiscardPolicy

####### 不处理新任务，直接丢弃掉。

###### ThreadPoolExecutor.DiscardOldestPolicy

####### 丢弃最早的未处理的任务请求。

##### 执行流程

######  

### Atomic原子类

#### 基本类型

##### AtomicInteger

###### 利用 CAS + volatile 和 native 方法来保证原子操作

```
// value字段相对对象内存地址的偏移量
private static final long valueOffset;

static {
    try {
    	// 获取偏移量
        valueOffset = unsafe.objectFieldOffset
            (AtomicInteger.class.getDeclaredField("value"));
    } catch (Exception ex) { throw new Error(ex); } 
}

// 对象指定字段值
private volatile int value;

```


###### incrementAndGet

```
public final int incrementAndGet() {
	return unsafe.getAndAddInt(this, valueOffset, 1) + 1;
}
```
```
/* Unsafe.class */

public final int getAndAddInt(Object var1, long var2, int var4) {
    int var5;
    do {
    	// 获取旧的预期值
    	var5 = this.getIntVolatile(var1, var2);
    // CAS操作，native方法
    // 将内存地址var1+var2的旧值var5更新为新值var5+var4
    } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));
   return var5;
}
```

##### AtomicLong

##### AtomicBoolean

#### 数组类型

##### AtomicIntegerArray

##### AtomicLongArray

##### AtomicReferenceArray 

#### 引用类型

基本类型原子类只能更新一个变量，如果需要原子更新多个变量，需要使用 引用类型原子类。

##### AtomicReference

##### AtomicStampedReference

###### 原子更新带有版本号的引用类型，可解决 ABA 问题。

##### AtomicMarkableReference 

#### 对象的属性修改类型

```
import java.util.concurrent.atomic.AtomicIntegerFieldUpdater;

public class AtomicIntegerFieldUpdaterTest {
    public static void main(String[] args) {
        AtomicIntegerFieldUpdater<User> a = 
        	AtomicIntegerFieldUpdater.newUpdater(User.class, "age");

        User user = new User("Java", 22);
        System.out.println(a.getAndIncrement(user)); // 22
        System.out.println(a.get(user)); // 23
    }
}
```

##### AtomicIntegerFieldUpdater

##### AtomicLongFieldUpdater

##### AtomicStampedReference 

### AQS(AbstractQueuedSynchronizer)

#### AQS原理

##### AQS是线程阻塞等待以及被唤醒时锁分配的机制，由CLH队列实现

##### state表示同步状态

`private volatile int state;//共享变量，使用volatile修饰保证线程可见性`

##### getState，setState，compareAndSetState对state操作

```
//返回同步状态的当前值
protected final int getState() {
        return state;
}
 // 设置同步状态的值
protected final void setState(int newState) {
        state = newState;
}
//原子地（CAS操作）将同步状态值设置为给定值update如果当前同步状态的值等于expect（期望值）
protected final boolean compareAndSetState(int expect, int update) {
        return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
}
```

#####  

#### AQS对资源的共享方式

##### Exclusive（独占）

###### ReentrantLock 

```
/** Synchronizer providing all implementation mechanics */
private final Sync sync;
public ReentrantLock() {
    // 默认非公平锁
    sync = new NonfairSync();
}
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```

####### 非公平锁

```
static final class NonfairSync extends Sync {
    final void lock() {
        // 2. 和公平锁相比，这里会直接先进行一次CAS，成功就返回了
        if (compareAndSetState(0, 1))
            setExclusiveOwnerThread(Thread.currentThread());
        else
            acquire(1);
    }
    // AbstractQueuedSynchronizer.acquire(int arg)
    public final void acquire(int arg) {
        if (!tryAcquire(arg) &&
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
            selfInterrupt();
    }
    protected final boolean tryAcquire(int acquires) {
        return nonfairTryAcquire(acquires);
    }
}
/**
 * Performs non-fair tryLock.  tryAcquire is implemented in
 * subclasses, but both need nonfair try for trylock method.
 */
final boolean nonfairTryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {
        // 第二次CAS，这里没有对阻塞队列进行判断
        if (compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    else if (current == getExclusiveOwnerThread()) {
        int nextc = c + acquires;
        if (nextc < 0) // overflow
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}

```

######## lock()时，先CAS尝试加锁，失败后，若判断state==0，无视队列，CAS抢锁，再失败后入列尾

####### 公平锁

```
static final class FairSync extends Sync {
    final void lock() {
        acquire(1);
    }
    // AbstractQueuedSynchronizer.acquire(int arg)
    public final void acquire(int arg) {
        if (!tryAcquire(arg) &&
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
            selfInterrupt();
    }
    protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            // 1. 和非公平锁相比，这里多了一个判断：是否有线程在等待
            if (!hasQueuedPredecessors() &&
                compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        else if (current == getExclusiveOwnerThread()) {
            int nextc = c + acquires;
            if (nextc < 0)
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }
}
```

######## lock()时，若state==0，判断是否有线程等待，有则入列尾，否则CAS加锁

#######  

########  

##### Share（共享）

###### Semaphore

####### 默认构造AQS的state为permits，acquire时state-1，state==0时线程阻塞等待，release时state+1

###### CountDownLatch

####### 默认构造AQS的state为count，countDown时state-1，state==0时，await线程执行

#### AQS 使用了模板方法模式

```
// 自定义同步器时，可覆盖的方法
isHeldExclusively()   //该线程是否正在独占资源。只有用到condition才需要去实现它。
tryAcquire(int)       //独占方式。尝试获取资源
tryRelease(int)       //独占方式。尝试释放资源
tryAcquireShared(int) //共享方式。尝试获取资源
tryReleaseShared(int) //共享方式。尝试释放资源
```

#### Semaphore(信号量)

```
public class SemaphoreExample {

    public static void main(String[] args) {
        final int clientCount = 3;
        final int totalRequestCount = 10;
        Semaphore semaphore = new Semaphore(clientCount);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalRequestCount; i++) {
            executorService.execute(()->{
                try {
                    semaphore.acquire();
                    System.out.print(semaphore.availablePermits() + " ");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();
                }
            });
        }
        executorService.shutdown();
    }
}
```

##### 允许多个线程同时访问

#### CountDownLatch （倒计时器）

```
public class CountdownLatchExample {

    public static void main(String[] args) throws InterruptedException {
        final int totalThread = 10;
        CountDownLatch countDownLatch = new CountDownLatch(totalThread);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalThread; i++) {
            executorService.execute(() -> {
                System.out.print("run..");
                countDownLatch.countDown();
            });
        }
        countDownLatch.await();
        System.out.println("end");
        executorService.shutdown();
    }
}
```

##### 典型用法

###### 某一线程在开始运行前等待 n 个线程执行完毕。

###### 实现多个线程开始执行任务的最大并行性。

##### 不足

###### CountDownLatch 是一次性的，计数器值只构造时初始化一次，用完不能再用。

##### 一个或者多个线程，等待其他多个线程完成某件事情之后才能执行 

#### CyclicBarrier(循环栅栏)

```
public class CyclicBarrierExample {

    public static void main(String[] args) {
        final int totalThread = 10;
        CyclicBarrier cyclicBarrier = new CyclicBarrier(totalThread);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalThread; i++) {
            executorService.execute(() -> {
                System.out.print("before..");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException | BrokenBarrierException e) {
                    e.printStackTrace();
                }
                System.out.print("after..");
            });
        }
        executorService.shutdown();
    }
}
```

##### 应用场景

###### 用于多线程计算数据，最后合并计算结果

##### CyclicBarrier(int parties, Runnable barrierAction)

###### 用于在线程到达屏障时，优先执行barrierAction

##### 多个线程互相等待，直到到达同一个同步点，再继续一起执行 

#### CyclicBarrier与CountdownLatch 的区别

##### CountDownLatch 是计数器，只能使用一次，而 CyclicBarrier 的计数器提供 reset 方法，可以多次使用。

##### CountDownLatch 的实现是基于 AQS 的，而 CyclicBarrier是基于 ReentrantLock 和 Condition 的.

### 了解

#### 基本的线程机制

##### 定义任务

###### 实现Runnable接口，编写run()方法

##### Thread类

###### 将Runnable对象给Thread构造器来驱动

###### start()会快速返回

###### 线程调度机制是非确定性的

####### 早期JDK不会频繁对时间切片

##### 使用Executor

```
public static void main(String[] args) {
    ExecutorService executorService = Executors.newCachedThreadPool();
    for (int i = 0; i < 5; i++) {
        executorService.execute(new MyRunnable());
    }
    executorService.shutdown();
}
```

###### 客户端和任务执行间的间接层

###### 允许管理异步执行的任务，无需显式管理线程的生命周期

###### shutdown()

####### 防止新任务提交给Executor

####### 当前线程继续运行之前提交的所有任务

###### CachedThreadPool

####### 为每个任务创建一个线程

###### FixedThreadPool

####### 使用有限的线程集来执行所有提交的任务

####### 只有CachedThreadPool引发问题，才需切换FixedThreadPool

###### SingleThreadExecutor

####### 线程数为1的FixedThreadPool，所有任务使用相同的线程

####### 多个任务将排队，顺序执行

####### 序列化任务，不需要同步共享资源

##### 从任务中产生返回值

###### Runnable不返回任何值

###### 实现Callable接口

####### 编写call()

####### ExecutorService.submit()调用产生Future对象

####### Future.get()获取返回结果

##### 休眠

###### sleep()

####### InterruptedException

##### 优先级

###### 将线程的重要性传递给调度器

###### JDK 10个优先级

###### 只使用

####### MAX_PRIORITY

####### NORM_PRIORITY

####### MIN_PRIORITY

##### 让步

###### yield()

####### Thread.yield()建议线程调度器切换线程

##### 后台(daemon)线程

```
public static void main(String[] args) {
    Thread thread = new Thread(new MyRunnable());
    thread.setDaemon(true);
    thread.start();
}
```

###### 在后台提供通用服务

###### 不是程序中不可或缺的部分

###### 所有非后台线程结束后，会杀死进程的所有后台线程

###### setDaemon()将线程设置为后台线程

##### Thread继承

```
public class MyThread extends Thread {
	public void run() {
    	// ...
    }
}

public static void main(String[] args) {
    MyThread mt = new MyThread();
    mt.start();
}
```

###### 实现接口可以继承另一个类，Thread继承将不行

##### 加入一个线程

###### 线程t1中调用t2.join()，t1挂起，直至线程t2结束才恢复

#### 共享受限资源

##### 解决共享资源竞争

###### why

####### 你永远都不知道一个线程何时在运行

###### 对资源加锁

####### 序列化访问共享资源

####### 互斥量-mutex

####### synchronized

######## 流程

######### 检查锁是否可用

######### 获取锁

######### 执行代码

######### 释放锁

######## 特定对象

######### 所有synchronized方法共享一个锁

######### 一个syn方法可调用其他syn方法，锁计数累加

######## 每个类

######### synchronized static 方法共享一个锁

###### 显式的Lock对象

####### 代码缺乏优雅性

####### 相比于 wait() 这种等待方式，await() 可以指定等待的条件，更加灵活

####### 更细粒度的控制

##### 原子性与易变性

###### 原子性

####### 原子操作不能被线程调度机制中断

####### 应用中的可视性

######## 对volatile域修改

######## 所有读操作能立刻看到修改

####### 原子性操作

######## 自增加不是原子性操作

######## long和double外的基本类型的读取和赋值操作是原子性操作

####### long和double

######## 两个分离的32位操作

######## 字撕裂

###### volatile

####### 读取和写入直接针对内存，不被缓存

####### 类中只有一个可变的域时，优于synchronized

##### 原子类

###### AtomicInteger

###### AtomicLong

###### AtomicReference

###### compareAndSet(exp, update)

##### 临界区

同步方法内部的部分代码，不是整个方法

###### 同步控制块：synchronized指定某个对象，对象锁对花括号内的代码同步

```
synchronized(syncObject) {
	// 代码段
}
```

###### 显式的Lock对象

```
lock.lock();
try {
	// 代码段
} finally {
	lock.unlock();
}
```

##### ThreadLocal

###### 根除对变量的共享，为相同变量的不同线程，创建不同存储

###### 只能通过get()和set()访问对象内容 

#### 终结任务

##### 在阻塞时终结

###### 线程状态

####### new 新建

######## 有资格获得CPU时间

####### Runnable 就绪

######## 只要调度器分配时间片给线程就可运行

####### Blocked 阻塞

######## 有某个条件阻止线程运行

######## 调度器忽略线程

####### Dead 死亡

######## 不再是可调度的

######## run()方法返回或被中断

###### 进入阻塞状态

####### 调用sleep()进入休眠

####### 调用wait()线程挂起

####### 等待某个输入/输出完成

####### 试图在某对象调用同步方法，但对象锁不可用

##### 中断

###### Thread.interrupt()

####### 对阻塞的线程中断抛出InterruptedException来退出线程

####### Executor.shutdownNow()中断所有线程

####### Executor.submit()返回Future<?>，然后调用cancel(true)可中断单一线程

####### sleep()的调用可中断，I/O和synchronized方法上的等待不可中断

###### 被互斥阻塞时

####### synchronized上的阻塞不可中断

####### ReentrantLock上的阻塞可中断

###### 检查中断

####### 在无阻塞时退出线程

####### interrupted()检查interrupt()是否调用来判断是否退出线程

#### 线程间的协作

##### 概述

###### 互斥-解决资源共享问题

###### 协作-多个任务一起工作，解决某个问题

###### 任务之间的握手

##### wait()、notify()、notifyAll()

###### wait()

####### 等待某个条件发生变化，这个变化由另一线程实现

####### 将任务挂起而不是忙等待，只有在notify()或notifyAll()才被唤醒

####### 与sleep()的区别

######## 对象上的锁释放，sleep()和yield()没有释放锁！

######## 通过notify()、notifyAll()或时间到期唤醒

###### notify()

####### 唤醒同个类所有对象中等待锁的一个任务

###### notifyAll()

####### 唤醒同个类所有对象中等待锁的所有任务

###### 只有在同步控制方法或同步控制块里调用

##### 错失的信号

###### 必须用一个检查条件的while循环包围wait()

####### 多个任务等待同一个锁时，令不满足条件的任务再次挂起

####### 任务被唤醒时，存在条件不满足，需重新挂起

###### 检查条件的while循环应放在同步内部，否则会发生死锁

##### 使用显式的Lock和Condition对象

###### 对lock()的调用必须用try-finally子句保证锁的释放

###### 调用await()、signal()或signalAll()之前必须拥有锁

##### 生产者-消费者与队列

###### wait()和notifyAll()是低级的任务操作，每次交互都握手

```java
public static class Storage {
    private static final int MAX_SIZE = 10;
    private final LinkedList<Object> list = new LinkedList<>();

    public void produce() {
        synchronized (list) {
            while (list.size() == MAX_SIZE) {
                list.wait();
            }
            list.add(new Object());
            list.notifyAll();
        }
    }

    public void consume() {
        synchronized (list) {
            while (list.size() == 0) {
                list.wait();
            }
            list.remove();
            list.notifyAll();
        }
    }
    
    public static void main(Stirng[] args) {
    	Storage storage = new Storage();
        
        Thread producer = new Thread(() -> {
        	while(true) {
                storage.produce();
                Thread.sleep(1000);
            }
        });
        Thread consumer = new Thread(() -> {
        	while(true) {
                storage.consume();
                Thread.sleep(1000);
            }
        });
        
        producer.start();
        consumer.start();
    }
}
```

###### 使用信号量解决生产者-消费者问题

生产值-消费者（有界缓冲区）问题：两个进程共享一个固定大小的缓冲区。生产者将信息放入缓冲区，消费者从缓冲区取出信息。缓冲区属于临界资源。

```
private LinkedList<Object> list = new LinkedList<Object>();
private static final int N = 10;
// 互斥量
private final Semaphore mutex = new Semaphore(1);
// 空位
private final Semaphore empty = new Semaphore(N);
// 满位
private final Semaphore full = new Semaphore(0);

public void producer() {
    while(true) {
    	empty.acquire();
        mutex.acquire();
        list.add(new Object());
        mutex.release();
        full.release();
        
        Thread.sleep(1000);
    }
}

public void consumer() {
    while(true) {
    	full.acquire();
        mutex.acquire();
        list.remove();
        mutex.release();
        empty.release();
        
        Thread.sleep(1000);
    }
}
```

###### 使用 BlockingQueue 实现生产者消费者问题

```
public class ProducerConsumer {

    private static BlockingQueue<String> queue = 
    	new ArrayBlockingQueue<>(5);

    private static class Producer extends Thread {
        @Override
        public void run() {
            try {
                queue.put("product");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.print("produce..");
        }
    }

    private static class Consumer extends Thread {

        @Override
        public void run() {
            try {
                String product = queue.take();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.print("consume..");
        }
    }
}
```

####### 同一时刻只允许一个任务插入或删除元素

####### BlockingQueue 

######## FIFO队列

######### LinkedBlockingQueue

########## 无界队列

######### ArrayBlockingQueue

########## 固定长度

######## 优先队列

######### PriorityBlockingQueue

##### 任务间使用管道输入/输出

###### PipedWriter

####### 允许任何任务向管道写

###### PipedReader

####### 允许不同任务从同一管道中读取

###### 读取和写入数据时可中断

#### 死锁

```
public class DeadLock {
    private static final Object resource1 = new Object();
    private static final Object resource2 = new Object();

    public static void main (String[] args) {
        new Thread(() -> {
            synchronized(resource1) {
            	Thread.sleep(1000);
            	synchronized(resource2) {
            		System.out.println("get resource2");
            	}
            }
        }).start();
        new Thread(() -> {
            synchronized(resource2) {
            	Thread.sleep(1000);
            	synchronized(resource1) {
            		System.out.println("get resource1");
            	}
            }
        }).start();
    }
}
```

##### 哲学家就餐

###### Chopstaicks

###### Philosopher

###### DeadlockingDining

##### 死锁条件

###### 互斥条件

####### 任务的资源至少有一个是不能共享的（筷子）

###### 持有资源

####### 一个任务持有一个资源并等待另一个资源

###### 资源不能被任务抢占

###### 循环等待

#### 新类库中的构件

##### CountDownLatch

###### 同步一个或多个任务，强制它们等待由其他任务执行的一组操作

####### 设置初始计数值

####### 等待的任务调用await()阻塞

####### 调用countDown()减小计数值

####### 计数值为0时唤醒等待任务

##### CyclicBarrier

###### 一组任务并行执行，在进行下一个步骤前等待，直至所有任务都完成

####### 设置初始计数值和Runnable栅栏动作

####### 每个任务调用await()时计数值减1

####### 计数值为0时执行Runnable栅栏动作

####### 唤醒各个任务继续执行

##### DelayQueue

###### 无界的BlockingQueue，放置实现了Delayed的接口对象

###### 队头对象是延迟到期后最久的，对象只能在到期时才能从队列取走

###### Delayed接口

####### getDelay()返回延迟到期多久时间

####### compareTo()对到期时间排序

##### PriorityBlockingQueue

###### 优先级队列，具有可阻塞的读取操作

##### Semaphore

###### 计数信号量允许n个任务同时访问一个资源

##### Exchanger

###### 两个任务交换对象的栅栏

#### 性能调优

##### 免锁容器

###### 原理

####### 对容器的修改可以与读取操作同时发生，但读取者只能看到完成修改的结果

####### 修改在容器数据结果的副本执行，在修改过程中不可视

###### CopyOnWriteArrayList

###### CopyOnWriteArraySet

###### ConcurrentHashMap

###### ConcurrentLinkedQueue

##### 乐观加锁

###### 保持数据未锁定状态

##### ReadWriteLock

###### 优化不频繁写入，但多个任务经常读取的数据结构

###### 如果写锁被持有，任何读取者都不能访问

#### 总结

##### 线程好处

###### 轻量级的执行上下文切换（大约100条指令），进程需要上千条指令

###### 进程需改变所有内存空间，而线程只改变程序的执行序列和局部变量

##### 多线程缺点

###### 等待共享资源时，性能↓

###### 处理线程，额外CPU

###### 糟糕的程序设计，导致不必要的复杂度

###### 病态行为：竞争、死锁

###### 不同平台导致的不一致性
