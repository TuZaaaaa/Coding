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

## 常用命令
- 查看版本号
  - `nginx -v`
- 启动 Nginx
  - `nginx`
- 关闭 Nginx
  - `nginx -s stop`
- 重启 Nginx
  - `nginx -s reload`
- 检查语法错误
  - `nginx -t`
- 重新加载配置 
  - `nginx -s reload`