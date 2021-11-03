# 指针
> 指针就是保存地址的变量，变量的值是内存的地址
> - 普通变量的值是实际的值
> - 指针变量的值是具有实际值的变量的地址

## 作为参数：
- void f(int *p)，在被调用时得到了某个变量的地址：int i=0; f(&i);
- 在函数里面可以通过这个指针访问外面的这个 i，经过 f 函数的调用后 i 的值变了
```c
    #include <stdio.h>

    void f(int *p);

    int main(void)
    {
        int i = 6;
        printf("&i=%p\n", &i);
        f(&i);
        return 0;
    }

    void f(int *p)
    {
        printf(" p=%p\n", p);
    }
```
## 访问地址上的变量：
- \* 是一个单目运算符，用来访问指针的值所表示的地址上的变量
- 可以做右值也可以做左值：int k = *p; *p = k+1;

## 相关运算符:
- sizeof: 给出某个类型或变量在内存中占的字节数
- &: "%p"，取地址（16 进制）
    - 获得变量的地址，它的操作数必须是变量
    - 地址的大小是否与 int 相同取决于编译器
    - 不能对没有地址的取地址
    - 连续两个变量，地址紧挨，中间相差变量类型占得字节，先定义的在更低的位置（堆栈，自顶向下）
    - 数组：&a == a == &a[0] == &a[1] - 类型长度
    - int *p 表示 *p 是一个 int，p 是一个指针
> & 和 * 的功能
> \*&yptr == *(&yptr) == *(yptr的地址) == 得到那个地址上的变量 == yptr
> &\*yptr == &(*yptr) == &(y) == 得到 y 的地址 == yptr

Why? 编译没有报错
`int i; scanf("%d", i);`
将变量写到了不应该写的地址

## 应用场景
### 交换两个变量的值
```c
void swap(int *pa, int *pb)
{
    int t = *pa;
    *pa = *pb;
    *pb = t;
}
```

### 指针 return
- 函数返回多个值，某些值就只能通过指针返回
  - 传入的参数实际上是需要保存带回的结果的变量
```c
    #include <stdio.h>

    void minmax(int a[], int len, int *max, int *min);

    int main(void)
    {
        int a[] = {1,2,4,5,45,67,7,8,9,33,23};
        int min, max;
        minmax(a, sizeof(a) / sizeof(a[0]), &min, &max);
        printf("min=%d, max=%d\n", min, max);
        return 0;
    }

    void minmax(int a[], int len, int *min, int *max)
    {
        int i;
        *min = *max = a[0];
        for(int i = 1;i < len;i++)
        {
            if (a[i] < *min)
            {
                *min = a[i];
            }
            if (a[i] > *max)
            {
                *max = a[i];
            }
        }
    }
```

### 返回运算状态
- 常用的套路是让函数返回特殊的不属于有效范围内的值来表示出错
    - -1 或 0 (用于文件操作)
    - 当任何数值都是有效的可能结果时，就需要分开返回

## 指针与数组
- 函数参数表中的数组实际上是指针，参数中的 int a[] 是指针
    - sizeof(a) == sizeof(int*)
    - 但是可以用数组的运算符 [] 进行运算
    - int sum(int *ar, int n) == int sum(int *, int) == int sum(int ar[], int n) == int sum(int [], int) 在参数表中是等价的
    - 所以在函数内部没有办法用 sizeof 获取大小

- 数组变量是特殊的指针
    - 数组变量本身表达地址，因此 int a[10]; int *p = a; //无需用 & 取地址，但是数组的单元表达的是变量，需要用 & 取地址：a==&a[0];
    - []运算符可以对数组做，也可以对指针做：p[0] == a[0]
    - \* 运算符可以对指针做，也可以对数组做：*a = 25
    - 数组变量是 const 的指针，所有不能被赋值：int a[] == int *const a


## 指针与 const
### 指针是 const
> 表示一旦得到了某个变量的地址，不能再指向其他变量
```c
    int* const q = &i;// q 是 const
    *q = 26;// OK
    q++; // ERROR
```
### 所指是 const
> 表示不能通过这个指针区修改那个变量（并不能使那个变量称为 const）
```c
    const int *p = &i;
    *p = 26;// ERROR *p 是 const
    i = 26;// OK
    p = &j;// OK
```

