# AspectJ另一种AOP的方式

## 构建一个简单的AOP程序

类似地，创建一个目录结构

xyz.klenkiven.

+ advice
  + MyAdvice.java
+ pojo
  + MyInterface.java
  + Student.java
+ test
  + SpringAOPAspectJTest.java

----

MyAdvice.java

```java
package xyz.klenkiven.advice;


import org.aspectj.lang.ProceedingJoinPoint;

public class MyAdvice {
    // 前置通知
    public void before() {
        System.out.println("MyAdvice.before:我是前置通知");
    }
    // 后置通知
    public void after() {
        System.out.println("MyAdvice.after:我是后置通知");
    }
    // 环绕通知
    public Object myRound(ProceedingJoinPoint pp) throws Throwable {
        // 环绕前
        System.out.println("MyAdvice.myRound:我是环绕——前");
        // 放行
        Object proceed = pp.proceed();
        // 环绕后
        System.out.println("MyAdvice.myRound:我是环绕——后");

        return proceed;
    }
    // 异常通知
    public void myThrow(Exception e) {
        System.out.println("MyAdvice.myThrow:我是异常通知：" + e.getMessage());
    }
}
```

----

MyInterface.java

```java
package xyz.klenkiven.pojo;

public interface MyInterface {
    // 生命接口方法
    void testStudent();
}
```

----

Student.java

```java
package xyz.klenkiven.pojo;

public class Student implements MyInterface{
    // 功能方法
    @Override
    public void testStudent() {
        System.out.println("Student.testStudent:我是学生，SpringAOP的AspectJ");
    }
}
```

----

SpringAOPAspectJTest.java

```java
package xyz.klenkiven.test;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import xyz.klenkiven.pojo.MyInterface;

public class SpringAOPAspectJTest {
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("application-context.xml");
        MyInterface myInterface = (MyInterface) ac.getBean("stu");
        myInterface.testStudent();
    }
}
```

----

application-context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 学生的Bean -->
    <bean id="stu" class="xyz.klenkiven.pojo.Student" />

    <!-- 配置通知Bean -->
    <bean id="advice" class="xyz.klenkiven.advice.MyAdvice" />

    <!-- 配置AOP -->
    <aop:config>
        <aop:aspect ref="advice">
            <aop:pointcut id="pointcut" expression="execution(* testStudent())"/>
            <!-- 声明扩展方法 -->
            <aop:before method="before" pointcut-ref="pointcut" />
            <aop:after-returning method="after" pointcut-ref="pointcut" />
            <aop:around method="myRound" pointcut-ref="pointcut" />
            <aop:after-throwing method="myThrow" pointcut-ref="pointcut" throwing="e" />
        </aop:aspect>
    </aop:config>
</beans>
```

## 特点一：advice包里面只有一个advice类MyAdvice

对比Spring AOP这个只是使用了一个advice的类——MyAdvice。通过里面方法的定义，以及配置文件的配置达到对切面的配置。

这个配置也很有特点！

使用了一个新的配置标签 `<aop:aspect ref="">` 这个`ref`指的就是Advice这个类，方便下面的配置。同时在 `<aop:aspect>` 标签下面也有很多标签可以使用。

|         标签名          |                             作用                             |
| :---------------------: | :----------------------------------------------------------: |
|     `<aop:before>`      |                    用于声明前置通知的方法                    |
|      `<aop:after>`      | 用于声明后置通知的方法。其实这个意思是，不管方法本身有没有抛出异常，都会执行这个后置通知 |
| `<aop:after-returning>` | 用于声明后置通知的方法。这个后置通知有返回值的，建议声明方法的时候加上Object返回值。如果方法本身抛出异常了，这个后置的通知就不会执行 |
|     `<aop:around>`      | 用于声明环绕通知。这个声明的时候要注意方法应该有ProceedingJoinPoint类型的形参。 |
| `<aop:after-throwing>`  | 用于声明异常通知。这个Exception e可以声明参数的，但是这个参数是需要绑定的。 |

## 特点二：遇到有参数的方法，处理起来比较麻烦

假如说，这个`testStudent(String name, int age)` 方法有参数了。

首先，before这个方法得改一改，并且做一下数据绑定：

+ `before(String name, int age)`

+ 数据绑定

  ```xml
  <aop:aspect ref="advice">
      <aop:pointcut id="pointcut" expression="execution(* xyz.klenkiven.pojo.Student.testStudent(String, int)) and args(name, age)"/>
      <!-- 声明扩展方法 -->
      <aop:before method="before" pointcut-ref="pointcut" />
      <aop:after-returning method="after" pointcut-ref="pointcut" arg-names="name,age" />
      <aop:around method="myRound" pointcut-ref="pointcut" />
      <aop:after-throwing method="myThrow" pointcut-ref="pointcut" throwing="e" />
  </aop:aspect>
  ```

> 可见AspectJ在一些细节上的处理很繁琐