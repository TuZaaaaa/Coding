# 02_startup&shutdown

## Database Structure
- Database Service
	- Instance
		- memory
		- background process
	- Database
		- physical files
			- control file
			- redo file
			- .dbf file

## Startup

- Database State
	- shutdown: not start
	- nomount: start instance = start background process and allocate the memory
	- mount = load control file
	- open = load data file and redo log

## Shutdown
> 关闭的过程和启动相反

### Shutdown mode

| | Abort | immediate | transactional | normal |
| --- | --- | --- | --- | --- |
| 是否允许建立新的连接 | no | no | no | no |
| 是否等待当前会话结束 | no | no | no | yes |
| 是否等待当前事务处理结束 | no | no | yes | yes |
| 是否执行检查点(scn)操作 | no | yes | yes | yes |
	
