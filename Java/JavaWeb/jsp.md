# jsp

## 1.什么是 jsp，它有什么用?

jsp 的全换是 java server pages，Java 的服务器页面

jsp 的主要作用是代替 Servlet 程序回传 html 页面的数据

因为 Servlet 程序回传 html 页面数据是一件非常繁锁的事情，开发成本和维护成本都极高

JSP 文件基本结构
- 模板元素
  - 静态 HTML/XML内容
- JSP 元素
  - 指令元素
  - 脚本元素（声明、表达式、程序片）
  - 动作元素
- 注释

### Servlet 回传 html 页面数据的代码：

```java
public class PringHtml extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException,
    IOException {
        // 通过响应的回传流回传 html 页面数据
        resp.setContentType("text/html; charset=UTF-8");
        
        PrintWriter writer = resp.getWriter();
        
        writer.write("<!DOCTYPE html>\r\n");
        writer.write(" <html lang=\"en\">\r\n");
        writer.write(" <head>\r\n");
        writer.write(" <meta charset=\"UTF-8\">\r\n");
        writer.write(" <title>Title</title>\r\n");
        writer.write(" </head>\r\n");
        writer.write(" <body>\r\n");
        writer.write(" 这是 html 页面数据 \r\n");
        writer.write(" </body>\r\n");
        writer.write("</html>\r\n");
        writer.write("\r\n");
    }
}

```



### jsp 回传一个简单 html 页面的代码：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
	<title>Title</title>
</head>
<body>
	这是 html 页面数据
</body>
</html>
```



### jsp 如何访问： 

jsp 页面和 html 页面一样，都是存放在 web 目录下。访问也跟访问 html 页面一样

比如： 在 web 目录下有如下的文件： 

web 目录

a.html 页面 访问地址是 ==>>> http://ip:port/工程路径/a.html

b.jsp 页面 访问地址是 ==>>> http://ip:port/工程路径/b.jsp



## 2.jsp 的本质是什么

**jsp 页面本质上是一个 Servlet 程序**

当我们第一次访问 jsp 页面的时候，Tomcat 服务器会帮我们把 jsp 页面翻译成为一个 java 源文件，并且对它进行编译成 为.class 字节码程序

jsp 翻译出来的 java 类，它间接了继 承了 HttpServlet 类，也就是说，翻译出来的是一个 Servlet 程序



> 大家也可以去观察翻译出来的 Servlet 程序的源代码，不难发现其底层实现，
>
> 也是通过输出流，把 html 页面数据回传给客户端



## 3.jsp 的三种语法

### a)jsp 头部的 page 指令 jsp 的 page 指令

jsp 的 page 指令可以修改 jsp 页面中一些重要的属性，或者行为

<%@ page contentType="text/html;charset=UTF-8"  language="java" %>



| 属性         | 作用                                                         |
| ------------ | ------------------------------------------------------------ |
| language     | 表示jsp 翻译后是什么语言文件，暂时只支持 java                |
| contentType  | 表示 jsp 返回的数据类型是什么，也是源码中 response.setContentType()参数值 |
| pageEncoding | 表示当前 jsp 页面文件本身的字符集                            |
| import       | 跟 java 源代码中一样。用于导包，导类                         |
| autoFlush    | 设置当 out 输出流缓冲区满了之后，是否自动刷新冲级区。默认值是 true |
| buffer       | 设置 out 缓冲区的大小。默认是 8kb                            |
| errorPage    | 设置当 jsp 页面运行时出错，自动跳转去的错误页面路径          |
| isErrorPage  | 设置当前 jsp 页面是否是错误信息页面。默认是 false。如果是 true 可以 获取异常信息 |
| session      | 设置访问当前 jsp 页面，是否会创建 HttpSession 对象。默认是 true |
| extends      | 设置 jsp 翻译出来的 java 类默认继承类                        |

> out 缓冲区满了，不能自动刷新就会报错



### b)jsp 中的常用脚本

#### i. 声明脚本(极少使用)

声明脚本的格式是： <%! 声明 java 代码 %> 

作用：可以给 jsp 翻译出来的 java 类定义属性和方法甚至是静态代码块、内部类等



#### ii. 表达式脚本（常用）

表达式脚本的格式是：<%=表达式%> 表达式脚本的作用是：在jsp 页面上输出数据

表达式脚本的特点： 

1、所有的表达式脚本都会被翻译到 jspService() 方法中 

2、表达式脚本都会被翻译成为 out.print()输出到页面上 

3、由于表达式脚本翻译的内容都在 jspService() 方法中，所以 jspService()方法中的对象都可以直接使用

4、表达式脚本中的表达式不能以分号结束



#### iii. 代码脚本

代码脚本的格式是： 

<% 

​	java 语句 

%>

代码脚本的作用是：可以在 jsp 页面中，编写我们自己需要的功能（写的是 java 语句）

代码脚本的特点是：

1、代码脚本翻译之后都在 jspService 方法中

2、代码脚本由于翻译到 jspService()方法中，所以在 jspService()方法中的现有对象都可以直接使用

3、还可以由多个代码脚本块组合完成一个完整的 java 语句

4、代码脚本还可以和表达式脚本一起组合使用，在 jsp 页面上输出数据



### c)jsp 中的三种注释

#### i. html 注释

```jsp
<!-- 这是 html 注释 -->
```

html 注释会被翻译到 java 源代码中，在 jspService 方法里，以 out.writer 输出到客户端



#### ii. java 注释

```jsp
<%
    // 单行 java 注释
    /* 多行 java 注释 */
