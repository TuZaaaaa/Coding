# BookCityProject

## 一.JavaEE 项目的三层架构

![image-20210805210311966](D:\Coding\Java\JavaWeb\WebImage\BookCityImage/1.png)

> 分层的目的是为了解耦。解耦就是为了降低代码的耦合度。方便项目后期的维护和升级



| 结构           | 路径                                | 接口及实现类       |
| -------------- | ----------------------------------- | ------------------ |
| web 层         | com.atguigu.web/servlet/controller  |                    |
| service 层     | com.atguigu.service                 | Service 接口包     |
|                | com.atguigu.service.impl            | Service 接口实现类 |
| dao 持久层     | com.atguigu.dao                     | Dao 接口包         |
|                | com.atguigu.dao.impl                | Dao 接口实现类     |
| 实体 bean 对象 | com.atguigu.pojo/entity/domain/bean | JavaBean 类        |
| 测试包         | com.atguigu.test/junit              |                    |
| 工具类         | com.atguigu.utils                   |                    |

搭建书城项目开发环境：

![image-20210805211658226](D:\Coding\Java\JavaWeb\WebImage\BookCityImage\2.png)



### 1、先创建书城需要的数据库和表

```sql
drop database if exists book;
create database book;
use book;
create table t_user(
	`id` int primary key auto_increment,
	`username` varchar(20) not null unique,
	`password` varchar(32) not null,
	`email` varchar(200)
);
insert into t_user(`username`,`password`,`email`) values('admin','admin','admin@atguigu.com');
select * from t_user
```



### 2、编写数据库表对应的 JavaBean 对象

```java
public class User {
    private Integer id;//因为进行了 == null 的判断，方便操作
    private String username;
    private String password;
    private String email;
```



### 3、编写工具类 JdbcUtils

#### 3.1、导入需要的 jar 包（数据库和连接池需要）:

- druid-1.1.9.jar

- mysql-connector-java-5.1.7-bin.jar 

以下是测试需要： 

- hamcrest-core-1.3.jar
- junit-4.12.jar



#### 3.2、在 src 源码目录下编写 jdbc.properties

```xml
username=root
password=root
url=jdbc:mysql://localhost:3306/book
driverClassName=com.mysql.jdbc.Driver
initialSize=5
maxActive=10
```



#### 3.3、编写 JdbcUtils 工具类

```java
public class JdbcUtils {
    
	private static DruidDataSource dataSource;
    
static {
    try {
        Properties properties = new Properties();
        // 读取 jdbc.properties 属性配置文件
        InputStream inputStream =
        JdbcUtils.class.getClassLoader().getResourceAsStream("jdbc.properties");
        // 从流中加载数据
        properties.load(inputStream);
        // 创建 数据库连接 池
        dataSource = (DruidDataSource) DruidDataSourceFactory.createDataSource(properties);
    } catch (Exception e) {
        e.printStackTrace();
    }
}
    
    /**
    * 获取数据库连接池中的连接
    * @return 如果返回 null,说明获取连接失败<br/>有值就是获取连接成功
    */
    public static Connection getConnection(){
        
        Connection conn = null;
        
        try {
            conn = dataSource.getConnection();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return conn;
    }
    
    /**
    * 关闭连接，放回数据库连接池
    * @param conn
    */
    public static void close(Connection conn){
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```



#### 3.4、JdbcUtils 测试

```java
public class JdbcUtilsTest {

    @Test
    public void testJdbcUtils(){
        for (int i = 0; i < 100; i++){
            Connection connection = JdbcUtils.getConnection();
            System.out.println(connection);
            JdbcUtils.close(connection);
        }
    }
}
```



### 4、编写 BaseDao

#### 4.1、导入 DBUtils 的 jar 包

commons-dbutils-1.3.jar



#### 4.2、编写 BaseDao

