# Introduce
> Docker是基于Go语言实现的云开源项目。
> Docker的主要目标是“**Build, Ship[ and Run Any App,Anywhere**"，
> 也就是通过对应用组件的封装、分发、部署、运行等生命期的管理，使用户的APP (可以是一个WEB应用或数据库应用等等)及其运行环境能够做到“**一次封装，到处运行**”。

## 配置镜像加速
> 适用于 Ubuntu 16.04+、Debian 8+、CentOS 7+
目前主流 Linux 发行版均已使用 systemd 进行服务管理，这里介绍如何在使用 systemd 的 Linux 发行版中配置镜像加速器

请首先执行以下命令，查看是否在 docker.service 文件中配置过镜像地址。
```shell
	$ systemctl cat docker | grep '\-\-registry\-mirror'
```
如果该命令有输出，那么请执行  systemctl cat docker 查看 ExecStart= 出现的位置，修改对应的文件内容去掉 --registry-mirror 参数及其值，并按接下来的步骤进行配置。
如果以上命令没有任何输出，那么就可以在 /etc/docker/daemon.json 中写入如下内容（如果文件不存在请新建该文件）：
```json
  {
    "registry-mirrors": [
      "https://hub-mirror.c.163.com",
      "https://mirror.baidubce.com"
    ]
  }
```
注意，一定要保证该文件符合 json 规范，否则 Docker 将不能启动。
之后重新启动服务。
```shell
  $ sudo systemctl daemon-reload
  $ sudo systemctl restart docker
```

## Docker 提权
```shell
  sudo chmod 666 /var/run/docker.sock
```