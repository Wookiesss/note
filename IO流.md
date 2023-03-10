# IO流

## 概述

存储和读取数据的解决方案

I：input      O：output

流：像水流一样传输数据

根据数据的流向分为：**输入流**和**输出流**

- **输入流** ：把数据从`其他设备`上读取到`内存`中的流。  文件->程序
- **输出流** ：把数据从`内存` 中写出到`其他设备`上的流。 程序->文件

根据数据的类型分为：**字节流**和**字符流**

- **字节流** ：以字节为单位，读写数据的流。可以操作**所有类型**的文件
- **字符流** ：以字符为单位，读写数据的流。只能操作**纯文本**文件

纯文本文件：用Windows系统自带的记事本打开并且能读懂的文件

​                        txt文件、md文件、xml文件、lrc文件

## 体系结构

![1](images\IO流\1.png)

## 字节流

### OutputStream

#### 常用方法

| 方法名称                                      | 说明                                                   |
| --------------------------------------------- | ------------------------------------------------------ |
| public void close()                           | 关闭此输出流并释放与此流有关的所有系统资源             |
| public void flush()                           | 刷新此输出流并强制写出所有缓冲的输出字节               |
| public abstract void write(int b)             | 将指定的字节写入此输出流                               |
| public void write(byte[] b)                   | 将 b.length 个字节从指定的 byte 数组写入此输出流       |
| public void write(byte[] b, int off, int len) | 将指定byte数组中从偏移量off开始的len个字节写入此输出流 |

#### FileOutputStream

把程序中的数据写到本地文件上，是字节流的基本流

| 构造方法                             | 说明                                                   |
| ------------------------------------ | ------------------------------------------------------ |
| public FileOutputStream(File file)   | 创建一个向指定File对象表示的文件中写入数据的文件输出流 |
| public FileOutputStream(String name) | 创建一个向具有指定名称的文件中写入数据的输出文件流     |



**创建字节输出流对象**

```java
FileOutputStream fos = new FileOutputStream(file)；
```

- 参数是字符串表示的路径或者File对象都是可以的
- 如果文件不存在会创建一个新的文件，但是要保证父级路径是存在的
- 如果文件已经存在，则会清空文件

**写数据**

```java
fos.write(88);
```

- write方法的参数是整数，但是实际上写到本地文件中的是整数在ASCII上对应的字符

**释放资源**

```java
fos.close();
```

- 每次使用完流之后都要释放资源

**换行**

```java
fos.write("\r\n".getBytes());
```

- 回车符\r : 回到一行的开头
- 换行符\n : 下一行
- Windows : \r\n
- Linux : \n
- Mac : \r

**追加续写**

构造方法中还提供了两种方法，第二个参数boolean append表示追加写开关

true 表示续写加数据不清空原有数据，false 表示清空原有数据再创建新文件覆盖原有文件

- `public FileOutputStream(File file, boolean append)`
- `public FileOutputStream(String name, boolean append)`

```java
FileOutputStream fos = new FileOutputStream(file,true)；
```



### InputStream

#### 常用方法

| 方法名称                   | 说明                                                  |
| -------------------------- | ----------------------------------------------------- |
| public void close()        | 关闭此输出流并释放与此流有关的所有系统资源            |
| public abstract int read() | 从输入流读取数据的下一个字节                          |
| public int read(byte[] b)  | 从输入流中读取一些字节数，并将它们存储到字节数组 b 中 |



#### FileInputStream

**构造方法**

```java
FileInputStream(File file)
FileInputStream(String name)
```

**创建字节输入流对象**

- 如果文件不存在，就直接报错

**读取数据**

- read()空参方法：一次读一个字节，读出来的是数据在ASCII上对应的数字
- 读到文件末尾了，read方法返回-1

