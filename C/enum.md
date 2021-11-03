# 枚举
> enum(enumeration) 是一种用户定义的数据类型

它用关键字 enum 以如下语法声明：
- enum 枚举类型名字 {名字0, ..., 名字n};
- 枚举类型名字通常不是真的使用，要用的是大括号里的名字，因为它们就是常量符号
  - 他们的类型是 int，值依次从 0 到 n，如：
    - enum colors {red, yellow, green};
    - red 的值为 1，yellow 的值为 2，green 的值为 3，
  - 当需要一些可以排列起来的常量值是，定义枚举的意义就是给了这些常量值名字
- 枚举量可以作为值
- 枚举类型可以跟上 enum 作为类型
- 但是实际上是以整数来做内部计算和外部输入输出的

- **小套路：自动计数的枚举**
- 定义枚举时，在末尾添加一个计数标识
```c
    enum COLOR {RED, YELLOW, GREEN, NumCOLORS};

    char *ColorNames[NumCOLORS] = {
        "red", "yellow", "green"
    };
```
> 这样需要遍历所有的枚举量或者需要建立一个用枚举量做下标的数组的时候就很方便了

- 声明枚举量的时候可以指定值
```c
enum COLOR {RED = 1, YELLOE, GREEN = 5};
```

> 一般用于有意义的排比的名字，用枚举比 const int 方便
> 枚举比宏(macro) 好，因为枚举有 int 类型
