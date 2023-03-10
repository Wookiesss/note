# File

- File对象就表示一个路径，可以是文件的路径、也可以是文件夹的路径
- 这个路径可以是存在的，也允许是不存在的

## 构造方法

| 方法名称                                  | 说明                                               |
| ----------------------------------------- | -------------------------------------------------- |
| public File(String pathname)              | 根据文件路径创建文件对象                           |
| public File(String parent , String child) | 根据父路径名字符串和子路径名字符串创建文件对象     |
| public File(File parent , String child)   | 根据父路径对应文件对象和子路径名字符串创建文件对象 |

```java
//1.根据字符串表示的路径，变成File对象
String str = "C:\\Users\\admin\\Desktop\\a.txt";
File f1 = new File(str);
//2.父级路径：C:\Users\admin\Desktop
//子级路径：a.txt
String parent = "C:\\Users\\admin\\Desktop";
String child = "a.txt";
File f2 = new File(parent,child);
//3.把一个File表示的路径和String表示路径进行拼接
File parent2 = new File("C:\\Users\\admin\\Desktop");
String child2 = "a.txt";
File f4 = new File(parent2,child2);
```

## 常用成员方法

### 判断

| 方法名称                     | 说明                               |
| ---------------------------- | ---------------------------------- |
| public boolean isDirectory() | 判断此路径名表示的File是否为文件夹 |
| public boolean isFile()      | 判断此路径名表示的File是否为文件   |
| public boolean exists()      | 判断此路径名表示的File是否存在     |

### 获取

| 方法名称                        | 说明                                |
| ------------------------------- | ----------------------------------- |
| public long length()            | 返回文件的大小(字节)                |
| public String getAbsolutePath() | 返回文件的绝对路径                  |
| public String getPath()         | 返回定义文件时使用的路径            |
| public String getName()         | 返回文件的名称，带后缀              |
| public long lastModified()      | 返回文件的最后修改时间 (时间毫秒值) |

**length方法细节：**无法获取文件夹的大小，返回0



### 创建和删除

| 方法名称                       | 说明                 |
| ------------------------------ | -------------------- |
| public boolean createNewFile() | 创建一个新的空的文件 |
| public boolean mkdir()         | 创建单级文件夹       |
| public boolean mkdirs()        | 创建多级文件夹       |
| public boolean delete()        | 删除文件、空文件夹   |

**createNewFile细节：**

- 如果当前路径表示的文件是不存在的，则创建成功，方法返回true

   如果当前路径表示的文件是存在的，则创建失败，方法返回false

- 如果父级路径是不存在的，那么方法会有异常IOException

- createNewFile方法创建的一定是文件，如果路径中不包含后缀名，则创建一个没有后缀的文件

**mkdir细节：**

- windows当中路径是唯一的，如果当前路径已经存在，则创建失败，返回false
- mkdir方法只能创建单级文件夹，无法创建多级文件夹。

**mkdirs细节：**

- 既可以创建单级的，又可以创建多级的文件夹

**detele细节：**

- 如果删除的是文件，则直接删除，不走回收站。
- 如果删除的是空文件夹，则直接删除，不走回收站
- 如果删除的是有内容的文件夹，则删除失败



### 获取并遍历

| 方法名称                                       | 说明                                     |
| ---------------------------------------------- | ---------------------------------------- |
| public static File[] listRoots()               | 列出可用的文件系统根(盘符)               |
| public String[] list()                         | 获取当前该路径下所有内容                 |
| public String[] list(FilenameFilter filter)    | 利用文件名过滤器获取当前该路径下所有内容 |
| **public File[] listFiles()**                  | **获取当前该路径下所有内容**             |
| public File[] listFiles(FileFilter filter)     | 利用文件名过滤器获取当前该路径下所有内容 |
| public File[] listFiles(FilenameFilter filter) | 利用文件名过滤器获取当前该路径下所有内容 |

**listFiles细节：**

- 当调用者File表示的路径不存在时，返回null
- 当调用者File表示的路径是文件时，返回null
- 当调用者File表示的路径是一个空文件夹时，返回一个长度为0的数组
- 当调用者File表示的路径是一个有内容的文件夹时，将里面所有文件和文件夹的路径放在File数组中返回（包含隐藏文件）
- 当调用者File表示的路径是需要权限才能访问的文件夹时，返回null

**代码示例：**

```java
public static void main(String[] args) {
    //需求：找到电脑中所有以avi结尾的电影。（需要考虑子文件夹）
    findAVI();
}

public static void findAVI(){
    //获取本地所有的盘符
    File[] arr = File.listRoots();
    for (File f : arr) {
        findAVI(f);
    }
}

public static void findAVI(File src){
    //1.进入文件夹src
    File[] files = src.listFiles();
    //2.遍历数组,依次得到src里面每一个文件或者文件夹
    if(files != null){
        for (File file : files) {
            if(file.isFile()){
                //3，判断，如果是文件，就可以执行题目的业务逻辑
                String name = file.getName();
                if(name.endsWith(".avi")){
                    System.out.println(file);
                }
            }else{
                //4，判断，如果是文件夹，就可以递归
                //细节：再次调用本方法的时候，参数一定要是src的次一级路径
                findAVI(file);
            }
        }
    }
}
```

