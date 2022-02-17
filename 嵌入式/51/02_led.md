# LED
## create new Project
- Vendor:Microchip
- Device:AT89C52

> LED
> Light Emitting Diode 发光二极管

Question
- Error: failed to execute 'd:\Keil\C51\BIN\C51.EXE' Target not created
> 解决办法：
> 找到自己的keil安装目录，下面有个TOOLS.INI文件。用记事本打开这个文件，找到：
> [C51] PATH=“d:\Keil\C51”
> 或类似字样，将PATH=后面的内容改为自己keil实际安装的目录，最后一个文件夹是C51
> 
> 我的keil装在了C:\Keil这个目录，那么最后类似于这样：
> [C51]
> PATH=“C:\Keil\C51”
> 
> 然后保存TOOLS.INI。重启keil即可生效（务必要重启keil）

## 案例
### 点亮一个 LED
Keil5:
```c
#include <REG52.H>

void main()
{
	P2=0xFE; //1111 1110
	while(1)
	{
		
	}
}
```

STC-ISP:
- 芯片型号：STC89C52RC/LF52RC
- 串口：USB-SERIAL CH340 (COM5)
