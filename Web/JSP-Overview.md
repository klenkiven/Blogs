# 动态网页 -- JSP

## 一、JSP的组成

+ 静态html代码

  ```jsp
  <html>
  	<head>
          <title></title>
      </head>
      <body>
          <h1>        </h1>
          <table>
              <tr>
              	<td></td>
              </tr>
          </table>
      </body>
  </html>
  ```

  > JSP会使用out.write("")向客户端逐行输出静态网页内容。

+ 指令

  1. 配置指令`<%@page %>`

      ```jsp
      <%@page language="java" pageEncoding="UTF-8"%>
      <!-- 
      jsp网页页面：
        1. 必须是.jsp结尾的
        2. 必须包含有配置指令
      -->
      ```


  2. 标签库`<%@taglib %>`

     ```jsp
     <%@taglib profix="前缀" url="" tagdir=""%>
     <!--
     导入第三方标签库或者导入自定义标签库
     url和taglib：用于指定标签库的位置
         url：指定互联网中的第三方标签库，使用http://域名
         taglib：指定本地的标签库，aa/a.之定义标签库
     -->
     ```

+ jsp脚本

  ```jsp
  <%
  	//在这里可以编写java的逻辑代码，不可以在其中声明方法或者定义类
  	int a = 1;
  	int b = a + 1;
  	int c = a + b;
  
  	for(int i = 0; i < 100; i++) {
          //Code
      }
  %>
  ```

+ 表达式

  ```jsp
  <%= %>
  <!--
  在=后面写表达式
  在当前位置输出表达式的结果
  -->
  <span><%=a+1 %></span>
  ```

+ 声明

  ```jsp
  <%!
      public int sum() {
      	return 0;
  }
      %>
  ```

+ 动作

  ```jsp
  <jsp:动作名称 ></jsp:动作名称>
  <!--
  jsp:include：在本页面中引入其他jsp页面
  jsp:forward：实现请求的转发
  -->
  ```

+ 注释

  ```jsp
  <!-- 这是html的注释 -->
  <%-- 这是JSP中的多行注释 --%>
  <% //这是jsp脚本中的注释 %>
  ```

  > html注释在客户端浏览器是可以看到的
  >
  > jsp注释不会被客户端浏览器看到

## 二、JSP的运行原理

