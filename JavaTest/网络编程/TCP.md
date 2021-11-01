# TCP



## TCP1

客户端发送内容给服务端，服务端将内容打印到控制台上。



```java
package test.TCPTest;

import org.junit.Test;

import java.io.*;
import java.net.InetAddress;
import java.net.ServerSocket;
import java.net.Socket;

public class TCPTest1 {

    //客户端
    @Test
    public void client() {
        Socket socket = null;
        OutputStream os = null;
        try {
            //1.创建 socket 对象 指明 IP 以及 端口号
            InetAddress inet = InetAddress.getByName("192.168.0.112");
            socket = new Socket(inet,8899);

            //2.获取一个输出流 传输数据
            os = socket.getOutputStream();

            //3.写出数据
            os.write("你好，我是客户端MM".getBytes());
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (os != null) {
                try {
                    os.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    //服务端 利用转换流 不推荐
    @Test
    public void server() {
        ServerSocket ss = null;
        Socket socket = null;
        InputStream is = null;
        InputStreamReader isr = null;
        OutputStreamWriter osw = null;
        try {
            //1.创建服务器端的 ServerSocket，指明端口号
            ss = new ServerSocket(8899);

            //2.调用 accept() 方法接受来自于客户端的 socket
            socket = ss.accept();

            //3.获取输入流
            is = socket.getInputStream();
            isr = new InputStreamReader(is);

            //4.获取输出流
            OutputStream os = socket.getOutputStream();
            osw = new OutputStreamWriter(os);

            //5.获取输入流的数据，通过转换流将 字节转换成字符输入，再转换为字节输出
            char[] cbf = new char[1024];
            int len;
            while ((len = isr.read(cbf)) != -1) {
                osw.write(cbf,0,len);
            }

            //6.在控制台输出输入流的数据
            System.out.println(cbf);
            System.out.println("收到了来自于：" + socket.getInetAddress().getHostAddress() + "的数据");
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (osw != null) {
                try {
                    osw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (is != null) {
                try {
                    is.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (isr != null) {
                try {
                    is.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (ss != null) {
                try {
                    ss.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    //服务端 利用 ByteArrayOutputStream 推荐
    @Test
    public void sever2() {
        ServerSocket ss = null;
        Socket socket = null;
        InputStream is = null;
        ByteArrayOutputStream baos = null;
        try {
            //1.创建服务端的 ServerSocket，指明 IP 和 端口号
            ss = new ServerSocket(8899);

            //2.调用 accept() 接收客户端的 socket
            socket = ss.accept();

            //3.获取输入流
            is = socket.getInputStream();

            //4.获取输入流的数据
            baos = new ByteArrayOutputStream();

            //不建议这样写，可能会有乱码
//        byte[] buffer = new byte[1024];
//        int len;
//        while((len = is.read(buffer)) != -1){
//            String str = new String(buffer,0,len);
//            System.out.print(str);
//        }
            byte[] buffer = new byte[1024];
            int len;
            while ((len = is.read(buffer)) != -1) {
                baos.write(buffer,0,len);
            }
            System.out.println(baos.toString());
            System.out.println("收到了来自于：" + socket.getInetAddress().getHostAddress() + "的数据");
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (baos != null) {
                try {
                    baos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (is != null) {
                try {
                    is.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (ss != null) {
                try {
                    ss.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



## TCP2

客户端发送文件给服务端，服务端将文件保存在本地



```java
package test.tcptest;

import org.junit.Test;

import java.io.*;
import java.net.InetAddress;
import java.net.ServerSocket;
import java.net.Socket;

//客户端发送文件给服务端，服务端将文件保存在本地。
public class TCPTest2 {
    //客户端
    @Test
    public void client() {
        Socket socket = null;
        BufferedInputStream bis = null;
        OutputStream os = null;
        try {
            InetAddress inet = InetAddress.getByName("192.168.0.112");

            socket = new Socket(inet,8899);

            bis = new BufferedInputStream(new FileInputStream("src/test/tcptest/1.jpg"));

            os = socket.getOutputStream();

            byte[] buffer = new byte[1024];
            int len;
            while ((len = bis.read(buffer)) != -1) {
                os.write(buffer,0,len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (os != null) {
                try {
                    os.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (bis != null) {
                try {
                    bis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    //服务端
    @Test
    public void server() {
        ServerSocket ss = null;
        Socket socket = null;
        BufferedOutputStream bos = null;
        try {
            ss = new ServerSocket(8899);
            socket = ss.accept();

            InputStream is = socket.getInputStream();

            bos = new BufferedOutputStream(new FileOutputStream("src/test/tcptest/2.jpg"));

            byte[] buffer = new byte[1024];
            int len;
            while ((len = is.read(buffer)) != -1) {
                bos.write(buffer,0,len);
            }

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (bos != null) {
                try {
                    bos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (ss != null) {
                try {
                    ss.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```





## TCP3

从客户端发送文件给服务端，服务端保存到本地

并返回“发送成功”给客户端，并关闭相应的连接



```java
package test.tcptest;

import org.junit.Test;

import java.io.*;
import java.net.InetAddress;
import java.net.ServerSocket;
import java.net.Socket;

//    从客户端发送文件给服务端，服务端保存到本地。并返回“发送成功”给
//            客户端。并关闭相应的连接。
public class TCPTest3 {
    //客户端
    @Test
    public void client() {
        Socket socket = null;
        BufferedInputStream bos = null;
        OutputStream os = null;
        InputStream is = null;
        try {
            //1.创建 socket 对象，指明 IP 和 端口号
            InetAddress inet = InetAddress.getByName("192.168.0.112");
            socket = new Socket(inet,8899);

            //2.输出数据
            //获取输出流
            bos = new BufferedInputStream(new FileInputStream("src/test/tcptest/1.jpg"));
            os = socket.getOutputStream();

            byte[] buffer = new byte[1024];
            int len;
            while ((len = bos.read(buffer)) != -1) {
                os.write(buffer,0,len);
            }
            socket.shutdownOutput();

            //3.接受数据
            //获取输入流
            is = socket.getInputStream();
            ByteArrayOutputStream baos = new ByteArrayOutputStream();
            byte[] buffer1 = new byte[1024];
            int len1;
            while ((len1 = is.read(buffer1)) != -1) {
                baos.write(buffer1,0,len1);
            }
            System.out.println(baos.toString());
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            //4.关闭流
            if (os != null) {
                try {
                    os.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (bos != null) {
                try {
                    bos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (is != null) {
                try {
                    is.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    //服务端
    @Test
    public void server() {
        ServerSocket ss = null;
        Socket socket = null;
        InputStream is = null;
        BufferedOutputStream bos = null;
        OutputStream os = null;
        try {
            //1.创建 服务端的 ServerSocket，指明端口号
            ss = new ServerSocket(8899);

            //2.接受来自客户端的 socket
            socket = ss.accept();

            //3.输入数据
            //获取输入流
            is = socket.getInputStream();
            bos = new BufferedOutputStream(new FileOutputStream("src/test/tcptest/3.jpg"));
            byte[] buffer = new byte[1024];
            int len;
            while ((len = is.read(buffer)) != -1) {
                bos.write(buffer,0,len);
            }
            socket.shutdownInput();
            //4.输出数据
            //获取输出流
            os = socket.getOutputStream();
            os.write("发送成功！".getBytes());
            socket.shutdownOutput();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            //5.关闭流
            if (bos != null) {
                try {
                    bos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (os != null) {
                try {
                    os.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (is != null) {
                try {
                    is.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (ss != null) {
                try {
                    ss.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```
