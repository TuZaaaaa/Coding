# io
## 格式化的输入输出
### printf
> %[flags][width][.prec][hIL]type

- flag

| flag | 含义|
|---|---|
|-|左对齐|
|+|在前面放 + 或 -|
|space|整数留空|
|0|0 填充|

- width

| width | 含义|
|---|---|
|number|最小字符数|
|*|下一个参数是字符数|
|.number|小数点后的位数|
|.*|下一个参数是小数点后的位数|

- 类型修饰

| 类型修饰 | 含义|
|---|---|
|hh|单个字节|
|h|short|
|I|long|
|II|long long|
|L|long double|

- type

| type | 含义|
|---|---|
|i/d|int|
|u|unsigned int|
|o|八进制|
|x|十六进制|
|X|字母大写的十六进制|
|f/F|float|
|e/E|指数|
|g|float|
|G|float|
|a/A|十六进制浮点|
|c|char|
|s|字符串|
|p|指针|
|n|读入/写出的个数|

### scanf
> %[flag]type

- flag

| type | 含义|
|---|---|
|*|跳过|
|数字|最大字符数|
|hh|char|
|h|short|
|I|long double|
|II|long long|
|L|long double|

- type

| type | 用于 |
|---|---|
|d|int|
|i|整数，可能为十六进制或八进制|
|u|unsigned int|
|o|八进制|
|x|十六进制|
|a,e,f,g|float|
|c|char|
|s|字符串（单词）|
|[...]|所允许的字符|
|p|指针|

- printf() 和 scanf() 的返回值
  - 读入的项目数
  - 输出的字符数
> 在要求严格的程序中，应该判断每次调用 scanf() 或 printf() 的返回值，
> 从而了解程序运行中是否存在问题

## 文件输入输出
- 用 > 和 < 做重定向 

### FILE
- FILE* fopen(const char* restrict path, const char* restrict mode);
- int fclose(FILE *stream);
- fscanf(FILE*, ...);
- fprintf(FILE*, ...);

### 打开文件的标准代码
```c
  FILE* fp = fopen("file", "r");
  if(fp)
  {
    fscanf(fp, ...);
    fclose(fp);;
  } else {
    ...
  }
```

- fopen 参数
  
| 参数 | 含义 |
|---|---|
|r|打开只读|
|r+|打开读写，从文件头开始|
|w|打开只写，如果不存在则新建，如果存在则清空|
|w+|打开读写，如果不存在则新建，如果存在则清空|
|a|打开追加，如果不存在则新建，如果存在则从文件尾开始|
|..x|只新建，如果文件已存在则不能打开|
## 二进制文件
- 其实所有的文件最终都是二进制的
- 文本文件无非是用最简单的方式可以读写的文件
  - more、tail
  - cat
  - vi
- 而二进制文件是需要专门的程序来读写的文件
- 文本文件的输入输出是格式化，可能经过转码

### 文本 vs 二进制
- Unix 喜欢用文本文件来做数据存储和程序配置
  - 交互式终端的出现使得人们喜欢用文本和计算机交流
  - Unix 的 shell 提供了一些读写文本的小程序
- Windows 喜欢用二进制文件
  - DOS 是草根文化，并不继承和熟悉 Unix 文化
  - PC 刚开始的时候能力有限，DOS 的能力更有限，二进制更接近底层
- 文本的优势是方便人类读写，而且跨平台
- 文本的缺点是程序输入输出要经过格式化，开销大
- 二进制的缺点是人类读写困难，而且不跨平台
  - int 的大小不一致，大小端的问题
- 二进制的优点是程序读写快

### 程序为什么要用文件
- 配置
  - Unix 用文本， Windows 用注册表
- 数据
  - 稍微有点量的数据都放数据库了
- 媒体
  - 这个只能是二进制的
> 程序通过第三方库来读写文件，很少直接读写二进制了

### 二进制的读写
- 读入
```c
size_t fread(void *restrict ptr, size_t size,
size_t nitems, FILE *restrict stream);
```

- 写出
```c
size_t fwrite(const void *restrict ptr, size_t size,
size_t nitems, FILE *restrict stream);
```
> 注意 FILE 指针是最后一个餐宿
> 返回的是成功读写的字节数

- 为什么用 nitem?
  - 因为二进制文件的读写一般都是通过对一个结构变量的操作进行的
  - 于是 nitem 就是用来说明这次读写几个结构变量

### 在文件中定位
- long ftell(FILE *stream);
- int fseek(FILE *stream, long offset, int whence);
  - SEEK_SET:从头开始
  - SEEK_CUR:从当前位置开始
  - SEEK_END:从尾开始（倒过来）

### 可移植性
- 这样的二进制文件不具有可移植性
  - 在 int 为 32 位的机器上携程的数据文件无法直接在 int 位 64 位的机器上正确读出
- 解决方案之一是放弃使用 int，而是 typedef 具有明确大小的类型
- 更好的方案是用文本

## 按位运算
按位运算运算符：
- & 按位的与
- | 按位的或
- ~ 按位取反
- ^ 按位的异或
- << 左移
- \>> 右移

### 按位与 &
- 如果 (x)$_{i}$ == 1，并且 (y)$_{i}$ == 1，那么 (x & y)$_{i}$ = 1
- 否则的话 (x & y)$_{i}$ = 0
- 按位与的应用
  -  让某一位或某些位为 0：x & 0xFE
  -  取一个数的一段：x & 0xFF

### 按位或 |
- 如果 (x)$_{i}$ == 1 或 (y)$_{i}$ == 1，那么 (x | y)$_{i}$ = 1
- 否则的话：(x | y)$_{i}$ == 0
- 按位或常用于两种应用
  - 使得一位或几个位为 1： x | 0X01
  - 把两个数拼起来：0x00FF | 0xFF00

### 按位取反 ~
- (~x)$_{i}$ = 1 - (x)$_{i}$
- 把 1 位变 0，0 位变 1
- 想得到全部位数位 1 的数：~0
- 7 的二进制是 0111，x | 7 使得第 3 位为 1
- x & ~7，就使得第 3 位为 0

### 逻辑运算 vs 按位运算
- 对于逻辑运算，它只看到两个值：0 和 1
- 可以认为逻辑运算相当于把所有非 0 值都变成 1，然后做按位运算
  - 5 & 4 -> 4 而 5 && 4 -> 1 & 1 -> 1
  - 5 | 4 -> 5 而 5 || 4 -> 1 | 1 -> 1
  - ~4 -> 3 而 !4 -> !1 -> 0

### 按位异或 ^
- 如果 (x)$_{i}$ == (y)$_{i}$，那么 (x ^ y)$_{i}$ = 0
- 否则的话，(x ^ y)$_{i}$ == 1
- 如果两个位相等，那么结果为 0，不相等，结果为 1
- 如果 x 和 y 相等，那么 x ^ y 的结果为 0
- 对一个变量用同一个值异或两次，等于什么也没做
  - x ^ y ^ y -> x

## 移位运算

### 左移 <<
- i << j
- i 中所有的位向左移动 j 个位置，而右边填入 0
- 所有小于 int 的类型，移位以 int 的方式来做，结果是 int
- x <<= 1 等价于 x *= 2
- x <<= n 等价于 x *= 2$^{n}$ 

### 右移 >>
- i >> j
- i 中所有的位向右移 j 位
- 所有小于 int 的类型，移位以 int 的方式做，结果是 int
- 对于 unsigned 的类型，左边填入 0
- 对于 signed 的类型，左边填入原来的最高位
- x >>= 1 等价于 x /= 2
- x >>= n 等价于 x /= 2$_{n}$