```java
//需求：拷贝文件
//把D:\aaa\movie.mp4 文件拷贝到 D:\bbb\copy.mp4
//创建对象
FileInputStream fis = new FileInputStream("D:\\aaa\\movie.mp4");
FileOutputStream fos = new FileOutputStream("D:\\bbb\\copy.mp4");
//拷贝
int len;
byte[] bytes = new byte[1024 * 1024 * 5];//5MB大小的字节数组
while( (len = fis.read(bytes)) != -1){
    fos.write(bytes,0,len);
}
//释放资源
fos.close();
fis.close();
//流的关闭原则: 先开后关，后开先关
```

**释放资源**

- 每次使用完流必须要释放资源



## 字符集

- 在计算机中，任意数据都是以二进制的形式来存储的
- 计算机中最小的存储单元是一个字节
- ASCII字符集中，一个英文占一个字节
- 简体中文版Windows，默认使用GBK字符集
- GBK字符集完全兼容ASCII字符集
  - 一个英文占一个字节，二进制第一位是0
  - 一个中文占两个字节，二进制高位字节的第一位是1

- Unicode字符集的UTF-8编码格式
  - 一个英文占一个字节，二进制第一位是0，转成十进制是正数
  - 一个中文占三个字节，二进制第一位是1，第一个字节转成十进制是负数



## 编码解码方法

| String类中的方法                           |         说明         |
| :----------------------------------------- | :------------------: |
| public byte[] getBytes()                   | 使用默认方式进行编码 |
| public byte[] getBytes(String charsetName) | 使用指定方式进行编码 |
| String(byte[] bytes)                       | 使用默认方式进行解码 |
| String(byte[] bytes , String charsetName)  | 使用指定方式进行解码 |

**出现乱码原因：**

- 读取数据时未读完整个汉字
- 编码和解码时的方式不统一



## 字符流

字符流的底层其实就是字节流，适合对纯文本文件进行读写操作

**特点**

- 输入流：一次读一个字节，遇到中文时，一次读多个字节
- 输出流：底层会把数据按照指定的编码方式进行编码，变成字节再写到文件中

### Reader

#### 常用方法

| 方法名称                       | 说明                             |
| ------------------------------ | -------------------------------- |
| public int read()              | 读取数据，读到末尾返回-1         |
| public int read(char[] buffer) | 读取多个数据，读到未尾返回-1     |
| public void close()            | 关闭该流并释放与之关联的所有资源 |



#### FileReader

| 构造方法                           | 说明                       |
| ---------------------------------- | -------------------------- |
| public FileReader(File file)       | 创建字符输入流关联本地文件 |
| public FileReader(String pathname) | 创建字符输入流关联本地文件 |

**读取数据**

```java
/*
*read () 细节:默认也是一个字节一个字节的读取的,如果遇到中文就会一次读取多人
*在读取之后，方法的底层还会进行解码并转成十进制,并作为返回值
*如果想看到中文汉字，需要将这些十进制数据进行强转
*/
FileReader fr = new FileReader("D:\\test\\1.txt");
int ch;
while((ch = fr.read()) != -1){
    System.out.print((char)ch);
}
fr.close();
//带参方法：把读取、解码、强转三步合并了，强转后的字符放到数组中
char[] chars = new char[2];
int len;
while((len = fr.read(chars)) != -1){
    System.out.print(new String(chars,0,len));
}
```



### Writer

#### 常用方法

| 方法名称                                    | 说明                               |
| ------------------------------------------- | ---------------------------------- |
| void write(int c)                           | 写入一个字符                       |
| void write(String str)                      | 写入一个字符串                     |
| void write(String str , int off , int len)  | 写入一个字符串的一部分             |
| void write(char[] cbuf)                     | 写入一个字符数组                   |
| void write(char[] cbuf , int off , int len) | 写入字符数组的一部分               |
| public abstract void close()                | 关闭此流，但要先刷新它             |
| public abstract void flush()                | 将缓冲区中的数据，刷新到本地文件中 |

**刷新和关流：**

- flush刷新： 刷新之后，还可以继续往文件中写出数据
- close关流：断开通道，无法再往文件中写出数据



#### FileWriter

