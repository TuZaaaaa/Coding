# CLion

## CMakeLists.txt 
```txt
# 限定了 CMake 的版本
cmake_minimum_required(VERSION 3.8)

# 该命令表示项目的名称是 main 
project(demo1)

# set(变量 值)
set(CMAKE_C_STANDARD 99)
set(SOURCE_FILES main.c)

# ${xxx} 引用上面定义的 xxx 变量
# add_executable 表示把变量 SOURCE_FILES 编译成一个名称为 demo1 的可执行文件。
add_executable(demo1 ${SOURCE_FILES})
```

## Show Chinese Character in the console
- 将 file encodings 字符集全改成 utf8
- 将控制台下面的字符集改成 GBK

> 控制台下面的字符集是跟微软字符集交互，微软默认字符集是 GBK