%>
```

java 注释会被翻译到 java 源代码中



#### iii. jsp 注释

```jsp
<%-- 这是 jsp 注释 --%>
```

jsp 注释可以注掉，jsp 页面中所有代码



## 4.jsp 九大内置对象

jsp 中的内置对象，是指 Tomcat 在翻译 jsp 页面成为 Servlet 源代码后，内部提供的九大对象，叫内置对象



| Object      | Description         |
| ----------- | ------------------- |
| request     | 请求对象            |
| response    | 响应对象            |
| pageContext | jsp 的上下文对象    |
| session     | 会话对象            |
| application | ServletContext 对象 |
| config      | ServletConfig 对象  |
| out         | jsp 输出流对象      |
| page        | 指向当前 jsp 的对象 |
| exception   | 异常对象            |

## 5.jsp 四大域对象



四个域对象分别是：

| Object      | Class              | Description                                                |
| ----------- | ------------------ | ---------------------------------------------------------- |
| pageContext | PageContextImpl    | 当前 jsp 页面范围内有效                                    |
| request     | HttpServletRequest | 一次请求内有效                                             |
| session     | HttpSession        | 一个会话范围内有效（打开浏览器访问服务器，直到关闭服务器） |
| application | ServletContext     | 整个 web 工程范围内都有效（只要 web 工程不停止，数据都在） |



域对象是可以像 Map 一样存取数据的对象，四个域对象功能一样，不同的是它们对数据的存取范围

虽然四个域对象都可以存取数据，在使用上它们是有优先顺序的，四个域在使用的时候，

优先顺序分别是，他们从小到大的范围的顺序：

​	pageContext ==>>> request ==>>> session ==>>> application



scope.jsp 页面：

```jsp
<body>
    <h1>scope.jsp 页面</h1>
    <%
        // 往四个域中都分别保存了数据
        pageContext.setAttribute("key", "pageContext");
        request.setAttribute("key", "request");
        session.setAttribute("key", "session");
        application.setAttribute("key", "application");
    %>
    pageContext 域是否有值：<%=pageContext.getAttribute("key")%> <br>
    request 域是否有值：<%=request.getAttribute("key")%> <br>
    session 域是否有值：<%=session.getAttribute("key")%> <br>
    application 域是否有值：<%=application.getAttribute("key")%> <br>
    <%
        request.getRequestDispatcher("/scope2.jsp").forward(request,response);
    %>
</body>
```



scope2.jsp 页面：

```jsp
<body>
    <h1>scope2.jsp 页面</h1>
    pageContext 域是否有值：<%=pageContext.getAttribute("key")%> <br>
    request 域是否有值：<%=request.getAttribute("key")%> <br>
    session 域是否有值：<%=session.getAttribute("key")%> <br>
    application 域是否有值：<%=application.getAttribute("key")%> <br>
</body>

