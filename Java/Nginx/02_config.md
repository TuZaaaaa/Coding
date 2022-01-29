# config

## 配置文件
### 配置文件位置
- 所有的 Nginx 配置文件都在/etc/nginx/目录下
- 主要的 Nginx 配置文件是/etc/nginx/nginx.conf
- web 项目：/var/lib/tomcat9/webapps/

### 配置文件组成
- 全局块
> 主要会设置一些影响 Nginx 服务器整体运行的配置指令
- events 块
> 主要影响 Nginx 服务器与用户的网络连接
- http 块
> http 全局块配置的指令包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等
> - http 全局块
> - server 块

## 配置实例
### 反向代理示例一
- 实现功能
  - 浏览器中输入 www.123.com，跳转到 tomcat 页面
#### 安装 Jdk8
```shell
# 安装
sudo apt-get install openjdk-8-jdk
# 配置环境变量
vim ~/.bashrc
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
# 测试
java -version
```
#### 安装 tomcat9
```shell
# 安装
sudo apt install tomcat9 tomcat9-admin tomcat9-docs tomcat9-examples
# 查看服务的 8080 端口是否开启
ss –ltn
# 开启/关闭tomcat开机自启
sudo systemctl enable tomcat9
sudo systemctl disable tomcat9
```
#### 具体配置
- 在 windows 系统的 host 文件进行域名和 ip 对应关系的配置（无效）
- 配置 nginx.conf
```json
server {
  listen 80;
  server_name 49.232.214.47;
  
  location / {
      root html;
      index index.html index.htm;
      proxy_pass http://49.232.214.47:8080;
  }
}
```

### 反向代理示例二
- 实现功能
  - 使用 Nginx 反向代理，根据访问的路径跳转到不同端口的服务中
  - Nginx 监听端口 9001
    - 访问 http://127.0.0.1:9001/edu/ 跳转到 127.0.0.1:8081
    - 访问 http://127.0.0.1:9001/vod/ 跳转到 127.0.0.1:8082
#### 具体配置
1. 准备两个 tomcat，一个 8080 端口，一个 8081 端口，并准备好测试的页面
2. 修改 Nginx 的配置文件

### 最佳实践
- 为每个域名创建一个独立的配置文件，便于维护服务器。你可以按照需要定义任意多的 block 文件。
- Nginx 服务器配置文件被储存在/etc/nginx/sites-available目录下。在/etc/nginx/sites-enabled目录下的配置文件都将被 Nginx 使用。
- 最佳推荐是使用标准的命名方式。例如，如果你的域名是mydomain.com，那么配置文件应该被命名为/etc/nginx/sites-available/mydomain.com.conf
- 如果你在域名服务器配置块中有可重用的配置段，把这些配置段摘出来，做成一小段可重用的配置。
- Nginx 日志文件 (access.log 和 error.log) 定位在/var/log/nginx/目录下。推荐为每个服务器配置块，配置一个不同的access和error。
- 你可以将你的网站根目录设置在任何你想要的地方。最常用的网站根目录位置包括：
  - /home/<user_name>/<site_name>
  - /var/www/<site_name>
  - /var/www/html/<site_name>
  - /opt/<site_name>