# Java 基础与容器

<!--Note-->
## 资料

- 《JAVA 核心技术》
- 《Java 编程思想》
- 《Effective java 中文版》
- 《深入理解 Java 虚拟机》
- 《Java 并发编程实战》
- 《精通 Spring 4.x》
- 《Spring 揭秘》
<!--/Note-->

## 基础

- [Java 基础](https://cyc2018.github.io/CS-Notes/#/notes/Java%20基础)

### 数据类型

#### 基本数据类型

##### byte

###### 8bit，1字节

##### char

###### 16bit，2字节

##### short

###### 16bit，2字节

##### int

###### 32bit，4字节

##### float

###### 32bit，4字节

##### long

###### 64bit，8字节

##### double

###### 64bit，8字节

##### boolean

###### 用int代替，boolean数组用byte数组代替

#### 包装类型

```

Integer x = 2;     // 装箱 调用了 Integer.valueOf(2)
int y = x;         // 拆箱 调用了 X.intValue()
```

##### 缓存池

###### new Integer(123) 每次都会新建一个对象

###### Integer.valueOf(123) 会使用缓存池中的对象

###### 基本类缓存池范围

- boolean values true and false
- all byte values
- short values between -128 and 127
- int values between -128 and 127
- char in the range \u0000 to \u007F

### Java 特性

#### 面向对象和面向过程

##### 面向过程

###### 性能更高

####### 因为类调用时需要实例化，开销比较大，比较消耗资源

####### 因此单片机、嵌入式开发、Linux/Unix 等一般采用面向过程开发（C语言）

##### 面向对象

###### 易维护、易复用、易扩展

####### 由于封装、继承、多态的特性

#### JVM、JDK 和 JRE

##### JVM

###### JVM 有针对不同系统的特定实现（Windows，Linux，macOS），目的是使用相同的字节码，它们都会给出相同的结果。

######  

##### JDK

###### 用于支持 Java 程序开发的最小环境

###### Java 语言 + Java 虚拟机 + Java API 类库

##### JRE

###### 支持 Java 程序运行的标准环境

###### Java 虚拟机 + Java API 类库中的 Java SE API 子集

#### Java 和 C++

##### Java 是纯粹的面向对象语言，所有的对象都继承自 java.lang.Object，C++ 即支持面向对象也支持面向过程。

##### Java 通过虚拟机从而实现跨平台特性，但是 C++ 依赖于特定的平台。

##### Java 没有指针，它的引用可以理解为安全指针，而 C++ 具有和 C 一样的指针。

##### Java 支持自动垃圾回收，而 C++ 需要手动回收。

##### Java 不支持多重继承，只能通过实现多个接口来达到相同目的，而 C++ 支持多重继承。

##### Java 不支持操作符重载，而 C++ 支持操作符重载。

##### Java 语言中没有结束符，C++字符串或字符数组最后都会 用‘\0’来表示结束。

#### Java 8 新特性 

##### 接口的默认方法

```
interface Formula {
    double calculate(int a);

    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}
```

##### Lambda 表达式

```
List<String> names = Arrays.asList("peter", "anna", "mike", "xenia");

Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return b.compareTo(a);
    }
});
```

```
Collections.sort(names, (a, b) -> b.compareTo(a));
```

###### 允许把方法作为参数，但必须是函数式接口的方法

##### 函数式接口


```
@FunctionalInterface
interface Converter<F, T> {
    T convert(F from);
}
Converter<String, Integer> converter = (from) -> Integer.valueOf(from);
Integer converted = converter.convert("123");
System.out.println(converted);    // 123
```

###### 是指 只有一个抽象方法 的接口，每个该类型的lambda表达式都会被匹配到这个抽象方法。

##### 方法与构造函数引用

###### 通过 className :: funcName / new 来指定一个方法 / 构造器，一般和 Lambda 联用

##### Date Time API 

###### LocalDate/LocalTime 和 LocalDateTime

###### Timezones 时区

##### Stream API

##### Optional 类 

###### 是一个可以为null的容器对象，用来解决空指针异常

### 继承

#### 重载和重写

##### 重写

###### 子类对父类的允许访问的方法的实现过程进行重新编写,发生在子类中，方法名、参数列表必须相同

###### 限制

####### 访问权限 >= 父类

####### 返回类型 <= 父类

即为父类方法返回类型或其子类型

####### 异常类型 <= 父类

##### 重载

###### 发生在同一个类中，方法名必须相同，参数类型、个数、顺序至少有一个不同。

#### 接口和抽象类

##### 接口

###### 方法

####### 默认 public abstract，不能实现（JDK 8 默认方法可实现） 

###### 字段

####### 默认 public static final

###### JDK 8 引入默认方法和静态方法，JDK 9 引入私有方法和私有静态方法

##### 抽象类

###### 抽象方法

####### abstract 声明，可为public、protected、default

###### 非抽象方法

##### 比较

###### 一个类可以实现多个接口，但是不能继承多个抽象类。

###### 接口的字段只能是 static 和 final 类型的，而抽象类的字段没有这种限制。

###### 接口的成员只能是 public 的，而抽象类的成员可以有多种访问权限

##### 使用选择

###### 使用接口

####### 需要让不相关的类都实现一个方法

####### 需要使用多重继承

###### 使用抽象类

####### 需要在几个相关的类中共享代码

####### 需要能控制继承来的成员的访问权限，而不是都为 public。

####### 需要继承非静态和非常量字段。

#### 继承情况下的初始化顺序

- 父类（静态变量、静态语句块）
- 子类（静态变量、静态语句块）
- 父类（实例变量、普通语句块）
- 父类（构造函数）
- 子类（实例变量、普通语句块）
- 子类（构造函数）

#### 匿名内部类

##### 要实现接口或继承的类，通过new向上转型

##### 访问的外部局部变量

###### 必须为final，以保证局部变量和内部类中的副本 的数据一致性

- 匿名内部类能使用外部类及局部变量，是因为他们隐含地作为构造方法参数传入
- 若局部变量不是final，当方法中改变原始变量或内部类中改变变量副本时，无法保持两者的一致性。

###### 1.8以后，并非不用是final的，而是在编译期间要求值不发生变化。

##### 访问实例变量与静态变量

###### 无须为final，因为隐含构造传入的是对象的引用（this），该引用不会变

### String, StringBuffer and StringBuilder

#### 可变性

##### String 不可变

```
// Java 8
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    // 使用final关键字修饰
    private final char value[];
}
```

```
// Java 9
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final byte[] value;

    /** The identifier of the encoding used to encode the bytes in {@code value}. */
    private final byte coder;
}
```

###### 可缓存 hash 值

###### String Pool 的需要

###### 线程安全

##### StringBuffer 和 StringBuilder 可变

###### StringBuilder 与 StringBuffer 都继承自 AbstractStringBuilder 类

```
abstract class AbstractStringBuilder implements Appendable, CharSequence {
    /**
     * The value is used for character storage.
     */
    // 没有用 final 关键字修饰
    char[] value;

    /**
     * The count is the number of characters used.
     */
    int count;

    AbstractStringBuilder(int capacity) {
        value = new char[capacity];
    }
```

#### 线程安全

##### String 不可变，因此是线程安全的

##### StringBuilder 不是线程安全的

##### StringBuffer 内部用 synchronized 同步，是线程安全的

#### 性能

##### 每次对 String 进行操作都还生成新对象，对 StringBuilder 和 StringBuffer 进行操作则不会

#####  StringBuilder 不需要同步，性能比 StringBuffer 高

### Object 通用方法

```

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

##### equals() 和 ==

###### == 判断两个对象的地址是否相等，而 equals() 判断两个对象是否等价

#### hashCode()

##### hashCode() 的作用就是获取哈希码，哈希码的作用是确定该对象在哈希表中的索引位置。

##### 等价的两个对象散列值一定相同，但是散列值相同的两个对象不一定等价

##### 在覆盖 equals() 方法时应当总是覆盖 hashCode() 方法，保证等价的两个对象哈希值也相等。

#### toString()

##### 默认返回： 类名@散列码

#### clone()

##### cloneable

###### clone() 是 Object 的 protected 方法，没有重写的类无法调用 clone() 

###### 重写 clone() 需要实现 Cloneable 接口，否则抛出 CloneNotSupportedException

```
public class CloneExample implements Cloneable {
    private int a;
    private int b;

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

##### 浅拷贝

```
public class ShallowCloneExample implements Cloneable {

    private int[] arr;

    public ShallowCloneExample() {
        arr = new int[10];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = i;
        }
    }

    @Override
    protected ShallowCloneExample clone() throws CloneNotSupportedException {
        return (ShallowCloneExample) super.clone();
    }
}

```

###### 生成新的对象中，对基本数据类型进行值传递，对引用数据类型，进行引用指向同一对象

##### 深拷贝

```
public class DeepCloneExample implements Cloneable {

    private int[] arr;

    public DeepCloneExample() {
        arr = new int[10];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = i;
        }
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

```

###### 生成新的对象中，对基本数据类型进行值传递，对引用数据类型，创建一个新的对象，并复制其内容

### 泛型

#### 引入原因

##### 多态

###### 方法的参数是基类

####### 具备很好的灵活性，但有性能损耗

###### 方法的参数是接口

####### 实现该接口的类都能够满足该方法，但是必须满足特定的接口

##### 泛型

###### 更通用→某种不确定的类型（接口）

###### 参数化类型（容器类）

#### 泛型

##### 简单泛型

###### 指定要持有什么类型的对象，置于<>内

###### 编译器→保证类型的正确性

##### 泛型接口

###### public interface Generator<T> { T next(); }

##### 泛型方法

###### public <T> void f(T x) {}

###### 尽量使用泛型方法，优于泛型类

#### 擦除

##### 在泛型代码内部，无法知道用来创建某个特例的实际类型参数

##### 使用泛型时，具体类型信息被擦除

###### List<String>和List<Integer>都被擦除成List

##### 与Java泛型不同，C++模板被实例化时，模板代码知道模板参数的类型

##### 擦除的补偿

###### 通过引入类型标签，即显式地传递类型的Class对象

```
public class ClassTypeCapture<T> {
    Class<T> kind;

    public ClassTypeCapture(Class<T> kind) {
        this.kind = kind;
    }

    public boolean f(Object arg) {
        return kind.isInstance(arg);
    }
}
```

#### 边界

编译器在编译期执行类型检查并插入转型代码的地点。

##### 泛型类型参数将擦除到它的第一个边界

###### 可以用边界类型调用方法

##### <T extends HasF>

###### 限制为HasF的类型子集

###### T擦除到HasF → 好比声明中用HasF替换T 

```
public class Manipulator2<T extends HasF> {
    private T obj;

    Manipulator2(T x) {
        obj = x;
    }

    public void manipulate() {
        obj.f();
    }
}

```
等同于
```
public class Manipulator2 {
    private HasF obj;

    Manipulator2(HasF x) {
        obj = x;
    }

    public void manipulate() {
        obj.f();
    }
}
```


#### 通配符

##### <? extends Fruit>

###### 持有某种具体的Fruit或Fruit的子类型

###### LIst<Apple>不可以向上转型为List<Fruit>, 但可以向上转型为List<? extends Fruit>

##### <? super T>

###### 超类型通配符（参数是下界）

###### 某个特定类的任何基类

##### 无界通配符<?>

###### 等价于使用原生类型

####### List：可以持有任何Object类型的原生 List

####### List<?>: 只能持有某种特定类型的非原生 List

```
SuperClass<?> sup = new SuperClass<String>("lisi");

sup = new SuperClass<People>(new People());

sup = new SuperClass<Animal>(new Animal());

若不用?,用固定的类型的话，则：

SuperClass<String> sup1 = new SuperClass<String>("lisi");

SuperClass<People> sup2 = new SuperClass<People>("lisi");

SuperClass<Animal> sup3 = new SuperClass<Animal>("lisi");

这就是?通配符的好处。
```

### 异常

#### 基本异常：new创建异常对象->停止程序->弹出异常对象引用->异常处理程序->终止或恢复程序

#### Throwable

##### Error

###### JVM报告系统错误,，程序无法控制和处理

####### OutOfMemoryError

####### Virtual MachineError

###### “不受检测异常”

##### Exception

###### “检测异常”

####### 编译期检查

######## IOException 

######## SQLException

######## 用户自定义Exception

####### 需要try-catch捕获

###### “不受检测异常”

####### 运行时异常（RuntimeException）

######## ArrayIndexOutOfBoundsException

######## NullPointerException

######## ArithmeticException（除0）

######## ClassNotFoundException

####### 自动捕获，可抛出

##### 图

######  

#### try-catch-finally

##### 异常处理程序catch

###### 仅处理匹配的catch子句

###### 派生类对象，可以匹配基类

##### finally

###### 子句总会执行

####### 即使try中有return！

###### 保证内存释放和资源清理

#### printStackTrace()

##### 从方法调用处→异常抛出处的方法调用序列

##### 标准错误流

##### 栈顶是最后一个方法调用，栈底是第一个方法调用

### 反射

#### 基本运用

##### 获取 Class 对象

###### Class 类的静态 forName()

```

Class<?> klass = Class.forName("com.mysql.jdbc.Driver");
```

###### T.class

```

Class<?> klass1 = int.class;
Class<?> klass2 = Double[].class;
```

###### Object 类的 getClass()

```

StringBuilder str = new StringBuilder("123");
Class<?> klass = str.getClass();
```

##### 判断是否为某个类的实例

```

if (A.Class.isInstance(a)) {
};
```

##### 创建实例

###### newInstance()

```
Class<?> c = String.class;
Object str = c.newInstance();

```

##### java.lang.reflect

###### Field 类

####### 可以使用 get() 和 set() 方法读取和修改 Field 对象关联的字段

```

Class<?> c = Student.class;
Field f = c.getField("name");
Constructor constructor = c.getConstructor(String.class);
Object obj = constructor.newInstance();
//为Student对象中的name属性赋值
f.set(obj, "zhangsan");
```

###### Method 类

####### 可以使用 invoke() 方法调用与 Method 对象关联的方法

```

Class<?> klass = MethodClass.class;
Object obj = klass.newInstance();

//获取methodClass类的add方法
Method method = klass.getMethod("add",int.class,int.class);
//调用method对应的方法 => add(1,4)
Object result = method.invoke(obj,1,4);
```


###### Constructor 类

####### 可以用 Constructor 的 newInstance() 创建新的对象

```

Class<?> c = String.class;
//获取String类带一个String参数的构造器
Constructor constructor = c.getConstructor(String.class);
//根据构造器创建实例
Object obj = constructor.newInstance("23333");
```

#### 优点

##### 可扩展性

###### 应用程序可以利用全限定名创建可扩展对象的实例，来使用来自外部的用户自定义类。

##### 可视化开发环境

###### 可视化开发环境（如 IDE）可以从利用反射中可用的类型信息中受益，以帮助程序员编写正确的代码

##### 调试器和测试工具

###### 调试器需要能够检查一个类里的私有成员。测试工具可以利用反射来自动地调用类里定义的可被发现的 API 定义

#### 缺点

##### 性能开销

###### 反射涉及了动态类型的解析，所以 JVM 无法对这些代码进行优化

##### 安全限制

###### 使用反射技术要求程序必须在一个没有安全限制的环境中运行

##### 内部暴露  

###### 反射允访问私有的属性和方法, 可能导致代码功能失调并破坏可移植性

## 容器

- [Java 容器](https://cyc2018.github.io/CS-Notes/#/notes/Java%20容器)

### 容器基础

#### 容器 Collection

##### 基本概念

###### 用途

####### 保存对象

####### 编译期×错误类型→容器

####### 自动调整尺寸

###### 向上转型为接口 

例如，`
List<Apple> apples = new ArrayList<>();
`

####### 要改变具体实现，只需在创建时修改

####### 但接口中不包含具体额外方法

###### 说明

####### 独立元素的序列

####### 槽内只有一个元素

##### List

按照插入顺序

###### ArrayList

####### 基于动态数组实现，支持随机访问

####### 中间插入和移除低效，随机访问高效

###### LinkedList

####### 基于双向链表实现，只能顺序访问

####### 中间插入和移除高效，随机访问低效

####### 可以被用作栈、队列或双端队列（Deque）

###### Stack

####### 后进先出 LIFO

###### Vector

####### 线程安全

##### Set

元素不能重复

###### HashSet

####### 散列函数→查找速度快

####### 存储顺序不重要

###### TreeSet

####### 红黑树→排序

####### 存储顺序很重要

###### LinkedHashSet

####### 散列函数→查找速度快

####### 链表维护插入顺序

##### Queue

先进先出 FIFO→并发编程

###### PriorityQueue

###### LinkedList实现

##### Stack

###### Deque<String> stack = new ArrayDeque<>();

###### java.util.Stack

#### Map

##### 映射表：“键值对”对象

##### 槽内有两个对象：键和与之关联的值

##### 分类

###### HashMap

####### 快速访问

###### TreeMap

####### 保持排序

###### LinkedHashMap

####### 链表维护插入顺序，哈希提供快速访问

#### 迭代器 Iterator

##### 统一了对容器的访问方式

##### 只能单向移动

##### foreach隐形包括迭代器

##### 方法

###### Collection中实现

####### interator()

######## 令容器返回一个Iterator

###### Iterator中实现

####### next()

####### hasNext()

####### remove()

#### Collection 可以通过继承 AbstractCollection 实现，必须实现 iterator（）

#### 容器中的设计模式

##### for-in和迭代器

```
@Override
  public Iterator<String> iterator() {
    return new Iterator<String>() {
      private int index = 0;
      @Override
      public boolean hasNext() {
        return index < words.length;
      }
      @Override
      public String next() { return words[index++]; }
      @Override
      public void remove() { // Not implemented
        throw new UnsupportedOperationException();
      }
    };
  }
```

######  Iterable 接口 → 包含iterator() 方法 → 生成 Iterator 实例 → for-in 遍历序列。

##### 适配器方法

生成反向迭代器，不能使用覆盖，重新生成。
```
public Iterable<T> reversed() {
    return new Iterable<T>() {
      public Iterator<T> iterator() {
        return new Iterator<T>() {
          int current = size() - 1;
          public boolean hasNext() {
            return current > -1;
          }
          public T next() { return get(current--); }
          public void remove() { // Not implemented
            throw new UnsupportedOperationException();
          }
        };
      }
    };
  }
```

#### 容器分类 

- 黑色粗线框表示常用容器；
- 虚线框表示接口；
- 实线框表示普通类；
- 虚线空心箭头表示特定类实现接口；
- 实心箭头表示某个类可以生成所指向类的对象。

### ArrayList

#### RandomAccess接口 -> 快速随机访问

```
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

#### 扩容

```
/**
 * ArrayList扩容的核心方法。
 */
private void grow(int minCapacity) {

    // oldCapacity为旧容量，newCapacity为新容量
    int oldCapacity = elementData.length;
    
    // 使用右移将新容量更新为旧容量的1.5倍，
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    
    // 检查新容量是否大于最小需要容量，
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
        
    // 检查新容量是否超出了ArrayList所定义的最大容量
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);

    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

##### ensureCapacityInternal() 保证容量足够

```
// size为元素个数，elementData.length为数组长度
// if (size + 1 - elementData.length > 0)
//     grow(minCapacity);

public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}

// 小于默认容量10，则取10
private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    ensureExplicitCapacity(minCapacity);
}

// 判断是否需要扩容
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;
    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
```


```

##### oldCapacity + (oldCapacity >> 1) 扩容1.5倍

##### Arrays.copyOf() 复制到新数组

#### 插入删除元素

```
public E remove(int index) {
    rangeCheck(index);
    modCount++;
    E oldValue = elementData(index);
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index, numMoved);
    elementData[--size] = null; // clear to let GC do its work
    return oldValue;
}
```

##### System.arraycopy()将index+1后的numMoved个元素前移一位

######  

#### 序列化

##### 保存元素的数组elementData不被序列化

```
transient Object[] elementData
```

##### 只序列化有元素的那部分内容

#### Fail-Fast

##### modCount

###### 记录 ArrayList 结构发生变化的次数

##### 在进行序列化或者迭代等操作时，若modCount改变则抛出异常

#### 与LinkedList的区别

##### 底层数据结构

###### ArrayList为动态数组，LinkedList为双向链表

##### 操作复杂度

###### 对于随机访问get和set，ArrayList优于LinkedList，因为LinkedList要移动指针。

###### 对于新增和删除操作add和remove，LinkedList比较占优势，因为ArrayList要移动数据。

##### ArrayList支持快速随机访问，LinkedList不支持

###### ArrayList优先选择for循环遍历，LinkedList优先选择foreach遍历

##### 空间浪费

###### ArrayList需要在数组尾部预留一定空间

###### LinkedList每个元素消耗更多的空间

#### 与Vector的区别

##### Vector的所有方法都是同步的，开销更大

##### Vector 每次扩容大小为 2 倍，而 ArrayList 是 1.5 倍。

### HashMap

#### 存储结构

##### JDK 1.7

###### Entry数组+链表（拉链法）

#######  

##### JDK 1.8

###### 数组+链表+红黑树

####### 数组长度>=64，链表长度>=8时，转红黑树

########  

#### 扩容

##### loadFactor

###### loadFactor越趋近于1，存放数据越多，越密，链表越长

##### thread = capacity * loadFactor

###### 超过该值则扩容

##### resize()

###### 扩为原来的2倍，遍历所有元素，将所有键值对重新插入newTable

###### JDK 1.7

```
void resize(int newCapacity) {
    Entry[] oldTable = table;
    int oldCapacity = oldTable.length;
    if (oldCapacity == MAXIMUM_CAPACITY) {
        threshold = Integer.MAX_VALUE;
        return;
    }
    Entry[] newTable = new Entry[newCapacity];
    transfer(newTable);
    table = newTable;
    threshold = (int)(newCapacity * loadFactor);
}

```

####### 原方法计算位置：hashCode() -> hash() -> (n - 1) & hash

```
// oldTable的所有键值对转移到newTable
void transfer(Entry[] newTable) {
    Entry[] src = table;
    int newCapacity = newTable.length;
    // 遍历所有元素
    for (int j = 0; j < src.length; j++) {
        Entry<K,V> e = src[j];
        if (e != null) {
            src[j] = null;
            do {
                Entry<K,V> next = e.next;
                // ReHash：重新计算hash
                int i = indexFor(e.hash, newCapacity);
                e.next = newTable[i];
                newTable[i] = e;
                e = next;
            } while (e != null);
        }
    }
}
```

###### JDK 1.8

```
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;
    if (oldCap > 0) {
        // 超过最大值就不再扩充了，就只好随你碰撞去吧
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        // 没超过最大值，就扩充为原来的2倍
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY && oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else { 
        // signifies using defaults
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    // 计算新的resize上限
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ? (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {
        // 把每个bucket都移动到新的buckets中
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { 
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        // 原位置不变
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        // 原位置 + oldCap
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    // 原位置放到bucket里
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    // 原位置 + oldCap放到bucket里
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}

```

####### 扩容后位置 = 原位置 or  (原位置 + 旧容量)

```
// newCap = oldCap << 1
oldCap              : 00010000
newCap              : 00100000

// hash & oldCap == 0
// hash % newCap == hash % oldCap
hash(key1)          : 00100011
hash(key1) % oldCap : 00000011
hash(key1) % newCap : 00000011

// hash & oldCap != 0
// hash % newCap == hash % oldCap + oldCap
hash(key2)          : 00110011
hash(key2) % oldCap : 00000011
hash(key2) % newCap : 00010011
```


#### 计算桶下标

```
// i = hash(key) % n, n为数组长度
int hash = hash(key);
int i = (n - 1) & hash;
```

##### 扰动函数hash方法

###### JDK1.7

```
final int hash(Object k) {
    int h = hashSeed;
    if (0 != h && k instanceof String) {
        return sun.misc.Hashing.stringHash32((String) k);
    }
	// 1次异或运算
    h ^= k.hashCode();

    // 2次位运算 + 2次异或运算 
    h ^= (h >>> 20) ^ (h >>> 12);
    
    // 2次位运算 + 2次异或运算
    return h ^ (h >>> 7) ^ (h >>> 4);
}
```

####### 9次扰动

###### JDK1.8

```
// JDK 1.8
static final int hash(Object key) {
    int h;
    // 1次位运算 + 1次异或运算
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

####### 2次扰动，性能更好

###### 防止比较差的hashCode()方法，减少碰撞

##### 取模 (n - 1) & hash

```
// x为2的k次方时
x       : 00010000
x-1     : 00001111
y       : 10110010
y&(x-1) : 00000010
y%x     : 00000010
```

###### 位运算代替模运算，性能更高

#### put方法

##### JDK1.7

```
public V put(K key, V value) {
    if (table == EMPTY_TABLE) {
        inflateTable(threshold);
    }
    // 键为 null 时，存放在第0个桶
    if (key == null)
        return putForNullKey(value);
    int hash = hash(key);
    // 确定桶下标
    int i = indexFor(hash, table.length);
    // 遍历对比 key，有相同则直接覆盖
    for (Entry<K,V> e = table[i]; e != null; e = e.next) {
        Object k;
        if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
            V oldValue = e.value;
            e.value = value;
            e.recordAccess(this);
            return oldValue;
        }
    }

    modCount++;
    // 插入新键值对
    addEntry(hash, key, value, i);
    return null;
}
```

###### 先判断扩容，再插入键值对

```
void addEntry(int hash, K key, V value, int bucketIndex) {
	// 元素个数 >= 阈值，且插入下标的桶不为空，则扩容
    if ((size >= threshold) && (null != table[bucketIndex])) {
        resize(2 * table.length);
        hash = (null != key) ? hash(key) : 0;
        bucketIndex = indexFor(hash, table.length);
    }

    createEntry(hash, key, value, bucketIndex);
}
```

###### 新键值对采用头插法插入

```
void createEntry(int hash, K key, V value, int bucketIndex) {
    Entry<K,V> e = table[bucketIndex];
    // 头插法，链表头部指向新的键值对
    table[bucketIndex] = new Entry<>(hash, key, value, e);
    size++;
}
```

##### JDK1.8

```
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    // table未初始化或者长度为0，进行扩容
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
        
    // 计算桶下标，桶中无元素则直接插入
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
        
    // 桶中已经存在元素
    else {
        Node<K,V> e; K k;
        // 比较桶中第一个元素的key值
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
        // 若为树结点
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        // 为链表结点
        else {
        	// 遍历链表
            for (int binCount = 0; ; ++binCount) {
                // 到达链表尾部,采用尾插法插入
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    // 达到阈值转红黑树
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                // 判断key是否相等，相等则跳出
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;

                p = e;
            }
        }
        // 表示在桶中找到相等key值，更新值
        if (e != null) { 
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    // 最后判断是否扩容
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
} 

```

###### 新键值对采用尾插法插入

####### 不容易出现循环链表

###### 先插入键值对，最后统一判断扩容

###### 

#######  

#### 计算数组容量

```
static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

##### 传入容量会自动转为大于该值的最小2的幂

```
// mask+1为大于num的最小2的幂
num                  10010000
mask |= mask >> 1    11011000
mask |= mask >> 2    11111110
mask |= mask >> 4    11111111
mask+1              100000000
```

#### 1.7 与 1.8 的区别

#####  

#### HashMap 多线程操作导致死循环问题

##### 主要原因在于并发下的Rehash 会造成元素之间会形成一个循环链表。

JDK 1.8已解决，但多线程下HashMap仍存在问题。

#### 与Hashtable比较

##### Hashtable 使用 synchronized 同步，开销更大，HashMap 是非线程安全的

##### HashMap 可以插入键为 null，HashTable 会抛 NullPointerException

##### Hashtable初始容量为11，扩容为2n+1, HashMap初始容量为16，扩容为2n

##### JDK 1.8后HashMap会转红黑树，Hashtable不会

##### HashMap 的迭代器是 fail-fast 迭代器

##### HashMap 不能保证随着时间的推移 Map 中的元素次序是不变的

#### 与HashSet对比

##### HashSet基于HashMap实现，实现Set接口

```
// 一个固定对象，用于封闭HashMap的value
private static final Object PRESENT = new Object();

public HashSet() {
     map = new HashMap<>();
}

public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}
```

### ConcurrentHashMap

#### 存储结构

##### JDK 1.7

###### Segment 数组 + HashEntry 数组 + 链表

#######  

##### JDK 1.8

###### Node 数组 + 链表 / 红黑树

#######  

##### HashTable

###### Entry 数组 + 链表

#######  

#### 线程安全

##### JDK 1.7

###### 分段锁 (Segment)

```
// 继承自ReentrantLock
static final class Segment<K,V> extends ReentrantLock implements Serializable {

    private static final long serialVersionUID = 2249069246763182397L;

    static final int MAX_SCAN_RETRIES =
        Runtime.getRuntime().availableProcessors() > 1 ? 64 : 1;
	// HashEntry数组
    transient volatile HashEntry<K,V>[] table;

    transient int count;

    transient int modCount;

    transient int threshold;

    final float loadFactor;
}
```
```
// Segment数组
final Segment<K,V>[] segments;
```

####### 并发度就是 Segment 的个数

###### ReentrantLock

##### JDK 1.8

###### 锁定链表或红黑树的首节点

###### CAS +  synchronized

##### HashTable

###### 同一把锁

###### synchronized

### LinkedHashMap

#### 继承自 HashMap，双向链表维护插入或LRU顺序

#####  

#### afterNodeAccess()

```
void afterNodeAccess(Node<K,V> e) { // move node to last
    LinkedHashMap.Entry<K,V> last;
    if (accessOrder && (last = tail) != e) {
        LinkedHashMap.Entry<K,V> p =
            (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
        p.after = null;
        if (b == null)
            head = a;
        else
            b.after = a;
        if (a != null)
            a.before = b;
        else
            last = b;
        if (last == null)
            head = p;
        else {
            p.before = last;
            last.after = p;
        }
        tail = p;
        ++modCount;
    }
}
```

##### 若accessOrder == true，访问节点移至链表尾部

#### afterNodeInsertion()

```
void afterNodeInsertion(boolean evict) { // possibly remove eldest
    LinkedHashMap.Entry<K,V> first;
    // 根据条件判断是否移除最近最少被访问的节点
    if (evict && (first = head) != null && removeEldestEntry(first)) {
        K key = first.key;
        removeNode(hash(key), key, null, false, true);
    }
}

// 移除最近最少被访问条件之一，通过覆盖此方法可实现不同策略的缓存
protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
    return false;
}
```

##### 若removeEldestEntry(first)返回true，插入新节点后将移除首部节点 

#### LRU 缓存

```

class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private static final int MAX_ENTRIES = 3;
	
    LRUCache() {
        super(MAX_ENTRIES, 0.75f, true);
    }
    
    protected boolean removeEldestEntry(Map.Entry eldest) {
        return size() > MAX_ENTRIES;
    }
}
```

##### 设定MAX_ENTRIES

##### accessOrder设为true

##### 覆盖removeEldestEntry()方法返回true

## I/O

- [Java IO](https://cyc2018.github.io/CS-Notes/#/notes/Java%20IO)

### BIO

同步阻塞I/O模式

#### 传统BIO

##### 一旦建立通信，不能接受其他客户端请求

##### 同时处理多个客户端请求，就必须使用多线程

##### 线程创建和销毁成本高、并发访问量增加可能会导致线程堆栈溢出

#### 伪异步IO

##### 线程池机制

##### 防止由于海量并发接入导致线程耗尽

### NIO

同步非阻塞的I/O模型

#### Channel (通道)

##### 通道是双向的，可读也可写，而流的读写是单向的

#### Buffer(缓冲区)

##### IO 面向流，而 NIO 面向缓冲区

##### 状态变量

###### capacity

####### 最大容量

###### position

####### 已读写字节数

###### limit

####### 可读写字节数

#### Selector (选择器)

##### 轮询监听多个通道，因此需配置套接字通道为非阻塞

#####  阻塞在select()监听事件到达

### AIO
