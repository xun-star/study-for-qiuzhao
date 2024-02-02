# 基础练习

```java
package test1;

import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {
    public static void main(String[] args) throws IOException {
        ServerSocket ss = new ServerSocket(10086);

        Socket socket = ss.accept();

        InputStream is = socket.getInputStream();
        InputStreamReader isr = new InputStreamReader(is);

        int b;
        while((b = isr.read()) != -1){
            System.out.println((char)b);
        }

        socket.close();
    }
}

```

```java
package test1;

import java.io.IOException;
import java.io.OutputStream;
import java.net.Socket;
import java.util.Scanner;

public class Client {
    public static void main(String[] args) throws IOException {
        //接收数据
        Socket socket = new Socket("127.0.0.1",10086);
        OutputStream os = socket.getOutputStream();
        Scanner sc = new Scanner(System.in);
        //写出数据
        while(true){
            System.out.println("请输入对话信息：");
            String str = sc.nextLine();
            if("886".equals(str)){
                break;
            }
            os.write(str.getBytes());
        }

        //释放资源
        socket.close();

    }
}

```

# 接收和反馈

客户端：发送一条数据，接收服务端反馈的消息并打印

服务端：接受数据并打印，再给客户端返回消息

```java
package test1;

import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {
    public static void main(String[] args) throws IOException {
        //绑定监听端口
        ServerSocket ss = new ServerSocket(10086);
		//等待连接
        Socket socket = ss.accept();

        InputStream is = socket.getInputStream();
        InputStreamReader isr = new InputStreamReader(is);

        int b;
        while((b = isr.read()) != -1){
            System.out.println((char)b);
        }
		//反馈信息
        OutputStream os = socket.getOutputStream();
        os.write("我已收到信息~".getBytes());

        socket.close();
    }
}

```

```java
package test1;

import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.Socket;
import java.util.Scanner;

public class Client {
    public static void main(String[] args) throws IOException {
        //接受数据

        Socket socket = new Socket("127.0.0.1",10086);
        OutputStream os = socket.getOutputStream();
        Scanner sc = new Scanner(System.in);
        //写出数据
        String str = "你好大美中国！！！！";
        os.write(str.getBytes());
        //写出一个结束标记
        socket.shutdownOutput();//关闭通道，使之可以接收到反馈信息
        InputStream is = socket.getInputStream();
        InputStreamReader isr = new InputStreamReader(is);
        int b;
        while((b = isr.read()) != -1){
            System.out.println((char)b);
        }
        //释放资源
        socket.close();

    }
}

```

# 上传文件

客户端：负责将本地文件上传到服务器，并接受反馈信息

服务端：接受客户端上传的文件，上传完毕后给出反馈

```java
package test1;

import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {
    public static void main(String[] args) throws IOException {
        ServerSocket ss = new ServerSocket(10000);

        Socket socket = ss.accept();

        BufferedInputStream bis = new BufferedInputStream(socket.getInputStream());
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("E:\\code\\java\\temp\\Thread\\Thread01\\Thread02\\ServerFile"));
        int len ;
        byte[] bytes = new byte[1024];
        while((len = bis.read(bytes)) != -1){
            bos.write(bytes,0,len);
        }

        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
        bw.write("上传成功");
        bw.newLine();
        bw.flush();

        socket.close();
        ss.close();
    }
}

```

```java
package test1;

import java.io.*;
import java.net.Socket;
import java.util.Scanner;

public class Client {
    public static void main(String[] args) throws IOException {
        Socket socket = new Socket("127.0.0.1",10000);

        //从本地文件中读取数据，并上传到服务器
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream("E:\\code\\java\\temp\\Thread\\Thread01\\Thread02\\clientFile\\photo.jpg"));
        BufferedOutputStream bos = new BufferedOutputStream(socket.getOutputStream());
        byte [] bytes = new byte[1024];
        int len;
        while((len = bis.read(bytes)) != -1){
            bos.write(bytes,0,len);
        }
        //写出结束标志
        socket.shutdownOutput();
        //接受服务器的回显信息
        BufferedReader br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
        String line = br.readLine();
        System.out.println(line);
        //释放资源
        socket.close();
    }
}

```