```java
public abstract class BaseDao {

    //使用 DbUtils 操作数据库
    private QueryRunner queryRunner = new QueryRunner();
        /**
        * update() 方法用来执行：Insert\Update\Delete 语句
        *
        * @return 如果返回-1,说明执行失败<br/>返回其他表示影响的行数
        */
        public int update(String sql, Object... args) {
            Connection connection = JdbcUtils.getConnection();
            try {
            	return queryRunner.update(connection, sql, args);
            } catch (SQLException e) {
            	e.printStackTrace();
            } finally {
            	JdbcUtils.close(connection);
            }
            return -1;
    }
    
    /**
    * 查询返回一个 javaBean 的 sql 语句
    *
    * @param type 返回的对象类型
    * @param sql 执行的 sql 语句
    * @param args sql 对应的参数值
    * @param <T> 返回的类型的泛型
    * @return
    */
    public <T> T queryForOne(Class<T> type, String sql, Object... args) {
        Connection con = JdbcUtils.getConnection();
        try {
        	return queryRunner.query(con, sql, new BeanHandler<T>(type), args);
        } catch (SQLException e) {
        	e.printStackTrace();
        } finally {
        	JdbcUtils.close(con);
        }
        return null;
    }
    
    /**
    * 查询返回多个 javaBean 的 sql 语句
    *
    * @param type 返回的对象类型
    * @param sql 执行的 sql 语句
    * @param args sql 对应的参数值
    * @param <T> 返回的类型的泛型
    * @return
    */
    public <T> List<T> queryForList(Class<T> type, String sql, Object... args) {
        Connection con = JdbcUtils.getConnection();
        try {
        	return queryRunner.query(con, sql, new BeanListHandler<T>(type), args);
        } catch (SQLException e) {
        	e.printStackTrace();
        } finally {
        	JdbcUtils.close(con);
        }
        return null;
    }
    
    /**
    * 执行返回一行一列的 sql 语句
    * @param sql 执行的 sql 语句
    * @param args sql 对应的参数值
    * @return
    */
    public Object queryForSingleValue(String sql, Object... args){
        Connection conn = JdbcUtils.getConnection();
        try {
        	return queryRunner.query(conn, sql, new ScalarHandler(), args);
        } catch (Exception e) {
       		e.printStackTrace();
        } finally {
        	JdbcUtils.close(conn);
        }
        return null;
    }
}
```



### 5、编写 UserDao 和测试

UserDao 接口：

```java
public interface UserDao {
    /**
    * 根据用户名查询用户信息
    * @param username 用户名
    * @return 如果返回 null,说明没有这个用户。反之亦然
    */
    public User queryUserByUsername(String username);
    
    /**
    * 根据 用户名和密码查询用户信息
    * @param username
    * @param password
    * @return 如果返回 null,说明用户名或密码错误,反之亦然
    */
    public User queryUserByUsernameAndPassword(String username,String password);
    
    /**
    * 保存用户信息
    * @param user
    * @return 返回-1 表示操作失败，其他是 sql 语句影响的行数
    */
    public int saveUser(User user);
    }
```



UserDaoImpl 实现类：

```java
public class UserDaoImpl extends BaseDao implements UserDao {
    @Override
    public User queryUserByUsername(String username) {
        String sql = "select `id`,`username`,`password`,`email` from t_user where username = ?";
        return queryForOne(User.class, sql, username);
    }
    @Override
    public User queryUserByUsernameAndPassword(String username, String password) {
        String sql = "select `id`,`username`,`password`,`email` from t_user where username = ? and
        password = ?";
        return queryForOne(User.class, sql, username,password);
    }
    @Override
    public int saveUser(User user) {
        String sql = "insert into t_user(`username`,`password`,`email`) values(?,?,?)";
        return update(sql, user.getUsername(),user.getPassword(),user.getEmail());
	}
}
```



UserDao 测试：

```java
public class UserDaoTest {
    
    UserDao userDao = new UserDaoImpl();
    
    @Test
    public void queryUserByUsername() {
        if (userDao.queryUserByUsername("admin1234") == null ){
        	System.out.println("用户名可用！");
        } else {
      	  System.out.println("用户名已存在！");
        }
    }
    
    @Test
    public void queryUserByUsernameAndPassword() {
        if ( userDao.queryUserByUsernameAndPassword("admin","admin1234") == null) {
      		System.out.println("用户名或密码错误，登录失败");
        } else {
      		System.out.println("查询成功");
        }
    }
    
    @Test
    public void saveUser() {
   		System.out.println( userDao.saveUser(new User(null,"wzg168", "123456", "wzg168@qq.com")) );
    }
}
```



### 6、编写 UserService 和测试

UserService 接口：

```java
public interface UserService {
    /**
    * 注册用户
    * @param user
    */
    public void registUser(User user);
    /**
    * 登录
    * @param user
    * @return 如果返回 null，说明登录失败，返回有值，是登录成功
    */
    public User login(User user);
    /**
    * 检查 用户名是否可用
    * @param username
    * @return 返回 true 表示用户名已存在，返回 false 表示用户名可用
    */
    public boolean existsUsername(String username);
}
```

