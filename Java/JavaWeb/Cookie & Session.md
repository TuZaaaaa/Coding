# Cookie & Session

## Cookie
### a)什么是 Cookie?

1. Cookie 是服务器通知客户端保存键值对的一种技术
2. 客户端有了 Cookie 后，每次请求都发送给服务器
3. 每个 Cookie 的大小不能超过 4kb

### b)如何创建 Cookie
Servlet:
```java
    protected void createCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException,
    IOException {
        //1 创建 Cookie 对象
        Cookie cookie = new Cookie("key4", "value4");
        //2 通知客户端保存 Cookie
        resp.addCookie(cookie);
        //1 创建 Cookie 对象
        Cookie cookie1 = new Cookie("key5", "value5");
        //2 通知客户端保存 Cookie
        resp.addCookie(cookie1);
        resp.getWriter().write("Cookie 创建成功");
    }
```

### c)服务器如何获取 Cookie
服务器获取客户端的 Cookie 只需要一行代码：
```java
req.getCookies():Cookie[]
```

Cookie 工具类：
```java
    public class CookieUtils {
    /**
    * 查找指定名称的 Cookie 对象
    * @param name
    * @param cookies
    * @return
    */
        public static Cookie findCookie(String name , Cookie[] cookies){
            if (name == null || cookies == null || cookies.length == 0) {
                return null;
            }
            for (Cookie cookie : cookies) {
                if (name.equals(cookie.getName())) {
                    return cookie;
                }
            }
            return null;
        }
    }
```

### d)Cookie 值的修改
方案一：
1、先创建一个要修改的同名（指的就是 key）的 Cookie 对象
2、在构造器，同时赋于新的 Cookie 值。
3、调用 response.addCookie( Cookie

方案二（更优）：
1、先查找到需要修改的 Cookie 对象
2、调用 setValue()方法赋于新的 Cookie 值。
3、调用 response.addCookie()通知客户端保存修改

### e)浏览器查看 Cookie
### f)Cookie 生命周期
> Cookie 的生命控制指的是如何管理 Cookie 什么时候被销毁（删除） 

setMaxAge()
正数，表示在指定的秒数后过期
负数，表示浏览器一关，Cookie 就会被删除（默认值是-1）
零，表示马上删除 Cookie

### g)Cookie 有效路径 Path 的设置
> Cookie 的 path 属性可以有效的过滤哪些 Cookie 可以发送给服务器。哪些不发。
> path 属性是通过请求的地址来进行有效的过滤。

CookieA path=/工程路径
CookieB path=/工程路径/abc
请求地址如下：
http://ip:port/工程路径/a.html
CookieA 发送
CookieB 不发送
http://ip:port/工程路径/abc/a.html
CookieA 发送
CookieB 发送

### h) Cookie 练习---免输入用户名登录
思路：
> 第一次访问（浏览器 > Tomcat 服务器） > 输入用户名 > 返回登录页面（Tomcat 服务器 > 浏览器）
> 提交 > 进入 LoginServlet 程序
> 1. 获取用户名和密码
> 2. 判断用户名和密码是否正确
>   - 正确 允许登录
>       - 把用户名保存为 Cookie 发送给服务器
>   - 错误 不允许登录
> Set-Cookie:username=用户名 浏览器有了用户名的 Cookie 信息

> 第二次访问，登录，将 Cookie 信息也发送给服务器

## Session
### a) 什么是 Session?
1. Session 就一个接口（HttpSession）。
2. Session 就是会话。它是用来维护一个客户端和服务器之间关联的一种技术。
3. 每个客户端都有自己的一个 Session 会话。
4. Session 会话中，我们经常用来保存用户登录之后的信息。

### b)如何创建 Session 和获取(id 号,是否为新)
如何创建和获取 Session ，它们的 API 是一样的
```java
request.getSession()
```
第一次调用是：创建 Session 会话
之后调用都是：获取前面创建好的 Session 会话对象。
- isNew(); 判断到底是不是刚创建出来的（新的）
- true 表示刚创建
- false 表示获取之前创建
每个会话都有一个身份证号。也就是 ID 值。而且这个 ID 是唯一的。
getId() 得到 Session 的会话 id 值

### c)Session 域数据的存取
```java
    req.getSession().setAttribute("key1", "value
```

```java
    Object attribute = req.getSession().getAttribute("key1");
```

### d) Session 生命周期控制
设置 Session 的超时时间（以秒为单位），超过指定的时长，Session就会被销毁
``` java
public void setMaxInactiveInterval(int interval)
```
值为正数的时候，设定 Session 的超时时长。
负数表示永不超时（极少使用）
获取 Session 的超时时间：
``` java
public int getMaxInactiveInterval()
```
销毁当前 Session：
```java
public void invalidate() 
```

Session 默认的超时时间长为 30 分钟。
因为在 Tomcat 服务器的配置文件 web.xml中默认有以下的配置，它就表示配置了当前 Tomcat 服务器下所有的 Session
超时配置默认时长为：30 分钟。

```xml
<session-config>
<session-timeout>30</session-timeout>
</session-config>
```

如果说。你希望你的 web 工程，默认的 Session 的超时时长为其他时长，你可以在你自己的 web.xml 配置文件中做以上相同的配置。就可以修改你的 web 工程下所有 Session 的默认超时时间
```xml
    <!--表示当前 web 工程。创建出来 的所有 Session 默认是 20 分钟 超时时长-->
    <session-config>
    <session-timeout>20</session-timeout>
    </session-config>
```
> session 的超时是指，客户端两次请求的最大间隔时长

### e)浏览器和 Session 之间关联的技术内幕
> Session 技术，底层其实是基于 Cookie 技术实现的