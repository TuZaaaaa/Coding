# Email

## 组成部分
- 用户代理(邮箱客户端)
- 邮件服务器
- SMTP

## 邮件服务器
- 管理和维护发给用户的邮件
- 输出**报文队列**，保持待发送邮件报文
- 邮件服务器之间的 SMTP 协议（发送 email）

## SMTP
> Simple Mail Transfer Protocol
- 基于 TCP，默认端口：25
- 直接传输：从发送方服务器到接收方服务器
- 传输
  - 握手
  - 传输报文
  - 关闭
- 命令/响应 交互
  - 命令：ASCII 文本
  - 响应：状态码和状态信息
- 报文必须为 7 位 ASCII 码
- 报文格式
  - header
    - To
    - From
    - Subject
  - body
    - ASCII 字符
- 多媒体扩展
  - MIME(Multipurpose Internet Mail Extensions) 
  - 支持更多编码格式的对象
  - 在 header 声明
    - MIME-Version: 1.0
    - Content-Transfer-Encoding: base64
    - Content-Type: image/jpeg
    - base64 encoded data

## 邮件访问协议
> SMTP：用于传送到接收方的邮件服务器
> 邮件访问协议：用于从服务器访问邮件 

- POP
  - Post Office Protocol
  - 本地管理
- IMAP
  - Internet Message Access Protocol
  - 远程管理
- HTTP
  - 方便