UserServiceImpl 实现类：

```java
public class UserServiceTest {
    
    UserService userService = new UserServiceImpl();
    
    @Test
    public void registUser() {
		userService.registUser(new User(null, "bbj168", "666666", "bbj168@qq.com"));
		userService.registUser(new User(null, "abc168", "666666", "abc168@qq.com"))
    }
    @Test
    public void login() {
    	System.out.println( userService.login(new User(null, "wzg168", "123456", null)) );
    }
    @Test
    public void existsUsername() {
    	if (userService.existsUsername("wzg16888")) {
    		System.out.println("用户名已存在！");
        } else {
    		System.out.println("用户名可用！");
    	}
    }
}
```



UserService 测试：

```java
public class UserServiceTest {
    
    UserService userService = new UserServiceImpl();
    
    @Test
    public void registUser() {
        userService.registUser(new User(null, "bbj168", "666666", "bbj168@qq.com"));
        userService.registUser(new User(null, "abc168", "666666", "abc168@qq.com"));
    }
    @Test
    public void login() {
        System.out.println( userService.login(new User(null, "wzg168", "123456", null)) );
    }
    @Test
    public void existsUsername() {
        if (userService.existsUsername("wzg16888")) {
       		System.out.println("用户名已存在！");
        } else {
        	System.out.println("用户名可用！");
    	}
    }
}
```



### 7、编写 web 层

#### 7.1、实现用户注册的功能

##### 7.1.1、图解用户注册的流程：

![image-20210805220918481](D:\Coding\Java\JavaWeb\WebImage\BookCityImage\3.png)



##### 7.1.2、修改 regist.html 和 regist_success.html 页面

1. 添加 base 标签

```html
<!--写 base 标签，永远固定相对路径跳转的结果-->
<base href="http://localhost:8080/book/">
```



2. 修改 base 标签对页面中所有相对路径的影响（浏览器 F12，哪个报红，改哪个）

以下是几个修改的示例：

```html
<link type="text/css" rel="stylesheet" href="static/css/style.css" >
<script type="text/javascript" src="static/script/jquery-1.7.2.js"></script>
```



3. 修改注册表单的提交地址和请求方式



##### 7.1.3、编写 RegistServlet 程序

```java
public class RegistServlet extends HttpServlet {
    
    private UserService userService = new UserServiceImpl();
    
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException,IOException {
        // 1、获取请求的参数
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        String email = req.getParameter("email");
        String code = req.getParameter("code");
        // 2、检查 验证码是否正确 === 写死,要求验证码为:abcde
        if ("abcde".equalsIgnoreCase(code)) {
            // 3、检查 用户名是否可用
            if (userService.existsUsername(username)) {
                System.out.println("用户名[" + username + "]已存在!");
                // 跳回注册页面
                req.getRequestDispatcher("/pages/user/regist.html").forward(req, resp);
            } else {
                // 可用
                // 调用 Sservice 保存到数据库
                userService.registUser(new User(null, username, password, email));
                // 跳到注册成功页面 regist_success.html
                req.getRequestDispatcher("/pages/user/regist_success.html").forward(req, resp);
            }
        } else {
            System.out.println("验证码[" + code + "]错误");
            req.getRequestDispatcher("/pages/user/regist.html").forward(req, resp);
        }
    }
}
```



#### 7.2、IDEA 中 Debug 调试的使用

##### 7.2.1、Debug 调试代码，首先需要两个元素：断点 + Debug 启动服务器

略



#### 7.3、用户登录功能的实现

##### 7.3.1、图解用户登录

![image-20210806125309346](D:\Coding\Java\JavaWeb\WebImage\BookCityImage\4.png)

   

##### 7.3.2、修改 login.html 页面和 login_success.html 页面

1. 添加 base 标签

```html
<!--写 base 标签，永远固定相对路径跳转的结果-->
<base href="http://localhost:8080/book/">
```



2. 修改 base 标签对页面中所有相对路径的影响（浏览器 F12，哪个报红，改哪个）

修改示例：

```html
<link type="text/css" rel="stylesheet" href="static/css/style.css" >
<script type="text/javascript" src="static/script/jquery-1.7.2.js"></script>
```



3. 修改 login.html 表单的提交地址和请求方式



