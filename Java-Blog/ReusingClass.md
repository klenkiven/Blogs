# 复用类

> **复用类**的方法：
> 1. **组合**：在新的类中产生现有类的对象
> 2. **继承**：按照现有类的类型来的类型来创建对象

## 一、组合语法

> 组合，顾名思义就是把不同的类组合起来组合，简单地将需要的类组合起来。

```java
class WaterSource {
    private String s;
    WaterSourse() {
        System.out.println("WaterSource()");
        s = "Constractor";
    }
    //toString()方法可以把相关的信息转换成String然后返回一个String对象
    public String toString() { return s; }
}
```

例如上述代码，直接把`String`类置于新的类中，实现了代码复用。

[实例初始化](https://www.programcreek.com/2011/10/java-class-instance-initializers/)