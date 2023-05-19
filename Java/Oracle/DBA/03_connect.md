# connect

## 客户端连接 Oracle 排错

- Oracle 数据库服务正常（硬件、系统、数据库（open, mount(sys)））

```SQL
select status from v$instance;
```
- listener(启动，有数据库的注册信息)

```bash
lsnrctl status
```
listener 刚刚启动时，需要等待轮询来注册数据库信息
也可以使用以下命令强制注册
```SQL
alter system register
```
Check listener.ora

- 正确安装 Oracle 客户端
- 配置网络服务名解析文件 tnsnames.ora(ip, port, service_name)

```SQL
show parameter service_names
```
- 网络

```bash
ping ip
tnsping net 服务名
```
