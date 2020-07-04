# 实战 -- OOM异常

虚拟机的几个内存区域都有发生OOM异常的可能，因此能够异常信息**快速定位内存溢出区域**是一个非常重要的能力。

## Java堆溢出

> 只要不断地创建对象，并且保证GC Root到对象之间有可达路径就可以避免垃圾回收清除这些对象。
>
> 在对象数量达到最大堆地容量限制后就会产生内存溢出异常

```java
package xyz.klenkiven.jvm.OOM;

import java.util.ArrayList;
import java.util.List;

public class HeapOOM {
    static class OOMObject {
        private int i;

        public OOMObject(int a) {
            a = i;
        }
    }

    public static void main(String[] args) {
        List<OOMObject> list = new ArrayList<>();

        while (true) {
            list.add(new OOMObject(1));
        }
    }
}
//:Args: -Xms20m -Xmx20m -XX:+HeapDumpOnOurOfMemoryError
//代码限制了对内存的大小而且不可扩展
//堆地最小值-Xms和最大值-Xmx设置为一样就可以避免堆自动扩展
//-XX:+HeapDumpOnOurOfMemoryError这个参数可以Dump出一个当前堆内存快照方便事后分析
```

通过快照分析工具可以看到对象的储存情况，如果存在内存泄漏，那么某个或某些类地对象就会占用过多。如果不是内存泄漏的问题，也就是内存的确还在存活。那么就应该考虑是否**调大堆内存**的大小，或者是从代码上分析，是否是**对象生命周期过长**、**持有状态时间过长**的情况。

## 虚拟机栈和本地方法栈溢出

Java虚拟机栈里面定义了两种异常：

+ 栈深度大于虚拟机所允许的最大深度：**StackOverFlowError**
+ 扩展栈的时候不能申请到足够的空间：**OutOfMemoryError**

但是使用下面的两种方法并不会使得虚拟机出现OOM异常：

+ 使用`-Xss`参数减少内存的容量。结果是抛出StackOveerFlowError
+ 定义大量的本地变量，增大方法帧中本地变量表的长度。结果是抛出StackOveerFlowError

```java
package xyz.klenkiven.jvm.OOM;

//限制栈内存的大小
//:Args: -Xss128k
public class JavaVMStackEOF {

    private int stackLength = 1;

    //递归的方法测试栈的深度
    public void stackLeak() {
        stackLength++;
        stackLeak();
    }

    public static void main(String[] args) throws Throwable {
        JavaVMStackEOF oom = new JavaVMStackEOF();
        try {
            oom.stackLeak();
        } catch (Throwable e) {
            System.out.println("stack length: " + oom.stackLength);
            throw e;
        }
    }

}
```

> 单线程下，无论是由于栈帧太大还是虚拟机容量太小，当内存无法分配的的时候，虚拟机都抛出StackOverFlowError异常。

每个线程分配到的容量越大，可以建立的内存越少，建立内存的时候就越容易把剩下的内存耗尽。那么多线程编程的时候尤其要注意，出现StackOverFlowError时可以通过错误堆栈阅读排除问题。

## 方法区和运行时常量池溢出

### 常量池溢出

> 关于常量池的讨论：JDK1.6以前常量池被放到“永久代”里面存储；JDK1.7将常量池移出永久代，移动到堆里面了；JDK1.8完全抛弃永久代，取而代之的是，元空间（Metaspace）

```java
package xyz.klenkiven.jvm.OOM;

import java.util.ArrayList;
import java.util.List;

public class RuntimeConstantPoolOOM {

    public static void main(String[] args) {
        OOM2();
    }

    public static void OOM1() {
        //使用List保持常量池的引用，防止Full GC回收常量池
        List<String> list = new ArrayList<>();
        //10MB的PermSize在integer范围内足够产生OOM了
        int i = 0;
        while (true) {
            list.add(String.valueOf(i++).intern());
        }
    }

    public static void OOM2() {
        String str1 = new StringBuilder("计算机").append("软件").toString();
        System.out.println(str1.intern() == str1);

        String conStr = "AABB";

        //关于这个部分会在下面讨论
        String str2 = new StringBuilder("ja").append("va").toString();
        System.out.println(str2.intern() == str2);
    }
}

```