| 构造方法                                            | 说明                             |
| --------------------------------------------------- | -------------------------------- |
| public FileWriter(File file)                        | 创建字符输出流关联本地文件       |
| public FileWriter(String pathname)                  | 创建字符输出流关联本地文件       |
| public FileWriter(File file , boolean append)       | 创建字符输出流关联本地文件，续写 |
| public FileWriter(String pathname , boolean append) | 创建字符输出流关联本地文件，续写 |



### 底层原理

#### 字符输入流

**创建字符输入流对象底层：**

- 关联文件，并创建缓冲区(长度为8192的字节数组)

**读取数据底层：**

1. 判断缓冲区中是否有数据可以读取
2. **缓冲区没有数据**：就从文件中获取数据，装到缓冲区中，每次尽可能装满缓冲区
                                  如果文件中也没有数据了，返回-1
3. **缓冲区有数据：**就从缓冲区中读取



#### 字符输出流

**写入数据底层：**

- 写入的数据先通过write方法写在缓冲区中(长度为8192的字节数组)
- 当以下情况出现后，才会将缓存区中的数据保存到目的地文件中

1. 缓存区装满了
2. 手动刷新(flush)
3. 释放资源(close)





## 缓冲流

缓冲流,也叫高效流，是对4个基本的`FileXxx` 流的增强。按照数据类型分类：

- **字节缓冲流**：`BufferedInputStream`，`BufferedOutputStream`
- **字符缓冲流**：`BufferedReader`，`BufferedWriter`

**缓冲流的基本原理：**在创建流对象时，会创建一个内置的默认长度为8192的缓冲区数组，通过缓冲区读写，减少系统IO次数，从而提高读写的效率。

**细节：**

- 本质上还是依赖于基本流
- 只需要关闭缓冲流，因为在缓冲流的方法中对基本流做了关流处理

### 字节缓冲流

| 构造方法                                      | 说明                     |
| --------------------------------------------- | ------------------------ |
| public BufferedInputStream(InputStream in)    | 创建一个新的缓冲输入流。 |
| public BufferedOutputStream(OutputStream out) | 创建一个新的缓冲输出流   |

构造方法也可传入第二个参数`int size`指定缓冲区的大小

```java
// 创建字节缓冲输入流
BufferedInputStream bis = new BufferedInputStream(new FileInputStream("D:\\1.txt"));
// 创建字节缓冲输出流
BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("D:\\1.txt"));
```

### 字符缓冲流

| 构造方法                          | 说明                   |
| :-------------------------------- | :--------------------- |
| public BufferedReader(Reader in)  | 创建一个新的缓冲输入流 |
| public BufferedWriter(Writer out) | 创建一个新的缓冲输入流 |

构造方法也可传入第二个参数`int size`指定缓冲区的大小

|  BufferedReader特有方法  |                 说明                 |
| :----------------------: | :----------------------------------: |
| public String readLine() | 读取一行数据，如无数据可读，返回null |

```java
//readLine方法在读取的时候，一次读一整行，遇到回车换行结束
//但是不会把回车换行读到内存当中
//创建字符缓冲输出流对象
BufferedReader br = new BufferedReader(new FileReader("D:\\1.txt"));
//循环读取数据
String line;
while((line = br.readLine())!=null){
    System.out.println(line);
}
//释放资源
br.close();
```

| BufferedWriter特有方法 |               说明               |
| :--------------------: | :------------------------------: |
| public void newLine()  | 跨平台的换行，由系统属性定义符号 |

```java
//创建字符缓冲输出流对象
BufferedWriter bw = new BufferedWriter(new FileWriter("D:\\1.txt"));
//写入数据
bw.write("123");
bw.newLine();
bw.write("123");
//释放资源
br.close();
```



## 转换流

字节流和字符流间的桥梁，是一种字符流

### InputStreamReader

字节流通向字符流的桥梁， 可使用指定的字符集，读取字节并将其解码为字符

| 构造方法                                              | 说明                           |
| :---------------------------------------------------- | :----------------------------- |
| InputStreamReader(InputStream in)                     | 创建一个使用默认字符集的字符流 |
| InputStreamReader(InputStream in, String charsetName) | 创建一个指定字符集的字符流     |

