# introduce

## 软件安装
- Keil5
- STC-ISP

## 单片机
> Micro Controller Unit
> 内部集成了 CPU, RAM, ROM, 定时器，中断系统，通讯接口等一系列电脑的常用硬件功能

**单片机的任务：**
- 信息采集（依靠传感器），处理（依靠 CPU）
- 对硬件设备（例如电机，LED 等）的控制

**应用领域：**
- 智能仪表
- 实时工控
- 通讯设备导航系统
- 家用电器

STC89C52 单片机
- 所属系列：51 单片机系列
- 公司：STC 公司
- 位数：8 位
- RAM：512 字节 random access memory 类似内存，断点数据丢失
- ROM：8K（Flash）read only memory 类似储存，断点数据还在
- 工作频率：12 MHz（本开发板使用）

### 单片机命名规则
STC89 aa bb .. 35 c - dddd ee

- 89 STC 12T/6T 8051 系列
- aa 工作电压
    - C:5.5V ~ 3.8V
    - LE:3.6V-2.4V/3.4V ~ 2.4V
- bb 程序空间以及 RAM 空间大小
    - 51:4k 程序空间，512 RAM 空间
    - 52:8k 程序空间，512 RAM 空间
    - 53:12k 程序空间，512 RAM 空间
    - 14:14k 程序空间，512 RAM 空间
    - 54:16k 程序空间，1280 RAM 空间
    - 58:32k 程序空间，1280 RAM 空间
    - 516:62k 程序空间，1280 RAM 空间
    - 510:40k 程序空间，1280 RAM 空间
    - 512:48k 程序空间，1280 RAM 空间
    - 514:56k 程序空间，1280 RAM 空间
- 35 最大工作频率
- c 工作维度范围
    - I:工业级 -40$^{\circ}$C ~ 85$^{\circ}$C
    - C:商业级 0$^{\circ}$C ~ 70$^{\circ}$C
- dddd 封装类型
    - 如 PDIP, LQFP, PLCC, PQFP
- ee 管脚数
    - 如 40, 44

> Vcc 正极
> Gnd 负极