```



## 6.jsp 中的 out 输出和 response.getWriter 输出的区别

response 中表示响应，我们经常用于设置返回给客户端的内容（输出） 

out 也是给用户做输出使用的



![image-20210806160550654](D:\Coding\Java\JavaWeb\WebImage\BookCityImage\5.png)

由于 jsp 翻译之后，底层源代码都是使用 out 来进行输出，所以一般情况下。我们在 jsp 页面中统一使用 out 来进行输出。避 免打乱页面输出内容的顺序

out.write() 输出字符串没有问题

out.print() 输出任意数据都没有问题（都转换成为字符串后调用的 write 输出）

>  **深入源码，浅出结论：在 jsp 页面中，可以统一使用 out.print()来进行输出**



## 7.jsp 的常用标签

### a)jsp 静态包含

```jsp
<%--
    <%@ include file=""%> 就是静态包含
    file 属性指定你要包含的 jsp 页面的路径
    地址中第一个斜杠 / 表示为 http://ip:port/工程路径/ 映射到代码的 web 目录
    静态包含的特点：
        1、静态包含不会翻译被包含的 jsp 页面。
        2、静态包含其实是把被包含的 jsp 页面的代码拷贝到包含的位置执行输出。
--%>
<%@ include file="/include/footer.jsp"%>
```



### b)jsp 动态包含（推荐）

```jsp
<%--
    <jsp:include page=""></jsp:include> 这是动态包含
    page 属性是指定你要包含的 jsp 页面的路径
    动态包含也可以像静态包含一样。把被包含的内容执行输出到包含位置
    动态包含的特点：
        1、动态包含会把包含的 jsp 页面也翻译成为 java 代码
        2、动态包含底层代码使用如下代码去调用被包含的 jsp 页面执行输出。
        JspRuntimeLibrary.include(request, response, "/include/footer.jsp", out, false);
        3、动态包含，还可以传递参数
--%>
<jsp:include page="/include/footer.jsp">
<jsp:param name="username" value="bbj"/>
<jsp:param name="password" value="root"/>
</jsp:include>
```



#### 动态包含的底层原理

![image-20210806161014791](D:\Coding\Java\JavaWeb\WebImage\BookCityImage\6.png)



### c)jsp 标签-转发

示例说明：

```jsp
<%--
	是请求转发标签，它的功能就是请求转发 page 属性设置请求转发的路径
--%>
<jsp:forward page="/scope2.jsp"></jsp:forward>
```



## 8、jsp 的练习

### 练习一：在 jsp 页面中输出九九乘法表

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <style type="text/css">
        table{
        width: 650px;
        }
    </style>
</head>
<body>
    <%-- 练习一：在 jsp 页面中输出九九乘法口诀表 --%>
    <h1 align="center">九九乘法口诀表</h1>
    <table align="center">
        <%-- 外层循环遍历行 --%>
        <% for (int i = 1; i <= 9; i++) { %>
            <tr>
            <%-- 内层循环遍历单元格 --%>
            <% for (int j = 1; j <= i ; j++) { %>
                <td><%=j + "x" + i + "=" + (i*j)%></td>
                <% } %>
            </tr>
        <% } %>
    </table>
</body>
</html>
```



### 练习二：jsp 输出一个表格，里面有 10 个学生信息

![image-20210806161652755](D:\Coding\Java\JavaWeb\WebImage\BookCityImage\7.png)



Student 类：

```java
public class Student {
    private Integer id;
    private String name;
    private Integer age;
    private String phone;
```



SearchStudentServlet 程序：

```java
public class SearchStudentServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException,
    IOException {
        // 获取请求的参数
        // 发 sql 语句查询学生的信息
        // 使用 for 循环生成查询到的数据做模拟
        List<Student> studentList = new ArrayList<Student>();
        for (int i = 0; i < 10; i++) {
            int t = i + 1;
            studentList.add(new Student(t,"name"+t, 18+t,"phone"+t));
        }
        // 保存查询到的结果（学生信息）到 request 域中
        req.setAttribute("stuList", studentList);
        // 请求转发到 showStudent.jsp 页面
        req.getRequestDispatcher("/test/showStudent.jsp").forward(req,resp);
    }
}
```



showStudent.jsp 页面：