### OutputStreamWriter

字符流通向字节流的桥梁，可使用指定的字符集，将要写入流中的字符编码成字节

| 构造方法                                                | 说明                           |
| :------------------------------------------------------ | :----------------------------- |
| OutputStreamWriter(OutputStream in)                     | 创建一个使用默认字符集的字符流 |
| OutputStreamWriter(OutputStream in, String charsetName) | 创建一个指定字符集的字符流     |

```java
//需求：将本地文件中的GBK文件，转成UTF-8
//1.JDK11以前的方案
InputStreamReader isr = new InputStreamReader(new FileInputStream("D:\\1.txt"),"GBK");
OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("D:\\1.txt"),"UTF-8");

int b;
while((b = isr.read()) != -1){
    osw.write(b);
}

osw.close();
isr.close();

//2.替代方案
FileReader fr = new FileReader("D:\\1.txt", Charset.forName("GBK"));
FileWriter fw = new FileWriter("D:\\1.txt",Charset.forName("UTF-8"));
int b;
while ((b = fr.read()) != -1){
    fw.write(b);
}
fw.close();
fr.close();
```



## (反)序列化流

### ObjectOutputStream

序列化流，又叫对象操作输出流，可以把Java中的对象写到本地文件中。是字节流

**构造方法：**

`public ObjectOutputStream(OutputStream out)`： 创建一个指定OutputStream的ObjectOutputStream

**成员方法：**

`public final void writeObject (Object obj)`： 将指定的对象写出

```java
//1.创建对象
Student stu = new Student("zhangsan",23);
//2.创建序列化流的对象/对象操作输出流
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("D:\\1.txt"));
//3.写出数据
oos.writeObject(stu);
//4.释放资源
oos.close();
```



### ObjectInputStream

反序列化流，又叫对象操作输入流，可以把序列化到本地文件中的对象，读取到程序中来。是字节流

**构造方法：**

`public ObjectInputStream(InputStream in)`： 创建一个指定InputStream的ObjectInputStream

**成员方法：**

`public final Object readObject ()` : 读取一个对象。

```java
//1.创建反序列化流的对象
ObjectInputStream ois = new ObjectInputStream(new FileInputStream("D:\\1.txt"));
//2.读取数据
Student o = (Student) ois.readObject();
//3.打印对象
System.out.println(o);
//4.释放资源
ois.close();
```



### 细节汇总

- 使用序列化流将对象写到文件时，需要让Javabean类实现Serializable接口
  否则，会出现NotSerializableException异常

- 序列化流写到文件中的数据是不能修改的，一旦修改就无法再次读回来了

- 序列化对象后，修改了Javabean类，再次反序列化，会抛出`InvalidClassException`异常

  解决方案：给Javabean类添加`serialVersionUID` (序列号、版本号)
                     用`private static final long`修饰

- 如果一个对象中的某个成员变量的值不想被序列化，又该如何实现呢?

  解决方案:给该成员变量加`transient`瞬态关键字修饰，该关键字标记的成员变量不参与序列化过程



## 打印流

### PrintStream

是字节打印流，底层无缓冲区

| 构造方法                                              | 说明                         |
| ----------------------------------------------------- | ---------------------------- |
| public PrintStream(OutputStream/File/String)          | 关联字节输出流/文件/文件路径 |
| public PrintStream(String fileName , Charset charset) | 指定字符编码                 |

| 特有方法                                           | 说明                         |
| -------------------------------------------------- | ---------------------------- |
| public void println(Xxx xx)                        | 写入任意数据，自动刷新和换行 |
| public void print(Xxx xx)                          | 写入任意数据，不换行         |
| public void printf(String format , Object... args) | 写入带有占位符的语句，不换行 |

### PrintWriter

是字符打印流，底层具有缓冲区，可开启自动刷新

