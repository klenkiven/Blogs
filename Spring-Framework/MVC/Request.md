# RequestMapping以及搭配其使用的注解

> RequestMapping 可以用来设定请求的地址。这个注解可以使用在类上面，可以使用在方法上面。不同的使用位置有不同的效果。

```java
package xyz.klenkiven.springdemo.controller;

// 假设Tomcat 对这个应用的地址为 http://url:port/webapp
import org.springframework.web.annotation.RequestMapping;
import org.springframework.stereotype.Controller;

@RequestMapping("/test")
@Controller
public class SpringMVCTest {
    
    private static final String SUCCESS = "success";
    
    @RequestMapping("/hello")
    public String hello() {
        System.out.println("测试请求");
        return SUCCESS;
    }
    
    @RequestMapping(value="method", method=RequestMethod.POST)
    public String method() {
        System.out.println("测试请求方法");
        return SUCCESS;
    }
    
    @RequestMapping(value="param", params={"username", "age!=10"}, headers={"Accept-Language=zh-CN,zh;q=0.8"})
    public String parameterAndHeaders() {
        System.out.println("测试params参数和headers参数");
        return SUCCESS;
    }
    
    @RequestMapping("/delete/{id}")
    public String delete(@PathVariable Integer id) {
        UserDao.deleteById(id);
        return "redirect:/user/list.action"
    }
    
    @RequestMapping(value="/order/{id}", method=RequestMethod.DELETE)
    public String deleteOrder(@PathVariable Integer id) {
        return SUCCESS + ": " + id + " is deleted";
    }
    
    @RequestMapping("/request/param")
    public String param(@RequestParam String username, @RequestParam Integer age) {
        return SUCCESS + " testRequestParam: " + username + ", " + age;
    }
}
```

## RequestMapping 修饰Controller类及其方法

1. 类定义处：提供初步的请求映射信息，相对于WEB应用的根目录。如果类没有使用这个注解，默认的映射位置就是WEB应用根目录的映射位置。

   > 在上面的示例代码中，在类上面的RequestMapping就代表，这个类下面的方法映射的根位置。
   >
   > 例如，hello这个请求，请求地址应该是`http://url:port/webapp/test/hello` 

2. 方法处：提供进一步的映射信息。一般而言，这个映射信息直接就可以访问对应的服务。是相对于类定义处的映射位置。

   > 在上面的方法中，直接访问 `/test/hello` 请求就可以直接返回结果。

## RequestMapping请求参数，请求方法和请求头

1. method属性可以定义请求的方法。

   > HTTP的请求方式有很多，例如，GET, POST, PUT, DELETE等等。在传统的WEB应用开发中，用的最多的就是GET和POST这两个请求方式。
   >
   > 但是，RESTful这种API风格下面这些不同的方法有了不同的含义，这也就是后话了。

2. params和header**支持简单的表达式**（使用频率比较少）

   > 上面的例子中，规定：param里面要求必须有username属性和age属性，并且age属性不允许等于10
   >
   > 同理，header也是这样要求的

## RequestMapping映射请求

RequestMapping路径请求支持Ant风格的请求。

## PathVariable映射URL绑定的占位符

通过 `@PathVariable` 可以讲URL中占位符参数绑定到控制器处理方法的入参中。

1. `@PathVariable("参数名")` 例如上面的例子就是 id 那么参数名就可以写id。
2. 直接在参数里面加注解，默认的就是和上面Mappin相对应的参数。

## HiddenHttpMethodFilter过滤器

> 浏览器form表单只是支持GET与POST请求，而DELETE, PUT等method并不支持，Spring 3.0添加了一个过滤器，可以讲这些请求转换为标准的http方法，使得支持GET, POST, PUT与DELETE请求。

这个过滤器的原理是，通过获得表单一个名为 `_method`  的属性值，从而判断是什么类型的请求。

对应的表单代码应该如下所示：

```html
<form action="test/order/1" method="post">
    <input type="hidden" name="_method" value="delete" />
    <input type="submit" value="TestRESTful DELETE" />
</form>
```

## RESTful 风格 API 样例

新增：`POST /order`

修改：`PUT  /order/1`

获取：`GET  /order/1`

删除：`DELETE /order/1`

## `@RequestParam` 注解获得请求参数

上面例子所示，就是`@RequestParam`的用法，其实看起来和 `@PathVariable` 注解很相似。也是放在形参的前面，但是这个获取属性的方式是通过GET或者POST请求获得的。

> 上面的例子请求地址例如是，
>
> `http://url:port/webapp/test/request/param?username=klenkiven&age=11` 
>
> 那么返回结果就是
>
> `UCCESS testRequestParam: klenkiven, 11` 

+ value：从请求获取的默认参数
+ required：是否是需要的，如果不需要设置为false。默认是true
+ defaultValue：请求参数的默认值

## `@RequestHeader` 注解绑定请求报头的属性

和上面的请求参数很类似，这里就不加赘述了。

```java
@RequestHeander("请求头名") String headerName
```

## 使用POJO作为参数

Spring MVC 会按照请求参数名和POJO属性名，进行自动匹配，自动为该对象填充属性值。支持**级联属性** 。

> 例如，
>
> User [ username, password, age, address ]
>
> Address [ province, city ]
>
> 在配置表单的时候就可以使用级联属性：address.province, address.city

## 使用Servlet API作为参数

这个直接使用原生的类型就好了，MVC可以自动注入。（QAQ咋这么神奇呢

MVC支持的Handler方法可以接受的参数：

+ HttpServletRequest
+ HttpServletResponse
+ HttpSession
+ java.security.Principle
+ Locale
+ InputStream和OutputStream
+ Reader和Writer

> 直接使用的话就很简单方便了
>
> public String handle(HttpServletRequest request, HttpServletResponse response, HttpSession session)