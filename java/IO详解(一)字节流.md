file是java.io.包下的类，file类的对象，用于代表当前操作系统的文件(可以是文件或者文件夹)

file类只能对文件本身进行操作，不能读写文件里面存储的数据

IO流用于读写数据

file代表文本

# File

* 构造方法

```java
File(String pathname)//通过将给定的路径名字字符串转换为抽象路劲名来创建新的File实例
    File f1 = new File("D:\\resource\\a.txt");
    File f2 = new File("D:/resource/a.txt");
    File f3 = new File("D:"+ File.resource+"resource"+File.resource+"a.txt");
//File.resource 为api，等同于系统的分隔符
f1.length()//获取文件大小
f1.exists()//判断文件是否存在
```

File可以指代一个不存在的文件路径，它封装的对象仅仅是一个路径名

File对象可以代表文件，也可以代表文件夹

绝对路径：带盘符的(D:\\resource\\a.txt)

相对路径：不带盘符，从当前工程下的目录开始寻找(resource\\a.txt)

* 常用方法一：判断文件类型，获取文件信息 

  ![image-20240122140856698](D:\study-for-qiuzhao\java\assets\image-20240122140856698.png)

   

* 常用方法二:创建文件，删除文件

  ```java
  public boolen createNewFile()//创建一个新文件，成功则返回true，若文件存在，则创建失败
      
  public boolen mkdir()//创建一个文件夹，只能创建一级文件夹
      
  pbulic boolen mkdirs()//创建多级文件夹
      
  public boolen delete()//删除文件，或者空文件夹，不能删除非空文件夹，删除后的文件不会放入回收站
  ```

* 常用方法三：遍历文件夹

  ```java
  public String[] list()//获取当前目录下的所有“一级文件名称”到一个字符串数组中
  public File[] listFiles()//获取当前目录下所有“一级文件对象”到一个文件对象数组中
  ```

  ![image-20240122144250422](D:\study-for-qiuzhao\java\assets\image-20240122144250422.png)


# IO流

>
>
>I:指input，称为输入流，负责把数据读到内存中去
>
>O:指output，称为输出流，负责写数据出去  

![image-20240122170243536](D:\study-for-qiuzhao\java\assets\image-20240122170243536.png)

![image-20240122170452596](D:\study-for-qiuzhao\java\assets\image-20240122170452596.png)

![image-20240122170901321](D:\study-for-qiuzhao\java\assets\image-20240122170901321.png)

### FileInputStream(文件字节输入流)

![image-20240122172246945](D:\study-for-qiuzhao\java\assets\image-20240122172246945.png)

![image-20240122175446385](D:\study-for-qiuzhao\java\assets\image-20240122175446385.png)

```java
//创建文件字节输入流管道，与源文件接通
InputStream is = new FileInputStream("D:\\resource\\a.txt");
//开始读取文件的字节数据
int b1 = is.read()//每次读入一个字节，如果文件中已经没有数据了，则返回-1；
//流使用完后，必须关闭，释放资源
is.close();

---
    
byte[] buffer = new byte[1024];
//每次读入多个字节到字节数组中去，返回读取的字节数量，读取完毕，返回-1；
int b2 = is.read(buffer);//返回读入的字节数

---
//一次读入全部数据
    方法一：准备一个字节数组，大小与文件大小一样
    方法二：
    public byte[] readAllBytes()//直接将当前字节输入流对应的文件对象的字节数据装到一个字节数组返回
```

### FileOutputStream

 ![image-20240122200231430](D:\study-for-qiuzhao\java\assets\image-20240122200231430.png)

![image-20240122200357982](D:\study-for-qiuzhao\java\assets\image-20240122200357982.png)

```java
OutputStream os = new FileOutputStream("D:\\resource\\a.txt");//覆盖数据
OutputStream os = new FileOutputStream("D:\\resource\\a.txt",true);//追加数据
os.write(97);//97也是一个字节，代表a
os.write('b');//'b'也是一个字节
//写入了-》ab

---
byte[] bytes = "我爱你中国abc".getBytes();
os.write(bytes);

os.close();//关闭流
```

