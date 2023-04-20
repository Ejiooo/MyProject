#### IO框架

lO(Input/Output）是计算机输入/输出的接口。Java的核心库java.io提供了全方面的IO接口，包括：文件系统的操作，文件读写，标准设备输出等等

##### File类及使用

一个File类的对象，表示了磁盘上的文件或目录。

File类提供了与平台无关的方法来对磁盘上的文件或目录进行操作。

File类直接处理文件和文件系统。比如删除文件，获取文件长度大小等信息。

File类没有提供方法从文件读取或向文件存储信息。

构造方法：

```java
File(String directoryPath)
File(string directoryPath, string filonamo)
File(File dirObj,String filename)
```

File定义了获取File对象标准属性的方法

```java
部分Flilo类常用方法:
//文件名称
public String getName()	
//返回此抽象路径名父目录的路径名字符串，如果此路径名没有指定父目录，则返回null。
public String getParont()	
//返回此抽象路径名父目录的抽象路径名﹔如果此路径名没有指定父目录，则返回null
public File getParentFile()
//获取绝对路径
public String getAbsolutePath()
//测试文件是否存在
public boolean exists()
//遍历
public File listFiles()			
//测试此抽象路径名表示的文件是否是一个目录
public boolean isDirectory()
//测试此抽象路径名表示的文件是否是一个标准文件。   
public boolean isFile()			
//文件的长度    
public long length()
//册除此抽象路径名表示的文件或目录。如果此路径名表示一个目录，则该目录必须为空才能删除。
boolean delete()
//创建文件夹
boolean mkdir()					
```



##### 流的概念和分类

流是一个很形象的概念，当程序需要读取数据的时候，就会开启一个通向数据源的流，这个数据源可以是文件，内存，或是网络连接。类似的，当程序需要写入数据的时候，就会开启一个通向目的地的流。这时候你就可以想象数据好像在这其中“流"动一样。

![image-20221129001145435](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221129001145435.png)

###### 流的分类

流按其流向分为“输入流"和“输出流”

流按数据传输单位分为“字节流"和“字符流”

<font color='red'>“字节流"用来读写8位二进制的字节。</font>

<font color='red'>“字符流"用来读写16位二进制字符。</font>

流按功能分为“节点流"和“过滤流”

<font color='red'>“节点流"用于直接操作目标设备的流。例如:磁盘或一块内存区域。</font>

<font color='red'>“过滤流"是对一个已存在的流的链接和封装，通过对数据进行处理为程序提供功能强大、灵活的读写功能。</font>



#### 字节流

##### InputStream抽象类

字节流类用于向字节流读写8位二进制的字节。一般地，字节流类主要用于读写诸如图象或声音等的二进制数据。

字节流类以InputStream和OutputStream为顶层类。它们都是抽象类。

InputStream是定义了字节输入流的抽象类

**lnputStream中定义的方法:**

```java
//从输入流中读取数据的下一个字节
public abstract int read()
//从输入流中读取一定数量的字节，并将其存储在缓冲区数组b中
public int read(byte[] b)
//将输入流中最多len个数组读入byte数组
public int read(byte[] b, int off, int len)
//跳过和丢弃此输入流中的n个字节
public long skip(long n)
//能够获取有多少数据可以读
public int available()
//关闭流
public void close()
```



##### OutputStream抽象类

OutputStream是定义了字节输出流的抽象类

该类所有方法返回void值，在出错情况下抛IOException异常

**OutputStream中定义的方法**

```java
//将知道的字节写入此输出流
public abstract vold write(int b)
//将b.length个字节从指定byte数组写入此输出流
public vold write(byte[] b)
//将指定byte数组中从偏移量off开始的1en个字节写入此输出流。
public vold write(byte[] b, int off, int len)
//强制写出所有缓冲的输出字节
public vold flush()
//关闭流
public vold close()
```



##### FileInputStream和FileOutputStream

每个抽象类都有多个具体的子类，这些子类对不同的外设进行处理，例如磁盘文件，网络连接，甚至是内存缓冲区。



```java
FileInputStream类表示能从文件读取字节的InputStream类
常用的构造方法：
FileInputStream(String filepath);
FileInputStream(File fileObj)
```

```java
FileOutputStream表示能向文件写入字节的OutputStream类
构造方法：
FileOutputStream(String filePath)
FileOutputStream(File fileOb)
FileOutputStream(String filePath, boolean appond)
```



##### ByteArrayInput/OutputStream

ByteArraylnputStream是把字节数组当成源的输入流

```java
两个构造方法，每个都需要一个字节数组提供数据源：
ByteArrayInputStream(byte array[])
ByteArrayInputStream(byte array[], int start, int numBytes)
```

ByteArrayOutputStream是把字节数组当作目标的输出流

```java
//创建一个心得byte数组输出流
ByteArrayOutputStream()
//创建一个新的byte数组输出流，具有指定大小缓冲区(字节为单位)
ByteArrayOutputStream(int numBytes)
```



##### 过滤流介绍

过滤流（ filtered stream）仅仅是为底层透明地提供扩展功能的输入流（输出流)的包装。这些流一般由普通类的方法（即过滤流的一个父类）访问。