```jsp
<%@ page import="java.util.List" %>
<%@ page import="com.atguigu.pojo.Student" %>
<%@ page import="java.util.ArrayList" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <style>
        table{
        border: 1px blue solid;
        width: 600px;
        border-collapse: collapse;
        }
        td,th{
        border: 1px blue solid;
        }
    </style>
</head>
<body>
<%--练习二：jsp 输出一个表格，里面有 10 个学生信息。--%>
    <%
    List<Student> studentList = (List<Student>) request.getAttribute("stuList");
    %>
    <table>
        <tr>
            <td>编号</td>
            <td>姓名</td>
            <td>年龄</td>
            <td>电话</td>
            <td>操作</td>
        </tr>
    <% for (Student student : studentList) { %>
        <tr>
            <td><%=student.getId()%></td>
            <td><%=student.getName()%></td>
            <td><%=student.getAge()%></td>
            <td><%=student.getPhone()%></td>
            <td>删除、修改</td>
        </tr>
    <% } %>
    </table>
</body>
</html>
```



## 9、什么是 Listener 监听器？

1、Listener 监听器它是 JavaWeb 的三大组件之一

​	JavaWeb 的三大组件分别是：Servlet 程序、Filter 过滤器、Listener 监 听器

2、Listener 它是 JavaEE 的规范，就是接口

3、监听器的作用是，监听某种事物的变化。然后通过回调函数，反馈给客户（程序）去做一些相应的处理



### ServletContextListener 监听器

ServletContextListener 它可以监听 ServletContext 对象的创建和销毁

ServletContext 对象在 web 工程启动的时候创建，在 web 工程停止的时候销毁

监听到创建和销毁之后都会分别调用 ServletContextListener 监听器的方法反馈



两个方法分别是：

```java
public interface ServletContextListener extends EventListener {
    /**
    * 在 ServletContext 对象创建之后马上调用，做初始化
    */
    public void contextInitialized(ServletContextEvent sce);
    /**
    * 在 ServletContext 对象销毁之后调用
    */
    public void contextDestroyed(ServletContextEvent sce);
}
```



#### 如何使用 ServletContextListener 监听器监听 ServletContext 对象

使用步骤如下：

1、编写一个类去实现 ServletContextListener

2、实现其两个回调方法

3、到 web.xml 中去配置监听



监听器实现类：

```java
public class MyServletContextListenerImpl implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
    	System.out.println("ServletContext 对象被创建了");
    }
    @Override
    public void contextDestroyed(ServletContextEvent sce) {
    	System.out.println("ServletContext 对象被销毁了");
    }
}
```



web.xml 中的配置：

```xml
<!--配置监听器-->
<listener>
	<listener-class>com.atguigu.listener.MyServletContextListenerImpl</listener-class>
</listener>
```

## 总结

1. **request 对象**
   1. 读取请求数据
   2. 是域对象，存取数据
   3. 页面转发
   4. 获取 cookie
   5. 设置字符编码
   6. 获取其他信息
   7. 作用域：当前请求页面以及转发的页面

2. **response 对象**
   1. 页面重定向
   2. 设置响应头（3 秒跳转，刷新）
   3. 发送状态码，错误码
   4. 发送 cookie 到客户端浏览器
   5. 向客户端输出数据

3. **out 对象**
   1. 向浏览器输出内容
   2. 管理缓冲区 (8 kb) clear() close()

4. **session 对象**
   1. 会话中，各个页面之间共享数据，同一应用程序，不同的客户端创建不同的 session
   2.  域对象，不同客户端创建不同的 session
   3. 域对象，存取数据
   4. 判断当前会话是否为新会话
   5. 生命周期：打开浏览器 -> 关闭浏览器 默认30分钟 从某一刻停止请求开始计算

5. **application 对象**
    1. 域对象，存储数据
    2. 统一应用程序中，不同客户共享同一个 application

6. **pageContext 对象**
   1. 访问本页面中其他所有对象
   2. 域对象，存取数据，仅限当前页面
   3. 页面重定向，包含文件
   4. 作用域：当前页面

7. **page 对象**
   1. 代表 Object，可以用 this 代替
   2. 调用转移后的 Servlet 类定义的方法

8. **config 对象**
   1. 获取服务器的配置信息

9. **exception 对象**
   1.  jsp 页面中使用 exception，需要 page 指令中包含 isErrorPage="true"

## jsp 引入 bootstrap 样式的玄学问题
去掉这一行
```
<!DOCTYPE html>
```