上面的程序如果是JDK1.6的版本会是两个false，但是在JDK1.8就会是一个true另一个是false。

+ JDK1.6中，intern()方法会把首次遇到的字符串实例复制到永久代中，返回的也是永久代中的字符串的实例引用，然而StringBuilder创建的字符串在Java堆里面，英雌必然不是同一个引用所以返回的是false

+ JDK1.8中，intern()函数不会再次复制实例，**只是在常量池中记录首次出现的实例引用**，因此StringBuilder里面创建的字符串是同一个。

  > str2返回false是因为“java”这个字符串在StringBuilder创建之前已经出现了。这里出现的位置我在常量表里面并未出现，而且！常量表中出现的字符串并不会被认为已经出现过了。Mamet就会出现一个有趣的问题，这个“Java”字符串是从哪里来的。
  >
  > 经过查阅资料发现，Java虚拟机启动的时候会调用System类，详见`initializeSystemClass`方法。这个会初始化`sun.mise.Version`类，这个类里面有一个变量`launcher-name`是“java”。这里就是这个字符串的源头。
  >
  > str1这个字符串并是首次出现的，因此返回true

+ 我的JDK版本是JDK1.8测试的时候需要限制元空间的大小， 使用参数`-XX:+MaxMetaSpaceSize`

### 方法区溢出

方法区用于存放**Class类相关的信息**，如类名，访问修饰符，常量池，字段描述，方法描述等等。

> 思路：通过创建大量的类去填满方法区，直到溢出。

```java
package xyz.klenkiven.jvm.OOM;

//在JDK1.6里面会产生OOM异常：OutOfMemoryError: PermGen space
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

public class JavaMethodOOM {
    public static void main(String[] args) {
        while (true) {
            Enhancer enhancer = new Enhancer();
            enhancer.setSuperclass(OOMObject.class);
            enhancer.setUseCache(false);
            enhancer.setCallback(new MethodInterceptor() {
                @Override
                public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                    return methodProxy.invokeSuper(o, args);
                }
            });
            enhancer.create();
        }
    }
    static class OOMObject {

    }
}
```

方法区溢出也是一件非常常见的事情，因为一个类的回收条件是非常苛刻的。在动态生成大量Class的应用中，需要特别注意回收情况。

## 本机直接内存溢出

DirctMemory容量可以通过`-XX:MaxDiractMemorySize`指定，如果不指定，则默认与Java堆最大值一样。

```java
package xyz.klenkiven.jvm.OOM;

import sun.misc.Unsafe;

import java.lang.reflect.Field;

//:VM Args: -Xmx20m -XX:MaxDirectMemorySize=10M
public class DirectMemoryOOM {
    private static final int _1M = 1024*1024;

    public static void main(String[] args) throws IllegalAccessException {

        Field unsafeField = Unsafe.class.getDeclaredFields()[0];
        unsafeField.setAccessible(true);
        Unsafe unsafe = (Unsafe) unsafeField.get(null);
        while (true) {
            unsafe.allocateMemory(_1M);
        }

    }
}
```

|          类名           |                           相关描述                           |
| :---------------------: | :----------------------------------------------------------: |
|   DirectMemoryBuffer    | 并没有向操作系统申请内存，只是经过计算后得知内存不够分配后，手动抛出OOM异常 |
| Unsafe.allocateMemory() |                    真正向操作系统申请内存                    |

+ 特征：

  直接内存溢出最大的特征是HeapDump文件不会看见明显的异常情况，而且HeapDump文件一般都很小。如果发现OOM异常，并且HeapDump文件特别小的话，就可以考虑是不是直接内存溢出了。