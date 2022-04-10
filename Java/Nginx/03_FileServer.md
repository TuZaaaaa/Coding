```yaml
server {
    listen  80;
    # 自己PC的ip或者服务器的域名
    server_name 10.1.2.3;
    # 避免中文乱码
    charset utf-8;
    # 存放文件的目录
    root /home/pi/share;
    location / {
        # 索引
        autoindex on;
        # 显示文件大小
        autoindex_exact_size on;
        # 显示文件时间
        autoindex_localtime on;
    }
}
```