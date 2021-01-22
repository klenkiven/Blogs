# 什么是TCP/IP？

> 提供了一个参考的层次协议OSI 一共是7层结构。但是TCP/IP协议，是有四层来实现的。

+ 应用层

  > http、ftp、ssh等等很多协议

  **手动连接百度，并且获得主页的代码**

  ```shell
  exec 9<> /dev/tcp/www.baidu.com/80
  ```

  + 9：文件描述符
  + <> : 输入和输出重定向
  + /dev/tcp/www.baidu.com/80 ：这是一个文件

  ```shell
  echo -e "GET / HTTP/1.0\n" 1 >& 9
  cat 0<&9
  ```

  输出结果（部分）

  ```
  HTTP/1.0 200 OK
  Accept-Ranges: bytes
  Cache-Control: no-cache
  Content-Length: 14615
  Content-Type: text/html
  Date: Fri, 22 Jan 2021 05:13:10 GMT
  P3p: CP=" OTI DSP COR IVA OUR IND COM "
  P3p: CP=" OTI DSP COR IVA OUR IND COM "
  Pragma: no-cache
  Server: BWS/1.1
  Set-Cookie: BAIDUID=1F334B21B79B3A6AC2DE81B193B37CD5:FG=1; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com
  Set-Cookie: BIDUPSID=1F334B21B79B3A6AC2DE81B193B37CD5; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com
  Set-Cookie: PSTM=1611292390; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com
  Set-Cookie: BAIDUID=1F334B21B79B3A6A57640E983D77DA73:FG=1; max-age=31536000; expires=Sat, 22-Jan-22 05:13:10 GMT; domain=.baidu.com; path=/; version=1; comment=bd
  Traceid: 1611292390363090151410939105567534440705
  Vary: Accept-Encoding
  X-Ua-Compatible: IE=Edge,chrome=1
  
  <!DOCTYPE html><!--STATUS OK-->
  <html>
  <head>
          <meta http-equiv="content-type" content="text/html;charset=utf-8">
          <meta http-equiv="X-UA-Compatible" content="IE=Edge">
          <link rel="dns-prefetch" href="//s1.bdstatic.com"/>
          <link rel="dns-prefetch" href="//t1.baidu.com"/>
          <link rel="dns-prefetch" href="//t2.baidu.com"/>
          <link rel="dns-prefetch" href="//t3.baidu.com"/>
          <link rel="dns-prefetch" href="//t10.baidu.com"/>
          <link rel="dns-prefetch" href="//t11.baidu.com"/>
          <link rel="dns-prefetch" href="//t12.baidu.com"/>
          <link rel="dns-prefetch" href="//b1.bdstatic.com"/>
          <title>百度一下，你就知道</title>
  ...................................................
  ```

  

+ 内核（Kernel）

  + 传输控制层
  + 网络层
  + 链路层

+ 物理层

## Client -- Server

> 上面的手动连接的案例

1. 建立一个连接
2. 应用某种协议

> 连接建立到数据之间，时间间隔的时间太长就会报错
>
> 常识：连接不会断开，一般会有服务器超时

## 什么叫做连接？

> 应用层就是应用软件，但是在应用层连接的时候会调用内核获取连接，那么什么是连接，怎么获得连接？

这里就不得不提到传出控制层了，传输控制层有两个协议一个是TCP协议另一个是UDP协议。

TCP：英文名（Transmission Control Protocol）它是**面向连接**的、**可靠**的、**基于字节流**的传输层通信协议。

## TCP：三次握手、四次挥手

### 三次握手

![image-20210122132632590](D:\KlenKiven\Blogs\img\What-Is-TCP-IP-01.png)

在通信输出的时候，是双向的，有输出有输入。

+ 第一次握手：客户端输出一个同步的请求，等待服务端的输入一个确认
+ 第二次握手：服务端返回一个同步的请求和一个确认的请求，等待客户端回应一个确认
+ 第三次握手：客户端返回一个确认的请求，可靠的链接建立！

在三次握手完成以后，客户端和服务端才会分配对应的系统资源，用于这个连接使用。连接可以理解是，双方互相开辟资源空间用于通信的过程。

> 扩展：通信建立了连接，但是很长时间并没有互相发送数据包，那么我想现在发送数据包，这个连接还是有效的吗？
>
> 认为连接是有效的。有可能在连接时间范围内，因为各种非人为的原因导致设备以及通信的物理线路失效，那个这个连接是不通信的。
>
> 因此，需要导入一个概念，叫做心跳包。
>
> 为了保证连接是有效的，一定时间之内需要发送心跳包，保证互相的连接是有效的。

### 四次挥手

1. 为什么要挥手？

   ```shell
   netstat -natp
   ```

   