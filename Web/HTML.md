# HTML

## 概念

> HTML (**Hyper Text Markup Language** **超文本标记语言**)  是最基础的网页开发语言

+ **超文本**：超文本是用*超链接* 的方法，将各种不同的空间文字信息组织在一起的网状文本。
+ **标记语言**：由*标签* 构成的语言。<标签名称> 如 html，xml
  + 标记语言不是编程语言

## 快速入门

### 语法

1. html文档的**后缀名**是`.html` 或 `.htm` 两者含义完全相同
2. **标签**分类
   + 围堵标签：有开始标签和结束标签 `<html> </html>`
   + 自闭和标签：开始标签和结束标签在一起 `<br/>`
3. **标签可以嵌套**：需要正确嵌套，不可以你中有我，我中有你。
4. 再开始标签中可以定义属性。属性是由键值对构成，值需要用引号（单双引号都可以）
5. html的标签不区分大小写，但是建议使用小写

### 示例代码

```html
<html>
    <head>
        <title>这是Title</title>
    </head>

    <body>
        <font color="red">Hello World</font> <br/>
        <font color="green">Hello World</font>
    </body>
</html>
```

## HTML标签 -- 文件标签

> 构成html最基本的标签

+ `<html>` 

  html文档的根标签

+ `<head>`

  头标签。用于指定html文档的一些属性。引入外部的资源

+ `<title>`

  标题标签。

+ `<body>`

  体标签。

+ `<!DOCTYPE html>`

  定义文档类型

## HTML标签 -- 文本标签

> 和文本有关的标签

+ `<h1> to <h6>`：标题标签
  + 后面的数字代表不同等级的标签
  + 随着数字的增大，字体逐渐减小
+ `<p>`：段落标签，段落后自动加一个换行
+ `<br>`：换行标签
+ `<hr>`：水平线标签   *h5不支持*
  + color：颜色属性
  + width：宽度
  + size：粗细
  + align：对齐方式 left right center
+ `<b>`：字体加粗
+ `<i>`：斜体
+ `<font>`：字体标签  *h5已经不支持*
  + color：颜色
  + size：大小
  + face：字体样式
+ 属性定义：
  + color：
    + red  green  blue
    + rgb(val1, val2, val3) 值的取值范围0~255
    + #ffffff

## HTML标签 -- 图片标签

## HTML标签 -- 列表标签

## HTML标签 -- 链接标签

## HTML标签 -- 表格标签