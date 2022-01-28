# Introduce
> Nginx 是一个高性能的 HTTP 和反向代理 web 服务器
> 特点：占有内存少，并发能力强

## 反向代理
> 客户端对代理无感知，只需要将请求发送给反向代理服务器，
> 由反向代理服务器去选择目标服务器获取数据后，再返回给客户端
> 此时反向代理服务器和目标服务器对外就是一个服务器，
> 暴露的是代理服务器的地址，**隐藏了真实服务器 IP 地址**

> 正向代理
> 在客户端配置代理服务器，通过代理服务器进行互联网的访问（**隐藏真实的客户端**）

## 负载均衡
> 原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上，
> 将负载分发到不同的服务器，也就是我们所说的负载均衡

## 动静分离
> 为了加快网站的解析速度，可以将动态页面和静态页面由不同的服务器来解析，
> 加快解析速度，降低原来单个服务器的压力

### Nginx 安装

#### 安装 PCRE 库
> Perl Compatible Regular Expressions 兼容正则表达式
```shell
sudo apt-get install libpcre3 libpcre3-dev
# 查看版本号
pcre-config --version
```

#### 安装 zlib 库
> 用于对 HTTP 包的内容做 gzip 格式的压缩 
```shell
sudo apt-get install zlib1g-dev
```

#### 安装 OpenSSL 库
> 如果服务器不只是要支持 HTTP，还需要在更安全的 SSL 协议上传输 HTTP，那么需要拥有OpenSSL
> 另外，如果我们想使用 MD5, SHA1 等散列函数，那么也需要安装它 
```shell
sudo apt-get install openssl libssl-dev
```

#### 安装 Nginx
```shell
sudo apt install nginx
# 验证
sudo systemctl status nginx
```

## Nginx 配置文件结构以及最佳实践

- 所有的 Nginx 配置文件都在/etc/nginx/目录下。
- 主要的 Nginx 配置文件是/etc/nginx/nginx.conf。
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

## 常用命令
- 查看版本号
  - `nginx -v`
- 启动 Nginx
  - `nginx`
- 关闭 Nginx
  - `nginx -s stop`
- 重启 Nginx
  - `nginx -s reload`