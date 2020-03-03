# Java笔记---IO流

+ I/O是输入输出的缩写，I/O技术用于**处理设备之间的数据传输**，如读写文件，网络通讯等等
+ Java程序中，对于数据的输入输出操作以**”流(Strem)“**的方式进行
+ java.io包下面提供了各种”流“类和接口，用以获取不同种类的数据，并通过**标准的方法**输入成输出数据

## 流的分类

+ 根据数据的流向分为**输入流**，**输出流**。
+ 根据操作数据单位分为：**字节流(8 bit)**, **字符流**。
+ 根据流的角色不同分为：**节点流**，**处理流**。

## IO流体系

|      分类      |      字节输入流      |       字节输出流       |     字符输入流      |      字符输出流      |
| :------------: | :------------------: | :--------------------: | :-----------------: | :------------------: |
|  **抽象基类**  |     *InputStrem*     |     *OutputStream*     |      *Reader*       |       *Writer*       |
|  **访问文件**  |   *FileInputStrem*   |   *FIleOutputStream*   |    *FileReader*     |     *FileWriter*     |
|  **访问数组**  | ByteArrayInputStrem  | ByteArrayOutputStream  |   CharArrayReader   |   CharArrayWriter    |
|  **访问管道**  |   PipedInputStrem    |   PipedOutputStream    |     PipedReader     |     PipedWriter      |
| **访问字符串** |                      |                        |    StringReader     |     StringWriter     |
|   **缓冲流**   | *BufferedInputStrem* | *BufferedOutputStream* |  *BufferedReader*   |   *BufferedWriter*   |
|   **转换流**   |                      |                        | *InputStreamReader* | *OutputStreamWriter* |
|   **对象流**   |  *ObjectInputStrem*  |  *ObjectOutputStream*  |                     |                      |
|                |   FliterInputStrem   |   FliterOutputStream   |    FliterReader     |     FliterWriter     |
|   **打印流**   |                      |      PrintStream       |                     |     PrintWriter      |
| **推回输入流** |  PushbackInputStrem  |                        |   PushbackReader    |                      |
|   **特殊流**   |    DataInputStrem    |    DataOutputStream    |                     |                      |

访问文件的是一种**节点流**。缓冲流的就是**处理流**。斜体的就是需要注意的。

## FileReader读入数据的基本操作

```java
//将hello.txt文件中的内容读入到程序中，并输出到控制台
public void testFileReader() {
        //1. 实例化File类的对象
        File file = new File("IOTest/hello.txt");
        //2.提供具体的流
        FileReader fr = null;
        try {
            fr = new FileReader(file);
            //3.数据的读入
            //read()返回读入的一个字符，如果到达文件末尾则返回 -1
            //方式一：
//        int data = fr.read();
//        while(data != -1){
//            System.out.print((char) data);
//            //这里可以有读取下一个字符的作用
//            data = fr.read();
//        }
            //方式二：结构上的优化
            int data;
            while ((data = fr.read()) != -1)
                System.out.println((char) data);
            
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }catch (IOException e) {
            e.printStackTrace();
        }finally {
            //4. 手动关闭流的操作，很重要，对于这种一定要执行的操作使用finally保证他一定可以执行
            try {
                //如果FileReader并未实例化造成了空指针的异常
                if (fr != null)
                    fr.close();
	       } catch (IOException e) {
                e.printStackTrace();
        }
    }
}
```

**说明：**

1. **read()的理解**：返回读入的一个字符，如果达到文件末尾，返回-1。
2. **异常的处理**：为了保证流资源一定可以关闭，使用try - catch - finally处理
3. 读入文件一定要存在，否则会抛出**FileNotFountException**。

### 对read()操作升级，使用read的重载方法