过滤字节流FilterlnputStream和FilterOutputStream。

```java
FilterOutputStream(OutputStream os)
FilterInputStream(InputStream is)
```

这些类提供的方法和InputStream及OutputStream类的方法相同

常用的BufferedInputStream和BufferedOutputStream,DatalnputStream和DataOutputSteam就是一个字节过滤流。

##### 装饰模式

装饰模式的概念：

装饰模式以对客户透明的方式动态地给一个对象附加上更多的责任。换言之，客户端并不会觉得对象在装饰前和装饰后有什么不同。装饰模式可以在不使用创造更多子类的情况下，将对象的功能加以扩展。![image-20221129220225892](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221129220225892.png)







#### 字符流

##### Reader和Writer抽象类

字节流提供处理任何类型输入/输出操作的足够功能，但不能直接操作Unicode字符，因而需要字符流

字符流层次结构的顶层是Reader和Writer抽象类

Reader是定义Java的流式字符输入模式的抽象类

```java
Reader抽象类中的方法
public int read(CharBuffor target) throws lOException
public int read()
......   
```

Writer是定义流式字符输出的抽象类

该类的方法都返回Void值并在出错条件下抛IOException异常

```java
Writer抽象类的方法：
public vold write(int c)
public vold write(char[] cbuf)
.....
```



##### FileReader和FileWriter

FileReader类表示可以读取文件内容的Reader类

```java
构造方法：
FileReader(String filePath)
FileReader(File fileObj)
```

FileWriter类表示可以写文件的Writer类

```java
构造方法：
FileWriter(Stripg filePath)
FileWriter(String filePath, boolean appond)
FileWriter(File fileObj)   
```



##### BufferedReader和BufferedWriter

BufferedReader通过缓冲输入提高性能

```java
BufferedReader(Reader inputStream)
BufferedReader(Roader inputStream, int bufSize)
```

BufferedWriter通过缓冲输出提高性能

```java
BufferedWriter(Writer outputStream)
BufferedWriter(Writer outputStream, int bufSize)
```



#### 其他流

##### ObjectInput/OutputStream

ObjectOutputStream和 ObjectInputStream分别与FileOutputStream和FileInputStream一起使用时，可以为应用程序提供对对象的持久存储。我们把对象以某种特定的编码格式写入称之为“序列化"。把写入的编码格式内容还原成对象称之为“反序列化”。

被序列化的对象必须实现Serializable接口。



##### InputStreamReader/OutputStreamWriter

转换流是指将字节流与字符流之间的转换。

转换流的出现方便了对文件的读写，她在字符流与字节流之间架起了一座桥梁，使原本毫无关联的两种流操作能够进行转化，提高了程序的灵活性。

字节流中的数据都是字符时，转成字符流操作更高效。

如果使用非默认编码保存文件或者读取文件时，需要用到转换流，因为字节流的重载构造方法中有指定编码格式的参数，而FielReader与 FileWriter是默认编码的文本文件。

![image-20221129225325723](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221129225325723.png)

##### RandomAccessFile

**◇**RandomAccessFile随机访问文件支持对随机访问文件的读取和写入

◇随机访问文件的行为类似存储在文件系统中的一个大型byte 数组。存在指向该隐含数组的光标	或索引，称为文件指针

◇输入操作从文件指针开始读取字节，随着对字节的读取而前移此文件指针

◇如果随机访问文件以读取/写入模式创建，则输出操作也可用;输出操作从文件指针开始写入字	节，随着对字节的写入而前移此文件指针

◇写入隐含数组末尾之后的输出操作导致该数组扩展。

◇该文件指针可以通过getFilePointer方法读取，通过seek方法设置