##### 7.3.3、LoginServlet 程序

```java
public class LoginServlet extends HttpServlet {
    
    private UserService userService = new UserServiceImpl();
    
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException,
    IOException {
        // 1、获取请求的参数
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        // 调用 userService.login()登录处理业务
        User loginUser = userService.login(new User(null, username, password, null));
        // 如果等于 null,说明登录 失败!
        if (loginUser == null) {
            // 跳回登录页面
            req.getRequestDispatcher("/pages/user/login.html").forward(req, resp);
        } else {
            // 登录 成功
            //跳到成功页面 login_success.html
            req.getRequestDispatcher("/pages/user/login_success.html").forward(req, resp);
        }
    }
}
```

### 8、项目阶段二：用户注册和登陆的实现

**需求 1：用户注册 需求如下：** 

1）访问注册页面

2）填写注册信息，提交给服务器

3）服务器应该保存用户

4）当用户已经存在----提示用户注册 失败，用户名已存在

5）当用户不存在-----注册成功

**需求 2：用户登陆 需求如下：**

1）访问登陆页面

2）填写用户名密码后提交

3）服务器判断用户是否存在

4）如果登陆失败 --->>>> 返回用户名或者密码错误信息

5）如果登录成功 --->>>> 返回登陆成功 信息

### 9、项目阶段三：代码优化

#### a) 页面 jsp 动态化

1. 在 html 页面顶行添加 page 指令
2. 修改文件后缀名位：.jsp
3. 使用 IDEA 搜索替换 .html 为 .jsp（快捷键：Ctrl + Shift + R）

#### b) 抽取页面中相同的内容

1. head 中 css、jquery、base 标签
2. 每个页面的页脚
3. 登录成功的菜单
4. manager 模块的菜单

#### c) 登录，注册错误提示，以及表单回显

以登录回显为示例：
    Servlet 程序端需要添加回显信息到 Request 域中

#### d) BaseServlet 的抽取

在实际的项目开发中，一个模块，一般只使用一个 Servlet 程序

###### 代码优化一：合并 LoginServlet 和 RegistServlet 为 UserServlet

1. 修改 form 请求地址为 BaseServlet
2. 为 login.jsp 和 regist.jsp 添加隐藏域 action 
    用来判断是 login 还是 regist

```jsp
    <input type="hidden" name="action" value="login" />
    <input type="hidden" name="action" value="regist" />
```

##### 优化代码二：使用反射优化大量 else if 代码

```java
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException,IOException {
        String action = req.getParameter("action");
        try {
            // 获取 action 业务鉴别字符串，获取相应的业务 方法反射对象
            Method method = this.getClass().getDeclaredMethod(action, HttpServletRequest.class,
            HttpServletResponse.class);
            // System.out.println(method);
            // 调用目标业务 方法
            method.invoke(this, req, resp);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

##### 代码优化三：抽取 BaseServlet 程序

实现思路：
> 1. 获取 action 参数值
> 2. 通过反射获取 action 对应的业务方法
> 3. 通过反射调用业务方法

> 其他 Servlet 程序通过继承 BaseServlet 程序使用通用方法

实现代码：
```java
    public abstract class BaseServlet extends HttpServlet {
        protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String action = req.getParameter("action");
        try {
        // 获取 action 业务鉴别字符串，获取相应的业务 方法反射对象
        Method method = this.getClass().getDeclaredMethod(action, HttpServletRequest.class,
        HttpServletResponse.class);
        // System.out.println(method);
        // 调用目标业务 方法
        method.invoke(this, req, resp);
        } catch (Exception e) {
        e.printStackTrace();
        }
        }
    }
```

#### e) 数据的封装和抽取 BeanUtils 的使用

> **BeanUtils 工具类**
> 它可以一次性的把所有请求的参数注入到 JavaBean 中
> 经常用于把 Map 中的值注入到 JavaBean 中，或者是对象属性值的拷贝操作
> BeanUtils 不是 jdk 的类，而是第三方的工具类
> 1. 导入需要的 jar 包：
> - commons-beanutils-1.8.0.jar
> - commons-logging-1.1.1.jar
> 2. 编写 WebUtils 工具类使用

**WebUtils 工具类**

```java
    public class WebUtils {
        /**
        * 把 Map 中的值注入到对应的 JavaBean 属性中。
        * @param value
        * @param bean
        */
        public static <T> T copyParamToBean( Map value , T bean ){
            try {
                System.out.println("注入之前：" + bean);
                /**
                * 把所有请求的参数都注入到 user 对象中
                */
                BeanUtils.populate(bean, value);
                System.out.println("注入之后：" + bean);
            } catch (Exception e) {
                e.printStackTrace();
            }
            return bean;
        }
    }
