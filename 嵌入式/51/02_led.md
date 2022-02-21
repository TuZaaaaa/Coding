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


- Create Hex File
	> Options for target > Output

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

### LED 闪烁

利用 STC-ISP 的**软件延时计算器**生成延时函数
- 系统频率：12.000 MHz
- 定时长度：500 毫秒
- 8051 指令集：STC-Y1

Keil5:
```c
#include <REG52.H>
#include <INTRINS.H>

void Delay500ms();

void main()	
{
	while(1)
	{
		P2 = 0xFE;
		Delay500ms();
		P2 = 0xFF;
		Delay500ms();
	}
}

void Delay500ms()		//@12.000MHz
{
	unsigned char i, j, k;

	_nop_();
	i = 4;
	j = 205;
	k = 187;
	do
	{
		do
		{
			while (--k);
		} while (--j);
	} while (--i);
}
```

### LED 流水灯
```c
#include <REG52.H>
#include <INTRINS.H>

void Delay(unsigned int ms);

void main()
{
	while(1)
	{
		P2 = 0xFE; // 1111 1110
		Delay(100);
		P2 = 0xFD; // 1111 1101
		Delay(100);
		P2 = 0xFB; // 1111 1011
		Delay(100);
		P2 = 0xF7; // 1111 0111
		Delay(100);
		P2 = 0xEF; // 1110 1111
		Delay(100);
		P2 = 0xDF; // 1101 1111
		Delay(100);
		P2 = 0xBF; // 1011 1111
		Delay(100);
		P2 = 0x7F; // 0111 1111
		Delay(100);
	}
}


void Delay(unsigned int ms)		//@12.000MHz
{
	unsigned char i, j;
	while(ms)
	{
		i = 2;
		j = 239;
		do
		{
			while (--j);
		} while (--i);
		ms--;
	}
}
```