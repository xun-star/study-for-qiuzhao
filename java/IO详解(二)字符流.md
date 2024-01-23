# 字符流

* FileReader

  ![image-20240123132610342](D:\study-for-qiuzhao\java\assets\image-20240123132610342.png)

  ```java
  //创建一个文件字符输入流管道与源文件接通
  Reader fr = new FileReader("D:\\resource\\a.txt");//覆盖管道
  Reader fr = new FileReader("D:\\resource\\a.txt",true);//追加管道
  int c = fr.read();
  
  ---
  
  char[] buffer = new char[1024];
  int len;
  while((len = fr.read(buffer)) != -1){
  	String out = new String(buffer,0,len);
  	System.out.print(out);
  };//性能好，系统调用少
  ```

* FileWrite

  ![image-20240123133924474](D:\study-for-qiuzhao\java\assets\image-20240123133924474.png)

  ![image-20240123134002914](D:\study-for-qiuzhao\java\assets\image-20240123134002914.png)

  ```java
  Reader fr = new FileReader("D:\\resource\\a.txt");
  fr.write("\r\n");//写入换行
  fr.flush();//刷新流
  fr.close();//关闭流包含刷新流的操作
  ```

  **字符输出流写出数据后，必须刷新流，或者关闭流，写出去的数据才能生效**

  ![image-20240123135449314](D:\study-for-qiuzhao\java\assets\image-20240123135449314.png)

# 缓冲流

![image-20240123135620001](D:\study-for-qiuzhao\java\assets\image-20240123135620001.png)

缓冲流的作用：对原始流进行包装，以此来提高原始流读写数据的性能

字节输入缓冲流 /字节输出缓冲流 的缓冲池大小为8KB

![image-20240123140325186](D:\study-for-qiuzhao\java\assets\image-20240123140325186.png)

![image-20240123141648100](D:\study-for-qiuzhao\java\assets\image-20240123141648100.png)

![image-20240123141705597](D:\study-for-qiuzhao\java\assets\image-20240123141705597.png)

![image-20240123150116118](D:\study-for-qiuzhao\java\assets\image-20240123150116118.png)

# 转换流

如果**代码编码**和被读取的**文本文件编码是一致**的，使用**字符流**文本文件时，**不会出现乱码**

如果**代码编码**和被读取的**文本文件编码是不一致**的，使用**字符流**文本文件时，**会出现乱码**

>
>
>GBK编码中，汉字是两个字节，数字是一个字节
>
>UTF8编码中，汉字是三个字节，所以会出现乱码

![image-20240123152558476](D:\study-for-qiuzhao\java\assets\image-20240123152558476.png)

字符输入转换流：解决不同编码时，字符流读取文本内容乱码的问题

**解决思路：先获取文件的原始字节流，在将其按照真实的字符集编码转成字符输入流，这样字符输入流中的字符就不是乱码了**

![image-20240123152942278](D:\study-for-qiuzhao\java\assets\image-20240123152942278.png)

![image-20240123194215755](D:\study-for-qiuzhao\java\assets\image-20240123194215755.png)

# 打印流

可以实现更方便，更高效的打印数据出去，实现打印啥出去就是啥出去

![image-20240123194545479](D:\study-for-qiuzhao\java\assets\image-20240123194545479.png)

![image-20240123194636571](D:\study-for-qiuzhao\java\assets\image-20240123194636571.png)

![image-20240123195116524](D:\study-for-qiuzhao\java\assets\image-20240123195116524.png)

* 打印数据的功能是一模一样的，都是使用方便，性能高效(核心优势)
* PrintStream继承自字节输出流OutputStream，因此支持写字节数据的方法
* PrintWriter继承自字符输出流Writer，因此支持写字符数据出去

# 数据流

允许把数据和其类型一并写出去

![image-20240123195841517](D:\study-for-qiuzhao\java\assets\image-20240123195841517.png)

![image-20240123200144369](D:\study-for-qiuzhao\java\assets\image-20240123200144369.png)

**读写数据的顺序要一致**

# 序列化流

对象序列化：把java对象写入到文件中去

对象反序列化：把文件里的java对象读出来

![image-20240123200755512](D:\study-for-qiuzhao\java\assets\image-20240123200755512.png)