```

### 9、项目阶段四：使用 EL 表达式实现表单回显

```jsp
    ${ empty request.msg ? "请输入用户名和密码" : requestScope.msg }
    ${requestScope.username}
```

### 10、项目阶段五：图书模块

**MVC 概念**
- model 全称：Model 模型、View视图、Controller 控制器
- MVC 最早出现在 JavaEE 三层中的 Web 层，它可以有效的指导 Web 层的代码如何有效分离，单独工作
- View 视图：只负责数据和页面的显示，不接受任何与显示数据无关的代码，便于程序员和美工的分工合作 JSP/HTML
- Controller 控制器：只负责接受请求，调用业务层的代码处理请求，然后派发页面，是一个“调度者”的角色 Servlet 转到某个页面，或者重定向到某个页面 Servlet
- Model 模型：将与业务逻辑相关的数据封装为具体的 JavaBean 类，其中不掺杂任何与数据处理相关的代码 JavaBean/domain/entity/pojo

> MVC 是一种思想
> MVC 的理念是将软件代码拆分为组件，单独开发，组合使用（目的还是为了降低耦合度）

#### 编写图书模块的数据库表

```sql
    create table t_book(
        `id` int primary key auto_increment,
        `name` varchar(100),
        `price` decimal(11,2),
        `author` varchar(100),
        `sales` int,
        `stock` int,
        `img_path` varchar(200)
    );

    ## 插入初始化测试数据
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , 'java 从入门到放弃' , '国哥' , 80 , 9999 , 9 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , '数据结构与算法' , '严敏君' , 78.5 , 6 , 13 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , '怎样拐跑别人的媳妇' , '龙伍' , 68, 99999 , 52 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , '木虚肉盖饭' , '小胖' , 16, 1000 , 50 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , 'C++编程思想' , '刚哥' , 45.5 , 14 , 95 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , '蛋炒饭' , '周星星' , 9.9, 12 , 53 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , '赌神' , '龙伍' , 66.5, 125 , 535 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , 'Java 编程思想' , '阳哥' , 99.5 , 47 , 36 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , 'JavaScript 从入门到精通' , '婷姐' , 9.9 , 85 , 95 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , 'cocos2d-x 游戏编程入门' , '国哥' , 49, 52 , 62 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , 'C 语言程序设计' , '谭浩强' , 28 , 52 , 74 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , 'Lua 语言程序设计' , '雷丰阳' , 51.5 , 48 , 82 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , '西游记' , '罗贯中' , 12, 19 , 9999 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , '水浒传' , '华仔' , 33.05 , 22 , 88 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , '操作系统原理' , '刘优' , 133.05 , 122 , 188 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , '数据结构 java 版' , '封大神' , 173.15 , 21 , 81 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , 'UNIX 高级环境编程' , '乐天' , 99.15 , 210 , 810 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , 'javaScript 高级编程' , '国哥' , 69.15 , 210 , 810 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , '大话设计模式' , '国哥' , 89.15 , 20 , 10 , 'static/img/default.jpg');
    insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` , `img_path`)
    values(null , '人月神话' , '刚哥' , 88.15 , 20 , 80 , 'static/img/default.jpg');
    ## 查看表内容
    select id,name,author,price,sales,stock,img_path from t_book
```

#### 编写图书模块的 JavaBean

```java
    public class Book {
        private Integer id;
        private String name;
        private String author;
        private BigDecimal price;
        private Integer sales;
        private Integer stock;
        private String imgPath = "static/img/default.jpg";
    }
```

> 对 imgPath 处理
> 在 构造器和 imgPath 的 set 方法中进行 null 值 和 空字符串的判断

```java
    public void setImgPath(String imgPath) {
        //要求给定的图书封面的图书路径不能为空
        if (imgPath != null && !"".equals(imgPath)) {
            this.imgPath = imgPath;
        }
    }
```

Error
- regist.jsp 将注册的信息注入到 JavaBean 中
- BookDaoImpl queryBookById()
- BookDaoImpl queryBooks()
- BookService