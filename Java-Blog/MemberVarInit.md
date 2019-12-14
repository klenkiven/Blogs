# 成员和构造器初始化

## **成员初始化**
Java尽力保证所有变量可以在使用前可以初始化。
```java
void f(){
    int i;
    System.out.println(i);
    //! i++;   //开幕雷击：这里就报错了，会告诉你变量 i 未初始化
}

//Output: 0
```
这说明，数据成员在创建之初是有初始值的。但这并不代表java为数据成员提供了默认值。

### **指定初始化**
1. 直接赋值法
   ```java
    int i = 0;
    char ch = 'a';
     ```
2. 调用方法对数据成员赋值
   ```java
    public class MethodInit1{
        int i = f();    //调用方法对 i 赋值
        int f() { return 11; }
    }

    public class MethodInit2{
        int i = f();
        int j = g(i);   // i 变量已经被初始化可以这样做
        int f(){ return 11; }
        int g(int n){ return n*5 }
    }

    public class MethodInit3{
        int j = g(i);   // i 变量还未被初始化会报错
        int i = f();
        int f(){ return 11; }
        int g(int n){ return n*5 }
    }
   ```
    可见上述的程序运行情况取决于成员变量的初始化顺序，如果上面调用下面的成员变量`向前引用`就会发出警告。

## **构造器初始化**

new 一个类的时候，往往就会发生构**造器初始化**
1. 初始化顺序
   
   **首先**初始化变量成员，**最后**初始化构造器。即使成员变量分布在各个地方。
2. 静态数据的初始化

    - 无论创建多少个对象，静态数据都只占用一份储存区域
    - static关键字不能应用于局部变量，因此它只能作用于**域**
3. 非静态实例初始化

