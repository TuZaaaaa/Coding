# install&config

## install
- 将 database 解压到同一个目录
- 账户 orcl 密码 Admin123123

## Question
### 安装 oracle11g 时遇到 INS-13001 环境不满足最低要求 
database -> stage -> cvu > cvu_prereq.xml
添加以下内容：
```xml
    <OPERATING_SYSTEM RELEASE="6.2">
        <VERSION VALUE="3"/>
        <ARCHITECTURE VALUE="64-bit"/>
        <NAME VALUE="Windows 10"/>
        <ENV_VAR_LIST>
            <ENV_VAR NAME="PATH" MAX_LENGTH="1023" />
        </ENV_VAR_LIST>
    </OPERATING_SYSTEM>
```

### Environment variable: "PATH"
> This test checks whether the length of the environment variable "PATH" does not exceed the recommended length

cvu_prereq.xml
```xml
    <OPERATING_SYSTEM RELEASE="6.2">
        <VERSION VALUE="3"/>
        <ARCHITECTURE VALUE="64-bit"/>
        <NAME VALUE="Windows 10"/>
        <ENV_VAR_LIST>
            <ENV_VAR NAME="PATH" MAX_LENGTH="2023" />
        </ENV_VAR_LIST>
    </OPERATING_SYSTEM>
```

### ORA-00119: invalid specification ORA-00132: syntax error
https://stackoverflow.com/questions/28765999/ora-00119-invalid-specification-ora-00132-syntax-error
```cmd
    C:\Users\Hp>sqlplus SYSTEM as SYSDBA   
    SQL*Plus: Release 11.2.0.1.0 Production on Wed Aug 3 12:28:32 2016    
    Copyright (c) 1982, 2010, Oracle.  All rights reserved.    
    Enter password:    
    Connected to an idle instance.    
    SQL> startup  
    ORA-00119: invalid specification for system parameter LOCAL_LISTENER    
    ORA-00132: syntax error or unresolved network name 'LISTENER_ORCL'    
    SQL> create pfile from spfile;    
    File created.    
    **************************    
    Now open the PFILE (INIT%ORACLE_SID%.ORA) from %ORACLE_HOME%\database
    Search for string local_listener='LISTENER_ORCL' and remove it and save the file.     
    **************************    
    SQL> startup nomount pfile='C:\app\Hp\product\11.2.0\dbhome_1\database\INITorcl.ORA'    
    ORACLE instance started.    
    Total System Global Area 1686925312 bytes
    Fixed Size                  2176368 bytes
    Variable Size            1291848336 bytes
    Database Buffers          385875968 bytes
    Redo Buffers                7024640 bytes    
    SQL> create spfile from pfile;   
    File created.    
    SQL> startup    
    ORA-01081: cannot start already-running ORACLE - shut it down first    
    SQL> shutdown    
    ORA-01507: database not mounted    

    ORACLE instance shut down.    
    SQL> startup    
    ORACLE instance started.    
    Total System Global Area 1686925312 bytes
    Fixed Size                  2176368 bytes
    Variable Size            1291848336 bytes
    Database Buffers          385875968 bytes
    Redo Buffers                7024640 bytes
    Database mounted.
    Database opened.    
    SQL>
```
## Uninstall
作者：知我者
链接：https://www.zhihu.com/question/373407650/answer/1118099909
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

就按正常卸载的流程走就可以。可以借鉴:
**完全卸载oracle11g步骤：**
1. win+r 输入services.msc启动服务管理器，停止所有Oracle服务。
2. 开始找到oracle的启动栏打开选择Universal Installer，单击“卸载产品”-“全部展开”，选中除“OraDb11g_home1”外的全部目录，删除。
5. 运行**regedit**，选择HKEY_LOCAL_MACHINE\SOFTWARE\ORACLE，按del键删除这个入口。

6. 选择HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services，滚动这个列表，删除所有Oracle入口(以oracle或OraWeb开头的键)。
7. HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Eventlog\Application，删除所有Oracle入口。
8. 删除HKEY_CLASSES_ROOT目录下所有以Ora、Oracle、Orcl或EnumOra为前缀的键。
9. 删除HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\MenuOrder\Start Menu\Programs中所有以oracle开头的键。
10. 删除HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBCINST.INI中除Microsoft ODBC for Oracle注册表键以外的所有含有Oracle的键。
11. 我的电脑-->属性-->高级-->环境变量,删除环境变量CLASSPATH和PATH中有关Oracle的设定。
12. 从桌面上、STARTUP（启动）组、程序菜单中，删除所有有关Oracle的组和图标。（或右键选择打开文件位置，删除该Oracle文件夹）
13. 删除所有与Oracle相关的目录(如果删不掉，重启计算机后再删就可以了)包括：
1. C:\Program file\Oracle目录。
2. ORACLE_BASE目录(oracle的安装目录，如：E:\app\<你的用户名>\...)。
3. C:\WINDOWS\system32\config\systemprofile\Oracle目录。
4. C:\Users\Administrator\Oracle或C:\Documents and Settings\Administrator\Oracle目录。
5. C:\WINDOWS下删除以下文件ORACLE.INI、oradim73.INI、oradim80.INI、oraodbc.ini等等。
6. C:\WINDOWS下的WIN.INI文件中若有[ORACLE]的标记段，删除该段。
14. 如有必要，删除所有Oracle相关的ODBC的DSN
15. 到事件查看器中，删除Oracle相关的日志 说明： 如果有个别DLL文件无法删除的情况，则不用理会，**重新启动**，开始新的安装，安装时，选择一个新的目录，则，安装完毕并重新启动后，老的目录及文件就可以删除掉了。

**最后删除：安装时的app根目录。例如在E:\app，就删除E盘中的app文件。**

## 解锁 scott
先查询一下目前数据库是否有 scott 用户
```sql
    select username,account_status from dba_users where username like '%SCOTT%';
```
如果是锁定状态那么解锁和设置密码 tiger
```sql
    alter user scott account unlock identified by tiger;
```
