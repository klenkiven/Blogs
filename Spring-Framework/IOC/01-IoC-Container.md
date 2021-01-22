# Spring IoC容器

> 本章介绍了控制反转(IoC)原理的Spring框架实现。IoC也称为依赖注入(DI)。

## 依赖注入和控制反转

它是一个过程，对象仅通过**构造方法参数**、**工厂方法的参数**或在对象实例被构造或从工厂方法返回后在其上设置的**属性**来定义它们的依赖项(即它们使用的其他对象)。然后，容器在**创建bean时**注入这些依赖项。这个过程基本上是bean本身的反向(因此得名，控制反转)，它通过直接构造类或类似**服务定位器模式的机制**来控制其依赖项的实例化或位置。

> 服务器定位器模式：这是一种设计模式
>
> ​		参考链接：https://www.runoob.com/design-pattern/service-locator-pattern.html

## ApplicationContext接口

`org.springframework.context.ApplicationContext`接口表示Spring IoC容器，并负责实例化、配置和组装bean。容器通过读取配置元数据获得关于要实例化、配置和组装哪些对象的指令。配置元数据用**XML**、**Java注释**或**Java代码**表示。它允许您表达组成应用程序的对象以及这些对象之间丰富的相互依赖关系。

常见的两个ApplicationContext的实现：

+ `ClassPathXmlApplicationContext`
+ `FileSystemXmlApplicationContext`

![image-20201106211312997](D:\KlenKiven\Blogs\img\IoC-Container-Illustration.png)

## 配置元数据

+ 基于Java注释
+ 基于Java代码
+ 基于XML的配置文件

这里举一个XML的例子：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">  <!-- 1 2 -->
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->

</beans>
```

1. `id` 属性是标识单个bean定义的字符串。
2. `class` 属性就是定义bean的类型，使用完全限定的类名

## 实例化一个容器

```java
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```

这里参数使用 Resouces 目录里面的文件。这里不细说了，完了后面细说，现在记住就行。

## 使用容器

```java
// create and configure beans
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

// retrieve configured instance
PetStoreService service = context.getBean("petStore", PetStoreService.class);

// use configured instance
List<String> userList = service.getUsernameList();
```

差不多就是，创建一个容器实例，然后利用容器获得一个对象，然后使用对象。这里可以注意到，根本不用手动去 new 一个对象，只要 getBean() 就行了。