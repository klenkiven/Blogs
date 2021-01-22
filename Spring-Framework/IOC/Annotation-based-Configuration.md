# 基于注解的容器配置

> 注解是不是优于XML的配置？
>
> 基于注释的配置的引入提出了这样一个问题:这种方法是否比XML“更好”。简短的回答是“视情况而定”。简单地说，每种方法都有其优点和缺点，通常是由开发者决定哪种策略更适合自己。由于它们的定义方式，注释在声明中提供了大量上下文，从而使配置更短、更简洁。然而，XML擅长于在不修改源代码或重新编译组件的情况下连接组件。一些开发人员更喜欢让连接靠近源代码，而另一些人则认为带注释的类不再是pojo，而且，配置变得分散，更难控制。
>
> 无论如何选择，Spring都可以同时适应两种风格，甚至可以将它们混合在一起。值得指出的是，通过其JavaConfig选项，Spring允许以一种非侵入性的方式使用注释，而无需触及目标组件源代码，而且就工具而言，Eclipse的Spring工具支持所有配置样式。

## 使用 ~~`@Required`~~

~~`@Required`~~ 已经在Spring Framework 5.1 被废弃掉了。

该注释指出受影响的bean属性必须在配置时填充，可以通过bean定义中的显式属性值填充，也可以通过自动装配填充。

```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Required
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...
}
```

## 使用 `@Autowired` 

> 其实这个注解可以放到基本上你可以想到的所有地方，包括一些集合也可以使用。

`@Autowired` 的作用范围：

+ 构造器
+ 传统的Setter方法
+ 任意名字的方法
+ 数组属性
+ 有类型标注的集合属性
+ 普通属性

### 构造器注入

```java
// 构造器注入
public class MovieRecommender {

    private final CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```

像上面这种例子，只有一个构造器，是不需要加 `@Autowired` 注解的。但是如果有很多个构造器这个注解就是必须得了。用来给IoC容器指定，哪一个构造器是被需要的。

### 传统的Setter方法

```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Autowired
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...
}
```

### 任意名字的方法

```java
public class MovieRecommender {

    private MovieCatalog movieCatalog;

    private CustomerPreferenceDao customerPreferenceDao;

    // 任意名字的方法
    @Autowired
    public void prepare(MovieCatalog movieCatalog,
            CustomerPreferenceDao customerPreferenceDao) {
        this.movieCatalog = movieCatalog;
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```

### 甚至在构造器和属性上面都可以同时使用

```java
public class MovieRecommender {

    private final CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    private MovieCatalog movieCatalog;

    @Autowired
    public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```

### 集合类型的属性

```java
// 数组类型的属性
class MovieRecommender {

    @Autowired
    private MovieCatalog[] movieCatalogs;

    // ...
}

// 指定类型的集合属性
class MovieRecommender {

    private Set<MovieCatalog> movieCatalogs;

    @Autowired
    public void setMovieCatalogs(Set<MovieCatalog> movieCatalogs) {
        this.movieCatalogs = movieCatalogs;
    }

    // ...
}
```

> Tips：
>
> 您的目标bean可以实现`org.springframework.core`。如果希望数组或列表中的项按特定顺序排序，可以使用`@Order`或标准`@Priority`注释。否则，它们的顺序遵循容器中相应目标bean定义的注册顺序。

#### Map类型的集合（Limited）

即使是类型化的映射实例也可以自动装配，**只要期望的键类型是String**。映射值包含**所有期望类型的bean**，键包含相应的bean名。

```java
public class MovieRecommender {

    private Map<String, MovieCatalog> movieCatalogs;

    @Autowired
    public void setMovieCatalogs(Map<String, MovieCatalog> movieCatalogs) {
        this.movieCatalogs = movieCatalogs;
    }

    // ...
}
```

## 使用 `@Primary` 微调基于注解的自动注入

如果候选对象中恰好存在一个主bean，则它将成为自动绑定值。

```java
// 关于 @Configuration 和 @Bean 在后面
@Configuration
public class MovieConfiguration {

    @Bean
    @Primary
    public MovieCatalog firstMovieCatalog() { ... }

    @Bean
    public MovieCatalog secondMovieCatalog() { ... }

    // ...
}
```

与之等价的XML配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean class="example.SimpleMovieCatalog" primary="true">
        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean id="movieRecommender" class="example.MovieRecommender"/>

</beans>
```

## 使用 Qualifiers 微调基于注解的自动注入

上面的 `@Primary` 是一种比较好的控制自动注入的方法。但是如果想要更加细节的配置，那么只有这样的注解肯定是不够的。

因此这里使用 `@Qualifier()` 注解来指定一个名字，然后可以缩小匹配的范围。

```java
// 在属性上使用
public class MovieRecommender {

    @Autowired
    @Qualifier("main")
    private MovieCatalog movieCatalog;

    // ...
}
// 在方法内的参数上使用
public class MovieRecommender {

    private MovieCatalog movieCatalog;

    private CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public void prepare(@Qualifier("main") MovieCatalog movieCatalog,
            CustomerPreferenceDao customerPreferenceDao) {
        this.movieCatalog = movieCatalog;
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```

与上面等价的XML的配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean class="example.SimpleMovieCatalog">
        <qualifier value="main"/>  <!-- 1 -->

        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <qualifier value="action"/> <!-- 2 -->

        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean id="movieRecommender" class="example.MovieRecommender"/>

</beans>
```

> 1   The bean with the `main` qualifier value is wired with the constructor argument that is qualified with the same value.
>
> 2  The bean with the `action` qualifier value is wired with the constructor argument that is qualified with the same value.































































