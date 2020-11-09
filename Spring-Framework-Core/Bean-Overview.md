# Spring Bean 概述

> Spring IoC容器管理一个或多个bean。这些bean是使用您提供给容器的配置元数据创建的。
>
> 联系后面看过的，按照我的理解，bean并不可以理解成一个对象。但是一个单例的bean就可以理解bean就是一个对象。对于原型的bean就不可以理解成一个对象。这个部分可以在Bean Scope了解这两个东西。

Table.1 Bean defination

| Property                 |                        Explained in…                         |
| :----------------------- | :----------------------------------------------------------: |
| Class                    | [Instantiating Beans](https://docs.spring.io/spring-framework/docs/5.3.0/reference/html/core.html#beans-factory-class) |
| Name                     | [Naming Beans](https://docs.spring.io/spring-framework/docs/5.3.0/reference/html/core.html#beans-beanname) |
| Scope                    | [Bean Scopes](https://docs.spring.io/spring-framework/docs/5.3.0/reference/html/core.html#beans-factory-scopes) |
| Constructor arguments    | [Dependency Injection](https://docs.spring.io/spring-framework/docs/5.3.0/reference/html/core.html#beans-factory-collaborators) |
| Properties               | [Dependency Injection](https://docs.spring.io/spring-framework/docs/5.3.0/reference/html/core.html#beans-factory-collaborators) |
| Autowiring mode          | [Autowiring Collaborators](https://docs.spring.io/spring-framework/docs/5.3.0/reference/html/core.html#beans-factory-autowire) |
| Lazy initialization mode | [Lazy-initialized Beans](https://docs.spring.io/spring-framework/docs/5.3.0/reference/html/core.html#beans-factory-lazy-init) |
| Initialization method    | [Initialization Callbacks](https://docs.spring.io/spring-framework/docs/5.3.0/reference/html/core.html#beans-factory-lifecycle-initializingbean) |
| Destruction method       | [Destruction Callbacks](https://docs.spring.io/spring-framework/docs/5.3.0/reference/html/core.html#beans-factory-lifecycle-disposablebean) |

除了包含关于如何创建特定bean的信息的bean定义之外，`ApplicationContext`实现还允许注册在容器外部(由用户)创建的现有对象。

## 命名Bean

每个bean都有一个或多个标识符。这些标识符**在宿主bean的容器中必须是唯一的。**bean通常只有一个标识符。但是，如果它需要多个别名，那么额外的别名可以被视为别名。

> 注意：bean id唯一性仍然由容器强制执行，但XML解析器不再强制执行。

在基于xml的配置元数据中，可以使用`id`属性、`name`属性或两者来指定bean标识符。

+ id属性允许您指定一个id。按照惯例，这些名称是字母数字('myBean'， 'someService'等)，但它们也可以包含特殊字符。
+ 如果希望为bean引入其他别名，还可以在name属性中指定它们，用逗号(，)、分号(;)或空格分隔。

> Bean Naming Conventions（Bean的命名规范）
>
> The convention is to use the standard Java convention for instance field names when naming beans. That is, bean names start with a lowercase letter and are **camel-cased** from there. Examples of such names include `accountManager`, `accountService`, `userDao`, `loginController`, and so forth.
>
> Naming beans consistently makes your configuration easier to read and understand. Also, if you use Spring AOP, it helps a lot when applying advice to a set of beans related by name.
>
> > 上面就当阅读材料了

### 在Bean定义之外起别名

直接上代码，作用一目了然。

```xml
<!-- Defination -->
<alias name="fromName" alias="toName"/>

<!-- Usage -->
<alias name="myApp-dataSource" alias="subsystemA-dataSource"/>
<alias name="myApp-dataSource" alias="subsystemB-dataSource"/>
```

## 实例化Bean

bean定义本质上是创建一个或多个对象的（recipe）菜谱。当被请求时，容器查看已命名bean的配方，并使用该bean定义封装的配置元数据来创建(或获取)实际对象。

大致有两种实例化Bean的方法：

+ 利用反射调用其构造器
+ 使用静态工厂方法

> 内部类的名字
>
> 如果希望为静态嵌套类配置bean定义，则必须使用嵌套类的二进制名称。
>
> For example, if you have a class called `SomeThing` in the `com.example` package, and this `SomeThing` class has a `static` nested class called `OtherThing`, the value of the `class` attribute on a bean definition would be `com.example.SomeThing$OtherThing`.
>
> 请注意名称中使用了 `$` 字符来分隔嵌套的类名和外部类名。

### 使用构造方法实例化

> 这个原理是通过反射实例化对象的
>
> 在可以设置参数什么的（后面会讲到）。有Constructor-based 注入方法和Setter-based注入方法。

### 使用静态工厂方法实例化

> 这个可以参考一下那个设计模式的单例模式

### 使用一个工厂实例使用其中的工厂方法（非静态）实例化

> 这个和上面的原理其实也差不太多笔者个人觉得

## 废话少说，直接上例子

**目录结构**

src\

+ main
  + java
    + example
      + AccountService.java
      + ClientService.java
      + ClientServiceImpl.java
      + DefaultServiceLocator.java
  + resource
    + bean-config.xml
+ test
  + java
    + example
      + test.java
  + resource
    + bean-config.xml

pom.xml



**文件内容**

+ ClientService.java

  ```java
  package example;
  
  public class ClientService {
  
      private String message = "I'm ClientServer Instance";
      private static ClientService clientService = new ClientService();
  
      public ClientService() {
      }
  
      public static ClientService createClientServer() {
          return clientService;
      }
  
      public void showInfo() {
          System.out.println(message);
      }
  }
  ```

+ ClientServiceImpl.java

  ```java
  package example;
  
  public class ClientServiceImpl extends ClientService {
  
      private String message = "I'm ClientServiceImpl instance";
  }
  ```

+ AccountService.java

  ```java
  package example;
  
  public class ClientServiceImpl extends ClientService {
  
      private String message = "I'm ClientServiceImpl instance";
  }
  ```

+ bean-config.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
  
      <!-- 使用构造方法实例化 Bean -->
      <bean id="clientServer" class="example.ClientService" />
      <bean name="anotherClientServer" class="example.ClientService" />
  
      <!-- 使用静态工厂方法实例化 Bean -->
      <bean id="clientServiceByFactoryMthod"
            class="example.ClientService"
            factory-method="createClientServer"
      />
  
      <!-- 使用一个实例的工厂方法实例化 Bean -->
      <bean id="serviceLocator" class="example.DefaultServiceLocator" />
      <bean id="clientServiceByInstanceFactoryMethod"
            factory-bean="serviceLocator"
            factory-method="createClientServiceInstance"
      />
      <bean id="accountServiceByInstanceFactoryMethod"
            factory-bean="serviceLocator"
            factory-method="createAccountServiceInstance"
      />
  
  </beans>
  ```

+ test.java

  ```java
  package example;
  
  import org.junit.Test;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.support.ClassPathXmlApplicationContext;
  
  public class test {
  
      /**
       * 使用构造器实例化对象
       */
      @Test
      public void constructorTest() {
          // 创建配置Beans 创建Bean容器
          ApplicationContext context = new ClassPathXmlApplicationContext("bean-config.xml");
  
          // 检索配置实例
          ClientService clientServer = context.getBean("clientServer", ClientService.class);
  
          // 使用实例
          clientServer.showInfo();
          System.out.println(clientServer);
      }
  
      /**
       * 使用静态方法工厂实例化对象
       */
      @Test
      public void factoryMethodTest() {
          ApplicationContext context = new ClassPathXmlApplicationContext("bean-config.xml");
          ClientService clientServer = (ClientService) context.getBean("clientServiceByFactoryMthod");
          clientServer.showInfo();
          System.out.println(clientServer);
      }
  
      /**
       * 使用一个工厂实例中的静态工厂方法实例化对象
       */
      @Test
      public void instanceFactoryMethod() {
          ApplicationContext context = new ClassPathXmlApplicationContext("bean-config.xml");
          ClientService clientService = (ClientService) context.getBean("clientServiceByInstanceFactoryMethod");
          AccountService accountService = (AccountService) context.getBean("accountServiceByInstanceFactoryMethod");
  
          clientService.showInfo();
          System.out.println(clientService);
          accountService.showInfo();
          System.out.println(accountService);
      }
  
  }
  ```

+ pom.xml

  ```xml
  <!-- 这个会分享到安装包里面 -->
  ```

  

























































