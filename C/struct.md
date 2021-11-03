# struct
> 结构类型

## 声明结构类型
- 第一种形式
```c
    struct date {
        int  month;
        int day;
        int year;
    };

    struct date today;
```

- 第二种形式
```c
    struct {
        int  month;
        int day;
        int year;
    } p1, p2;
```

- 第三种形式
```c
    struct date {
        int  month;
        int day;
        int year;
    } p1, p2;
```
> 声明在函数内/外？
>   - 和本地变量一样，在函数内声明的结构类型只能在函数内部使用
>   - 通常在函数外声明结构类型，这样就可以被多个函数所使用

## 结构的初始化
- 第一种形式
```c
    struct date today = {07, 31, 2014};
```

- 第一种形式
```c
    struct date thismonth = {.month=7, .year=2014};
```
> 没有初始化的变量默认值为 0

## 结构成员
- 结构和数组有点像
- 数组用 [] 运算符和下标访问其成员
  - a[0] = 10;
- 结构用 . 运算符和名字访问其成员

## 结构运算
- 想要访问整个结构，直接用结构变量的名字 
- 对于整个结构，可以作赋值、取地址，也可以传递给函数参数

## 结构指针
- 和数组不同，结构变量的名字并不是结构变量的地址，必须使用 & 运算符
```c
    struct date *pDate = &today;
```

## 作为函数参数
- 整个结构可以作为参数的值传入韩素
- 这个时候实在函数内新建一个结构变量，并复制调用者的结构的值
- 也可以返回一个结构

## 输入结构
- 没有直接的方式可以一次 scanf() 一个结构
- 用函数读入结构
  - 如何将读入的结构返回？
  - C 在函数调用时时传值的
  - 可以返回一个 struct
  - 更优方式：传指针，返回指针

## 指向结构的指针
> 用 -> 标识指针所指的结构变量中的成员

## 结构中的结构
- 结构中的数组
```c
    struct date dates[100];
    struct date date[] = {
        {4, 5, 2005}, {2, 4, 2005}}
    };
```

- 结构中的结构
```c
    struct dateAndTime = {
        struct date sdate;
        struct time stime;
    };
```

## typedef
> typedef 用来声明一个已有的数据类型的新名字

比如：
`typedef int Lengeth;`
- 使得 Length 称为 int 类型的别名
```c
Length a, b, len;
Length numbers[10];
```

- 声明新的类型的名字
  - 新的名字是某种类型的名字
  - 改善了程序的可读性

```c
    typedef struct ADate {
        int month;
        int day;
        int year;
    } Date;

    typedef struct {
        int month;
        int day;
        int year;
    } Date;
```

## union
> 联合，与 struct 相似
- 声明
```c
union AnElt {
    int i;
    char c;
} elt1, elt2;
```

- 存储
  - 所有成员共享同一个空间
  - 同一时间只有一个成员是有效的
  - union 的大小是其最大的成员
- 初始化
  - 对第一个成员进行初始化
- 用途
```c
    typedef union {
        int i;
        char ch[sizeof(int)];
    } CHI;

    int main(int argc, char const *argv[])
    {
        CHI chi;
        int i;
        chi.i = 1234;
        for(i = 0; i < sizeof(int);i++)
        {
            printf("%02hhX", chi.ch[i]);
        }
        printf("\n");
        return 0;
    }
```
- 得到一个类型内部有多少个字节，可用于文件操作，当把一个数字以二进制的形式写进文件的时候，作为读取时候中间的媒介