| 构造方法                                                     | 说明                         |
| ------------------------------------------------------------ | ---------------------------- |
| public PrintWriter(Write/File/String)                        | 关联字节输出流/文件/文件路径 |
| public PrintWriter(String fileName , Charset charset)        | 指定字符编码                 |
| public PrintWriter(Write w , boolean autoFlush)              | 自动刷新                     |
| public PrintWriter(0utputStream out , boolean autoFlush , Charset charset) | 指定字符编码且自动刷新       |

特有成员方法跟PrintStream一致

```java
PrintWriter pw = new PrintWriter(new FileWriter("D:\\1.txt",true));
pw.println("你好");
pw.close();
```



## (解)压缩流

```java
/*
*   解压缩流
* */
public class ZipStreamDemo1 {
    public static void main(String[] args) throws IOException {
        //1.创建一个File表示要解压的压缩包
        File src = new File("D:\\aaa.zip");
        //2.创建一个File表示解压的目的地
        File dest = new File("D:\\");
        //调用方法
        unzip(src,dest);
    }
    
    //定义一个方法用来解压
    public static void unzip(File src,File dest) throws IOException {
        //解压的本质：把压缩包里面的每一个文件或者文件夹读取出来，按照层级拷贝到目的地当中
        //创建一个解压缩流用来读取压缩包中的数据
        ZipInputStream zip = new ZipInputStream(new FileInputStream(src));
        //要先获取到压缩包里面的每一个zipentry对象
        //表示当前在压缩包中获取到的文件或者文件夹
        ZipEntry entry;
        while((entry = zip.getNextEntry()) != null){
            System.out.println(entry);
            if(entry.isDirectory()){
                //文件夹：需要在目的地dest处创建一个同样的文件夹
                File file = new File(dest,entry.toString());
                file.mkdirs();
            }else{
                //文件：读取压缩包中的文件，并存放到目的地dest文件夹中（按照层级目录进行存放）
                FileOutputStream fos = new FileOutputStream(new File(dest,entry.toString()));
                int b;
                while((b = zip.read()) != -1){
                    //写到目的地
                    fos.write(b);
                }
                fos.close();
                //表示在压缩包中的一个文件处理完毕了。
                zip.closeEntry();
            }
        }
        zip.close();
    }
}
```

```java
/*
*   压缩流
*   需求：把D:\\aaa文件夹压缩成一个压缩包
* */
public class ZipStreamDemo2 {
    public static void main(String[] args) throws IOException {
        //1.创建File对象表示要压缩的文件夹
        File src = new File("D:\\aaa");
        //2.创建File对象表示压缩包放在哪里（压缩包的父级路径）
        File destParent = src.getParentFile();//D:\\
        //3.创建File对象表示压缩包的路径
        File dest = new File(destParent,src.getName() + ".zip");
        //4.创建压缩流关联压缩包
        ZipOutputStream zos = new ZipOutputStream(new FileOutputStream(dest));
        //5.获取src里面的每一个文件，变成ZipEntry对象，放入到压缩包当中
        toZip(src,zos,src.getName());//aaa
        //6.释放资源
        zos.close();
    }

    /*
    *   参数一：数据源
    *   参数二：压缩流
    *   参数三：压缩包内部的路径
    * */
    public static void toZip(File src,ZipOutputStream zos,String name) throws IOException {
        //1.进入src文件夹
        File[] files = src.listFiles();
        //2.遍历数组
        for (File file : files) {
            if(file.isFile()){
                //3.判断-文件，变成ZipEntry对象，放入到压缩包当中
                ZipEntry entry = new ZipEntry(name + "\\" + file.getName());// aaa\\
                zos.putNextEntry(entry);
                //读取文件中的数据，写到压缩包
                FileInputStream fis = new FileInputStream(file);
                int b;
                while((b = fis.read()) != -1){
                    zos.write(b);
                }
                fis.close();
                zos.closeEntry();
            }else{
                //4.判断-文件夹，递归
                toZip(file,zos,name + "\\" + file.getName());
            }
        }
    }
}
```

## 常用工具包

commons-io

hutool