```java
public void testFileReader1() {
    //1.File类的实例化
    File file = new File("IOTest/hello.txt");
    FileReader  fr = null;

    try {

        //2.FileReader类的实例化
        fr = new FileReader(file);

        //3.读入操作
        //read(char[] cbuf): 返回每次读入cbuf数组中的字符个数，如果达到文件末尾，返回-1
        char[] cbuf = new char[5];
        int len;
        while((len =fr.read(cbuf)) != -1) {

            //实测这是一种错误写法，可见read()方法在读取的时候，是覆盖而不是重写
            //for(char temp: cbuf)
            //    System.out.print(temp);
            //方式一：
            //for (int i = 0; i < len; i++) {
            //    System.out.print(cbuf[i]);
            //}
            //方式二：offset是string的开始字符
            String str = new String(cbuf, 0, len);
            System.out.print(str);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        //4.资源的关闭
        try {

            //防止空指针的异常
            if (fr != null)
                fr.close();

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## FileWriter数据写出的基本操作

> 从内存中写出数据到硬盘的文件里

```java
public void testFileWriter() throws IOException {
    //1.提供File类的对象，指明写出到文件
    File file = new File("IOTest/hi.txt");

    //2.提供FileWriter的对象，用于数据地写出
    //可以调用另一个构造器，即append是否为true
    FileWriter fw = new FileWriter(file, true);

    //3.写出的操作
    fw.write("I have a dream");
    fw.write("you need to have a dream!");

    //4.流资源的关闭
    fw.close();
}
```

**说明：**

1. 输出操作，对应的文件如果不存在，并不会报错
2. File对应的硬盘中的文件如果不存在，在输出过程中，会自动创建这个文件。
3. File对应的硬盘中的文件如果存在，在输出过程中，会将原有的文件覆盖。
4. 如果使用的构造器是：**File(File file, boolean append)** append参数为true时，则不会对原来文件覆盖，而是在，原有文件上追加。

## 文件的拷贝工作，利用FileWirter和FileReader

```java
public void fileReaderWriter() {
    FileReader fr = null;
    FileWriter fw = null;

    try {
        //1. 创建File类的对象，指明读入和写出的文件
        File srcFile = new File("IOTest/hello.txt");
        File destFile = new File("IOTest/hi.txt");

        //2. 创建流的对象
        fr = new FileReader(srcFile);
        fw = new FileWriter(destFile);

        //3. 数据的读入和写出操作
        char[] cbuf = new char[5];
        int len; //记录每次读入到cbuf中的个数
        while((len = fr.read(cbuf)) != -1){
            fw.write(cbuf, 0, len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        //4. 关闭流资源，倒着看然后找到那个流，选择关闭顺序
        //方式一：
        //try {
        //    fw.close();
        //} catch (IOException e) {
        //    e.printStackTrace();
        //} finally {
        //    try {
        //        fr.close();
        //    } catch (IOException e) {
        //        e.printStackTrace();
        //    }
        //}

        //方式二：
        try {
            if (fw != null)
                fw.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            if (fr != null)
                fr.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## FileInputStream和FileOutputStream复制非文本文件

> 非文本文件的复制

```java
public void fileIOStream() {
    FileInputStream fi = null;
    FileOutputStream fo = null;

    try {
        //1. 创建File类的对象，指明读入和写出的文件
        File srcFile = new File("IOTest/pic1.png");
        File destFile = new File("IOTest/pic2.png");

        //2. 创建流的对象
        fi = new FileInputStream(srcFile);
        fo = new FileOutputStream(destFile);

        //3. 数据的读入和写出操作
        byte[] bbuf = new byte[5];
        int len; //记录每次读入到bbuf中的个数
        while((len = fi.read(bbuf)) != -1){
            fo.write(bbuf, 0, len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        //4. 关闭流资源，倒着看然后找到那个流，选择关闭顺序
        try {
            if (fo != null)
                fo.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            if (fi != null)
                fi.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 实例：指定文件下文件的复制操作

```java
public void testCopyFile() {
    long start = System.currentTimeMillis();

    String srcPath = "IOTest/test.exe";
    String destPath = "IOTest/dest.exe";
    copyFile(srcPath, destPath);

    long end = System.currentTimeMillis();

    System.out.println("所用时间为："+(start - end)+"毫秒");
}

//指定路径下文件的复制操作
public void copyFile(String srcPath, String destPath) {
    FileInputStream fi = null;
    FileOutputStream fo = null;
    try {
        //1.创建File类的对象
        File srcFile = new File(srcPath);
        File destFile = new File(destPath);

        //2.创建流对象
        fi = new FileInputStream(srcFile);
        fo = new FileOutputStream(destFile);

        //3.文件的复制操作
        int len;
        byte[] buffer = new byte[1024];  //这个数组的大小对传输速度起着很大的作用
        while ((len = fi.read(buffer)) != -1){
            fo.write(buffer, 0, len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        //4.关闭文件流
        try {
            fo.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            fi.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
/* 测试文件大小：673MiB */
/* 当byte数组大小为1024时：*/
/* 所用时间为：7175毫秒 */
/* 当byte数组大小为5时：*/
/* 所用时间为：1365703毫秒 */
/* 可见，byte数组的大小，会影响复制文件所用的时间 */
```

## 缓冲流

缓冲流的相关类：

+ BufferedInputStream
+ BufferedOutputStream
+ BufferedReader
+ BufferedWriter

前两种是字节流，后两种是字符流。缓冲流是一种**处理流**，上文提到的File一揽子的流是**节点流**，或者文件流。

作用：提高流的读取，写入的速度。

### 实例：指定文件复制的缓冲流实现

```java
public void BufferedStreamTest() {
    long start = System.currentTimeMillis();
    
    BufferedInputStream bis = null;
    BufferedOutputStream bos = null;
    try {
        //1.创建文件
        File srcFile = new File("IOTest/test.exe");
        File destFile = new File("IOTest/dest.exe");

        //2.1创建节点流流
        FileInputStream fis = new FileInputStream(srcFile);
        FileOutputStream fos = new FileOutputStream(destFile);
        //2.2创建缓冲流
        bis = new BufferedInputStream(fis);
        bos = new BufferedOutputStream(fos);

        //3.复制细节
        byte[] buffer = new byte[1024];
        int len;
        while ((len = bis.read(buffer)) != -1) {
            bos.write(buffer, 0, len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        //4.关闭流
        //要求：先关闭外层的流，再关闭内层的流
        try {
            if (bis != null)
                bis.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            if (bos != null)
                bos.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        //说明：关闭外层流的同时，内层流会自动的进行关闭，可以省略
        //fos.close();
        //fis.close();
    }
    
    long end = System.currentTimeMillis();
    System.out.println("所用时间为："+(end - start)+"毫秒");
}
/* 测试文件大小：673MiB */
/* 当byte数组大小为1024时：*/
/* 所用时间为：1593毫秒 */
```

|       项目       | 文件流  | 缓冲流  |
| :--------------: | :-----: | :-----: |
|   **文件大小**   | 673MiB  | 673MiB  |
| **byte数组大小** |  1024   |  1024   |
|   **运行时间**   | 7175 ms | 1593 ms |

从上面的实验可知Buffered流速度更快。

## 转换流

+ 转换流提供了在**字节流**和**字符流**之间的转换

+ 用于转换不同的字符编码

+ 转换流的相关类：

  + InputStreamReader：

    将一个字节的输入流转换为字符的输入流。

  + OutputStreamWriter：

    将一个字符的输出流转换为字节的输出流。

+ 解码：字节、字节数组 ===>  *字符、字符数组*
+ 编码：*字符、字符数组* ===>  字节、字节数组
+ 字符集：UTF-8，gbk等等。

```java
/*
* 此时异常处理，也应该使用try-catch
* InputStreamReader实现字节的输出流到字符的输入流的转换
*/
public void test1() throws IOException {
    FileInputStream fis = new FileInputStream("IOTest/test.txt");
    //参数2声明了字符集，具体使用那个字符集取决于文件
    InputStreamReader isr = new InputStreamReader(fis, "UTF-8");  //系统默认的字符集是UTF-8

    char[] cbuf = new char[20];
    int len;
    while ((len = isr.read(cbuf)) != -1){
        String str = new String(cbuf, 0, len);
        System.out.print(str);
    }

    isr.close();
}
```

### 转换流实现文件的转码

```java
//实现文件转码的操作
//综合使用InputStreamReader和OutputStreamWriter
public void test2() {
    InputStreamReader isr = null;
    OutputStreamWriter osw = null;

    try {
        //创建文件对象
        File file1 = new File("IOTest/test.txt");
        File file2 = new File("IOTest/test-gbk.txt");

        //创建文件流对象
        FileInputStream fis = new FileInputStream(file1);
        FileOutputStream fos = new FileOutputStream(file2);

        //创建转换流的对象
        isr = new InputStreamReader(fis, "UTF-8");
        osw = new OutputStreamWriter(fos, "gbk");

        //读写过程
        char[] cbuf = new char[20];
        int len;
        while ((len = isr.read(cbuf)) != -1) {
            osw.write(cbuf, 0, len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        //施行关闭流操作
        if (isr != null) {
            try {
                isr.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        if (osw != null) {
            try {
                osw.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

}
```

### 字符集

+ **ASCII**：美国标准信息交换码。
+ **ISO8859-1**：拉丁码表，欧洲码表。
+ **GB2312**：中国的中文码表，最多两个字节编码所有字符。
+ **GBK**：中国的中文编码表升级，融合了更多的中文文字符号，最多两个字节编码
+ **Unicode**：国际编码，融合了目前人类是同的所有字符，位每个字符分配唯一的字符码。
+ **UTF-8**：变长的编码方式，可用1-4个字节来表示一个字符。

 ## 标准的输入输出流

1. 标准的输入输出流

   **System.in**：标准的输入流，默认从键盘输入。

   **System.out**：标准的输出流，默认从控制台输出。

2. System类的setIn(InputStream in) / setOut(PrintStream out) 方法重新指定输入和输出的方式

#### 小练习：输入字符串，“e”结束程序

```java
public void StranderdIOStream() {
    BufferedReader br = null;

    try {
        //创建一个转换流
        InputStreamReader isr = new InputStreamReader(System.in);
        br = new BufferedReader(isr);

        while (true) {
            System.out.println("请输入字符串：");
            String data = br.readLine();
            if (data.equalsIgnoreCase("e") || data.equalsIgnoreCase("exit")){
                System.out.println("程序结束");
                break;
            }

            String upperCase = data.toUpperCase();
            System.out.println(upperCase);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        //关闭流
        if (br != null) {
            try {
                br.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 打印流(了解)

+ 实现将**基本数据类型**的数据格式化为**字符串**输出

+ 打印流**PrintStream**和**PrintWriter**：

  + 提供一系列重载的print()和println()方法，用于**多种类型的输出**。

  + PrintStream和PrintWriter**不会抛出**IOException异常

  + PrintStream和PrintWriter有**自动flush功能**

    > 自动刷新模式（写入换行符或者字节 '\n'时都会刷新缓冲区）

  + PrintStream打印的所有字符都使用平台的默认字符编码转换为字节。在需要写入字符而不是写入字节的情况下，应该使用PrintWriter类。

  + System.out返回的是**PrintStream**的实例

```java
public void PrintStreamTest() {
    PrintStream ps = null;
    try {
        FileOutputStream fos = new FileOutputStream(new File("IOTest/testOutput.txt"));
        //创建打印输出流，设置为自动刷新模式（写入换行符或者字节 '\n'时都会刷新缓冲区）
        ps = new PrintStream(fos, true);
        if (ps != null)  //把标准输出流（控制台输出）改成文件输出
            System.setOut(ps);


        for (int i = 0; i <= 255; i++) {   //输出ASCII字符
            System.out.print((char)i);
            if (i%50 == 0)    //每50个数换一行
                System.out.println();  //换行
        }


    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } finally {
        if (ps != null)
            ps.close();
    }
}
```

## 数据流(了解)

+ 方便操作java语言的基本数据类型和String的数据，可以使用数据流。
+ 数据流有两个类：
  + **DataInputStream**和**DataOutputStream**
  + 作用：用于读取和写出基本数据类型的变量或字符串。

```java
//写入数据到文件中
public void dataOutputStreamTest(){

    DataOutputStream dos = null;
    try {
        dos = new DataOutputStream(new FileOutputStream("IOTest/data.txt"));

        dos.writeUTF("ABC");
        dos.flush();  //刷新一下将数据写入文件
        dos.writeInt(345);
        dos.flush();
        dos.writeBoolean(true);
        dos.flush();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        if (dos != null) {
            try {
                dos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}

//从文件中读取数据
public void dataInputStreamTest() {

    DataInputStream dis = null;
    try {
        dis = new DataInputStream(new FileInputStream("IOTest/data.txt"));
        //读取文件中的数据
        String name = dis.readUTF();
        int age = dis.readInt();
        boolean sex = dis.readBoolean();

        System.out.println("name = " + name);
        System.out.println("age = " + age);
        System.out.println("sex = " + sex);
        
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        //关闭流
        if (dis != null) {
            try {
                dis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

**注意！！！** 在读取文件时，一定要读取顺序与写入顺序一致，否则文件读取时会出现错误。