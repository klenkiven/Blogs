# JavaScript

## 概述

### 概念：一门客户端脚本语言

+ 运行在客户端浏览器中的。每一个浏览器都有JavaScript的解析引擎
+ 脚本语言：不需要编译，直接就可以被浏览器解析执行了

### 功能

+ 可以来增强用户的html页面的交互过程，可以控制html元素，让页面有一些动态效果，增强用户的体验

### JavaScript = ECMAScript + BOM + DOM

## ECMAScript：客户端脚本语言的标准

### 基本语法

#### 与html结合方式

+ 内部JS
  + 定义`<script>`标签，标签体内容就是js代码
+ 外部JS
  + 定义`<script>`标签，通过src属性引入外部的JS文件
+ 注意：
  + 可以写在HTML的任意位置
  + 放置的顺序会影响JS的执行顺序
  + `<script>`标签可以定义多个

#### 注释

+ 单行注释：//注释内容
+ 多行注释：/* 多行注释 */

#### 数据类型

+ 原始数据类型（基本数据类型）：
  + **number**：整数 / 小数 / NaN
  + **string**：字符串类型
  + **boolean**：布尔类型
  + **null**：一个对象为空的占位符
  + **undefined**：未定义。如果一个变量没有给初始化值，则会被认为是undefined
+ 引用数据类型：对象

#### 变量

+ 概念：一块存储数据的内存空间
+ Java语言是强类型的语言，而JavaScript是弱类型语言
+ 语法：var 变量名 =  初始化值;  //初始化值可以是任意值
+ typeof运算符：要检查的变量是什么样的数据类型
  + 注意：null原酸后得到的是object

#### 运算符

+ 一元运算符：只有一个运算数的运算符
  + ++，--，+(正号), -(负号)
  + 在JS中，如果运算数不是运算符所要求的类型，那么JS引擎会自动的讲运算符进行类型转换
    + 其他类型转number
      + string转number：按照字面之转换，如果字面值不是数字，则转换成NaN
+ 算术运算符：
  + +，-，*，/，% ...
+ 赋值运算符：
  + =，+=，-= ...
+ 比较运算符：
  + <, >, ==, ===(全等于), <=, >=
+ 逻辑运算符：
  + &&, ||, ! 
+ 三元运算符：
  + ?  : 
+ 特殊语法：
  + 语句以分号结尾，如果一行只有一条语句，则分号可以省略
  + 声明变量，不用加var也可以
    + 加var定义的是局部变量
    + 不加var定义的是全局变量（不建议使用）

#### 流程控制语句

+ if ... else
+ switch:
  + 在Java中switch可以接受的数据类型：byte, int, shor, char, 枚举, 字符串
  + 在JS中可以接受任何值
+ while
+ do ... while
+ for

#### 练习：99乘法表

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>九九乘法表</title>
    <style>
        td {
            border: 3px solid black;
        }
    </style>
    <script>
        document.write("<table align='center'>");
        for (var j = 1; j <= 9; j++){
            document.write("<tr>")
            for (var k = 1; k <= j; k++) {
                //输出
                document.write("<td>"+j+"*"+k+"="+j*k+"</td>");
            }
            document.write("</tr>")
        }
        document.write("</table>");
    </script>
</head>
<body>

</body>
</html>
```

### 基本的对象

+ Function对象：描述一个函数对象

  + **创建**：

    + 方式一

      var fun = new Function(形参列表, 方法体);

    + 方式二

      function 方法名称(形参列表) { 方法体 }

    + 方式三

      var 方法名 = function(形参列表) { 方法体 }

  + **方法**：

  + **属性**：

    + length：形参的个数

  + **特点**： 

    + 方法定义时，形参的类型不用写，返回值类型也可不买
    + 方法是一个对象，如果定义名称相同的方法，会覆盖
    + 在JS中，方法的调用只与方法的名称有关和参数列表无关
    + 在方法声明中，有一个隐藏的内置对象（数组），arguments，封装所有的实际参数

  + **调用**：

    + 方法名称(实际参数列表)

+ Array对象：数组对象

  + 创建：
    + var arr = new Array(元素列表);
    + var arr = new Array(默认长度);
    + var arr = [元素列表];
  + 方法
  + 属性
  + 特点
    1. JS中数组的元素的类型是可变的

+ Date

+ 

## BOM

## DOM

