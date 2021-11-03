# String
## 概念
- 以 0（整数0）结尾的一串字符
  - 0 或 '\0' 是一样的，但是和 '0' 不同
- 0 标志字符串的结束，但它不是字符串的一部分
  - 计算字符串长度的时候不包含这个 0
- 字符串以数组形式存在，以数组或指针的形式访问
  - 更多的是以指针的形式
- string.h 里有很多处理字符串的函数

## 字符串常量
-  "hello" 会被编译变成一个字符数组放在某处，这个数组的长度是 6，结尾还有表示结束的 0
- 两个相邻的字符串常量会自动连接起来
- 字符串是以字符数组的形态存在的
  - 不能用运算符对字符串做运算
  - 通过数组的方式可以遍历字符串
> 唯一特殊的地方就是字符字面量可以用来初始化字符数组
- char* s = "Hello World";
  - s 是一个指针，初始化为指向一个字符串常量，实际上是 const char* s
  - 如果字符串相同，指针将指向同一个地方

- 书写方式：指针还是数组？
  - 数组：这个字符串在这里
    - 作为本地变量空间自动被回收
  - 指针：这个字符串不知道在哪里
    - 处理参数
    - 动态分配空间
> 如果要构造一个字符串--> 数组
> 如果要处理一个字符串--> 指针

## 输入输出
- char string[8]
- scanf("%s", string);
    - scanf() 读入一个单词（到空格、tab或回车为止）
    - 不安全，因为不知道读入内容的长度
    - 在 % 和 s 之间的数字表示最多允许读入的字符数量，数量应为数组大小 - 1
- 空字符串
  - char buffer[100] = "";
    - 代表一个空字符串，buffer[0] == '\0'
  - char buffer[] = "";
    - 这个数组长度为 1

## put/getchar
- int putchar(int c);
    - 向标准输出写几个字符
    - 返回写了几个字符，EOF(-1) 表示写失败
- int getchar(void);
    - 从标准输入读入一个字符
    - 返回类型是 int 是为了返回 EOF(-1)
> EOF:End of File.

## string.h
### strlen
- size_t strlen(const char *s);
- 返回 s 的字符串长度（不包括结尾的 0 ）
```c
    size_t myLen(const char* s)
    {
        int idx = 0;
        while (s[idx] != '\0')
        {
            idx++;
        }
        return idx;
    }
```
### strcmp
- int strcmp(const char *s1, const char *s2);
- 比较两个字符串，返回值：
    -  0: s1 == s2
    -  1: s1 > s2
    - -1: s1 < s2
- 简单实现
    - lv1
        ```c
            int myCmp(const char* s1, const char* s2)
            {
                int idx = 0;
                while (1)
                {
                    if (s1[idx] != s2[idx])
                    {
                        break;
                    } else if (s1[idx] == '\0')
                    {
                        break;
                    }
                    idx++;
                    
                }
                return s1[idx] - s2[idx];
            } 
        ```
    - lv2
        ```c
            int myCmp(const char* s1, const char* s2)
            {
                int idx = 0;
                while (s1[idx] == s2[idx] && s1[idx] != '\0')
                {
                    idx++;
                }
                return s1[idx] - s2[idx];
            }
        ```
    - lv3
        ```c
            int myCmp(const char* s1, const char* s2)
            {
                while (*s1 == *s2 && *s1 != '\0')
                {
                    *s1++;
                    *s2++;
                }
                return *s1 - *s2;
            }
        ```

### strcpy
- char* strcpy(char *restrict dst, const char *restrict src);
- 把 src 的字符串拷贝到 dst
  - restrict 表明 src 和 dst 不重叠(C99)
- 返回 dst
- 为了能链起代码来
- 复制一个字符串
```c
    char *dst = (char*)malloc(strlen(src) + 1);
    strcpy(dst, src);
```
- 简单实现
  - array:
    ```c
        char* myCpy(char* dst, const char* src)
        {
            int idx = 0;
            while (src[idx])
            {
                dst[idx] = src[idx];
                idx++;
            }
            dst[idx] = '\0';
            return dst;
        }
    ```

  - pointer:
    ```c
        char* myCpy(char* dst, const char* src)
        {
            char *ret = dst;
            while (*src)
            {
                *dst++ = *src++;
            }
            *dst = '\0';
            return ret;
        }
    ```

  - suckless: 
    ```c
        char* myCpy(char* dst, const char* src)
        {
            char *ret = dst;
            while (*dst++ = *src++);
            *dst = '\0';
            return ret;
        }
    ```
### strcat
- char* strcat(char *restrict s1, const char *restrict s2);
- 把 s2 拷贝到 s1的后面，接成一个长的字符串
- 返回 s1
- s1 必须具有足够的空间
> 安全版本
> strcpy(), strcat() 都可能会出现安全问题，如果 des，没有足够的空间？
> char* strncat(char *restrict s1, const char *restrict s2, size_t n);

### strcmp
- char* strchr(const char *s, int c);
- char* strchr(const char *s, int c);
- 返回 NULL 表示没有找到
- 如何寻找第二个值
```c
    char s[] = "hello";
    char *p = strchr(s, 'l');
    p = strchr(p + 1, 'l');
    printf("%s\n", p);
    return 0;
```

- 将后面那段复制到字符串
```c
    char s[] = "hello";
    char *p = strchr(s, 'l');
    char *t = (char*)malloc(strlen(p) + 1);
    strcpy(t, p);
    p = strchr(p + 1, 'l');
    printf("%s\n", p);
    printf("%s\n", t);
    free(t);
    return 0;
```

- 将后面那段复制到字符串
  - 将后面那段第一个字符替换为 \0，使用完应替换回去
```c
    char s[] = "hello";
    char *p = strchr(s, 'l');
    char c = *p;
    *p = '\0';
    char *t = (char*)malloc(strlen(p) + 1);
    strcpy(t, s);
    p = strchr(p + 1, 'l');
    printf("%s\n", p);
    printf("%s\n", t);
    free(t);

    return 0;
```