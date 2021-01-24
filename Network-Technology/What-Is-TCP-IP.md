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

   ```
   激活Internet连接 (服务器和已建立连接的)
   Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
   tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      603/systemd-resolve
   tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      765/sshd: /usr/sbin
   tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      655/cupsd
   tcp        0      0 127.0.0.1:6010          0.0.0.0:*               LISTEN      2424/sshd: klenkive
   tcp        0      0 192.168.1.103:22        192.168.1.107:51165     ESTABLISHED 2335/sshd: klenkive
   tcp        0     48 192.168.1.103:22        192.168.1.107:51164     ESTABLISHED 2333/sshd: klenkive
   tcp        0      0 192.168.1.103:53160     101.6.8.193:80          TIME_WAIT   -
   tcp6       0      0 :::22                   :::*                    LISTEN      765/sshd: /usr/sbin
   tcp6       0      0 ::1:631                 :::*                    LISTEN      655/cupsd
   tcp6       0      0 ::1:6010                :::*                    LISTEN      2424/sshd: klenkive
   ```
   
   > LISTEN这个状态只有服务器才有这个状态
   
2. 端口号有多少？

   一共有65535个。0 ~ 65535 就是端口号码的编号

3. 为什么要使用端口号？

   IP   通过IP找到主机

   端口   通过端口找到主机上面的软件服务

   > 进程的端口号时不固定的，因此由不同的进程竞争不同的端口号。那么反过来可以意识到，一个端口号对应一个进程。

   假如说有三个客户端要连接ip号主机的tomcat服务，那么服务端会消耗一个端口号，例如8080。但是每个客户端需要消耗一个，选择是随机的。

4. 套接字`socket`:

   ip + port  ip+port  每一个套接字是独立的，是隔离的。

![image-20210122194435705](D:\KlenKiven\Blogs\img\What-Is-TCP-IP-2.png)

