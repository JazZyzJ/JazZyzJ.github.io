# Parallel Algorithms

## Intro

!!! question "intro"
    
    对于问题$a+b$，我们可以在怎样的时间复杂度内解决呢？

    一是$O(\log a + \log b)$，他对应的是用图灵机来解决

    二是$O(1)$，他对应的是用Random Access Machine来解决


我们今天来讨论的是$O(1)$的解决方案。

!!! note "定义"

    今天我们讨论的并行算法是基于RAM的，使用parallel RAM（PRAM）模型。

    - 已知一段无限长的cell sequence，每个cell可以存储一个整数，同时每一块cell有自己独立的地址。
    - 有一定数量的CPU，每个CPU中有常数个寄存器，每个寄存器有自己的名字。
    - 每个CPU可以执行以下操作：
        - 寄存器赋值：常数或者其他寄存器的值
        - 运算：整数的加减乘除
        - 比较：两个寄存器的大小
        - 内存访问：寄存器内部存储一个内存的地址，通过另一个寄存器write/read对应地址的cell的值

由于多个CPU同时对内存进行读写，所以需要考虑内存的冲突问题，根据冲突的解决方式，PRAM模型可以分为以下几种：

- CREW：Concurrent Read Exclusive Write，允许多个CPU同时读取同一个cell，但是写入时需要独占
- EREW：Exclusive Read Exclusive Write，写和读都是独占的
- CRCW：Concurrent Read Concurrent Write，允许多个CPU同时读取和写入同一个cell



