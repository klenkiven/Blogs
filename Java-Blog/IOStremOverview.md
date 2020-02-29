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
            if (bos != null)
                bos.close();
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