> 这些是什么意思？
```c
    int i;
    const int* p1 = &i;
    int const* p2 = &i;
    int *const p3 = &i;
```
> const 在 * 前，修饰的是所指
> const 在 * 后，修饰的是指针

### 转换
- 总是可以把一个非 const 的值转换为 const 的
```c
    void f(const int* x);
    int a = 15;
    f(&a); // OK
    const int b = a;

    f(&b); // OK
    b = a + 1;// ERROR 
```
> 当要传递的参数的类型比地址大的时候，这是常用的手段
> 既能用比较少的字节数传递值给参数，又能避免函数对外面的变量进行修改

### const 数组
- const int a[] = {1,2,3,4,5,6};
- 数组变量已经是 const 的指针，这里的 const 表明数组的每个单元都是 const int
- 所以必须通过初始化进行赋值
> 用于保护数组值
> - 当数组传入函数时，传递的是地址，函数内部可以修改数组的值
> - 为了保护数组不被函数破坏，可以设置参数为 const

## 指针运算
- 指针加 1，表示让指针指向下一个变量
```c
    int a[10];
    int *p = a;
    *(p+1) == a[1]
```
> 地址改变了多少，需要看类型占多少字节
> 如果指针不是指向一片连续分配的空间
> 如数组，则这种运算没有意义
> - += -= ++ -- 同理

- 指针相减
> 等于两个地址的差除以 sizeof(type)

- **\*p++**
- 取出 p 所指的数据，然后将 p 移到下一个位置
- * 的优先级小于 ++
- 常用于数组类的连续空间操作（遍历）
```c
char ac[] = {0,1,2,3,4,5,6,7,8,9,-1};
char *p = &ac[0];
int i;
for(i = 0;i < sizeof(ac) / sizeof(ac[0]);i++)
{
    print("%d\n", ac[i]);
}

while(*p != -1)
{
    printf("%d\n", *p++);
}
```

- 指针比较
> 比较在内存中的地址
> 数组中的单元的地址肯定是线性递增的

## 0 地址
- 内存中的 0 地址通常是不能随便碰的地址
- 用处：
  - 返回的指针是无效的
  - 指针没有被真正初始化（先初始化为0）
- NUll 是一个预先定义的符号，表示 0 地址
  - 推荐使用 NULL

## 指针的类型
- 无论指向什么类型，所有指针的大小都是一样的，因为都是地址
- 指向不同类型的指针不能互相赋值
- 这是为了避免用错指针

### 指针的类型转换
- void* 表示不知道指向什么东西的指针
- 计算时与 char* 相同（但不相通）
- 指针也可以转换类型
- int *p = &i;void *q = (void*)p;
- 这并没有改变 p 所指的变量的类型，而是让后人用不同的眼光看 p 所指的变量

## 动态内存分配*
> 输入数据时，告诉你个数，再输入，记录每个数据
```c
    #include <stdio.h>
    #include <stdlib.h>

    int main(void)
    {
        int number;
        int* a;
        int i;
        printf("输入数量：");
        scanf("%d", &number);
        a = (int*)malloc(number*sizeof(int));
        for(int i = 0;i < number;i++)
        {
            scanf("%d", &a[i]);
        }
        for(int i = number - 1;i >= 0;i--)
        {
            printf("%d", &[i]);
        }
        free(a);// 释放空间
        return 0;
    }
```

malloc
> #include <stdlib.h>
> void* malloc(size_t size);
> 向 malloc 申请空间是以字节为单位的
> 返回结果是 void*，需要类型转换为自己需要的类型
> (int*)malloc(n*sizeof(int))
> 没空间了？
> - 如果申请失败返回 0，或者叫 NULL

free()
> 将申请的空间还给系统
> 只能还申请来的空间的首地址

常见问题：
- 申请了没 free 长时间运行内存逐渐下降
- free() 过了，再次 free()
- 地址变过了，直接去 free()
