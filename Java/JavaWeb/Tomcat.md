# Tomcat



## JavaWeb概念

**a)什么是 JavaWeb** 

JavaWeb 是指，所有通过 Java 语言编写可以通过浏览器访问的程序的总称，叫 JavaWeb。 JavaWeb 是基于请求和响应来开发的

**b)什么是请求** 

请求是指客户端给服务器发送数据，叫请求 Request

**c)什么是响应** 

响应是指服务器给客户端回传数据，叫响应 Response

**d)请求和响应的关系** 

请求和响应是成对出现的，有请求就有响应



## TOMcat 的使用

### 目录介绍

1. bin 存放 Tomcat 的可执行程序

2. conf 存放 Tomcat 的配置文件

3. lib 存放 Tomcat 的 jar 包

4. logs 存放 Tomcat 运行时输出的日志信息

5. temp 存放 Tomcat 运行时产生的临时数据

6. webapps 存放部署的 Web 工程

7. work 是Tomcat 工作时的目录，用来存放 Tomcat 运行时 jsp 翻译为 Servlet 的源码，

   ​	和 Session 钝化（序列化）的目录
   
   
   

### 启动

#### 方式一

找到 Tomcat 目录下的 bin 目录下的 startup.bat 文件，双击，就可以启动 Tomcat 服务器

如何测试 Tomcat 服务器启动成功？？？ 

打开浏览器，在浏览器地址栏中输入以下地址测试：

1、http://localhost:8080 

2、http://127.0.0.1:8080 

3、http://真实 ip:808

#### 失败原因

常见的启动失败的情况有，双击 startup.bat 文件，就会出现一个小黑窗口一闪而来

这个时候，失败的原因基本上都是因为没有配置好 JAVA_HOME 环境变量



常见的 JAVA_HOME 配置错误有以下几种情况：

 一：JAVA_HOME 必须全大写

二：JAVA_HOME 中间必须是下划线，不是减号- 

三：JAVA_HOME 配置的路径只需要配置到 jdk 的安装目录即可 **不需要带上 bin 目录**

#### 方式二

1、打开命令行

2、cd 到 你的 Tomcat 的 bin 目录下

3、敲入启动命令： catalina run

### 关闭

1、点击 tomcat 服务器窗口的 x 关闭按钮

2、把 Tomcat 服务器窗口置为当前窗口，然后按快捷键 Ctrl+C

3、找到 Tomcat 的 bin 目录下的 shutdown.bat 双击，就可以停止 Tomcat 服务器



### 如何修改 Tomcat 的端口

找到 Tomcat 目录下的 conf 目录，找到 server.xml 配置文件

找到 Connector标签,修改port属性为你需要的端口号端口号范图:1-65535

修改完端口号,一定要重启 Toma服务器才能生效



### 如何部暑 web 工程到 Tomcat 中

#### 第一种部署方法：

​	只需要把 web 工程的目录拷贝到 Tomcat 的 webapps 目录下 即可

1、在 webapps 目录下创建一个 book 工程

2、把上午做的书城第一阶段的内容拷贝到里面

3、如何访问 Tomcat 下的 web 工程

​	只需要在浏览器中输入访问地址格式如下： http://ip:port/工程名/目录下/文件名

#### 第二种部署方法：

找到 Tomcat 下的 conf 目录\Catalina\localhost\ 下，创建如下的配置文件 abc.xml

```xml
<Context path="/abc" docBase="E:\book" />
<!-- Context 表示一个工程上下文
			path 表示工程的访问路径:/abc
			docBase 表示你的工程目录在哪里
-->
```



### 手托 html 页面到浏览器和在浏览器中输入 http://ip:端 口号/工程名/访问的区别

![image-20210728161731571](D:\Coding\Java\JavaWeb\WebImage\TomcatImgae\1.png)



![image-20210728161841557](D:\Coding\Java\JavaWeb\WebImage\TomcatImgae\2.png)

- war模式：将WEB工程以包的形式上传到服务器 ；
- war exploded模式：将WEB工程以当前文件夹的位置关系上传到服务器；









   