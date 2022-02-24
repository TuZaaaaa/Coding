# C#

## 结构体
- 值类型和引用类型
    - 结构体是值类型，Class 是引用类型
    - 在函数调用内部指令中，struct 直接在栈中进行内存分配，Class 是在堆中进行内存分配
    - 栈中的内存，会在函数调用完成后回收，堆中的内存由 C# 运行时自动回收（GC）
    - 在函数调用过程中，Class 拷贝指示拷贝地址，struct 是所有数据都拷贝（深拷贝）
    - 如果需要将 struct 的引用传递给函数内部，则需要使用关键字 ref
- 内存分布
    - Readonly：代码区 Const
    - 代码执行区域：stack, 栈
    - 数据存储：heap
    - 全局区域：static 类型信息

## 容器
- Array 固定长度
- List 动态数组，自动增长
- Dictionary 使用 Key 来获取 Value
- HashSet 唯一 [] Contains
- Queue 可动态增长，先进先出 Enqueue Dequeue
- Stack 可动态增长，后进先出 Push Pop

## 函数指针
- delegate：可以保存多个函数指针，使用 += -= 进行操作
- 匿名函数：方便代码的编写，编译器会给他取名字
- Action：无返回值的 delegate
- Func：有返回值的 delegate
- 监听者模式

