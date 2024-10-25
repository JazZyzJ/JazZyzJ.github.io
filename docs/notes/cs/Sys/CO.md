

# CO

!!! abstract 
    "本来不想写CO的笔记的，上学期计逻的笔记一直是在PPT旁边写的，但是这学期感觉有点懈怠，made桑上课还不喜欢提前发PPT，所以在这里还是整理一下上课的知识，做一点提炼和浓缩，方便自己复习和回顾。"

这学期的CO主要版块很明确：

- 计算机概要
- 计算机算术运算
- 指令
- 处理器
- 内存层次架构
- IO

这篇笔记由算术运算开始记录。


## Arithmetic

### Numbers

- Two's Complement

正数最高位为0，负数最高位为1。

正数正常表示，负数取反减1.

### Add & Sub



### Multiplication

乘法器的实现就是加法器，但是需要进行移位和多次加法。

**Version 1**

实现一个64位的乘法器，需要128位的加法器：

![co-1](/../../../../assets/pics/CO/co-1.png)

还是比较好理解的，就是模仿手写乘法的过程：非常的复杂缓慢，运用128位加法器，但是真正在相加的只有64位。

在乘数(Multiplier)的当前位为1时，将被乘数(Multiplicand)加到乘积(Product)上，然后乘数右移，被乘数左移。（这里乘数右移是因为我们每次只看乘数的最低位是否为1，在看完了之后需要看下一位，所以需要右移）

在乘数(Multiplier)的当前位为0时，乘数右移，被乘数左移。

进行64次循环后结束。

**Version 2**

![co-2](/../../../../assets/pics/CO/co-2.png)

改变思路，移动乘积，这样避免了使用较大的加法器。

这里需要一个128位的寄存器作为乘积寄存器，分为高64位和低64位，刚开始第一个乘积放在最高位。

在每次循环中，如果乘数最低位为1，将被乘数加到乘积寄存器的的高64位，然后乘积寄存器右移，乘数右移；

如果乘数最低位为0，乘积寄存器右移，乘数右移。

经过64次循环后，乘积寄存器的低64位就是我们想要的结果了。


**Version 3**

![co-3](/../../../../assets/pics/CO/co-3.png)

在先前的版本中，乘积寄存器最终会有一半的空间是浪费掉的，这一半的长度正好与乘数的长度相同，因此我们可以用128位乘积寄存器初始的右半部分来存储乘数，这样就不需要乘数寄存器了。

----

对有符号数乘法，需要存储符号位，然后进行无符号数乘法，最后根据符号位确定结果的符号。

但是注意

!!! warning

    **乘法不能用补码计算**

----

- Improved Multiplier

**Booth's Algorithm**

我不想写了，放了xyx学长的笔记：
[🔗](https://xuan-insr.github.io/computer_organization/3_arithmetic/#booths-algorithm)
### Division

**Version 1**
除法最初的版本也是模仿手写除法：

![co-4](/../../../../assets/pics/CO/co-4.png)

进行除法时，主要是通过判断余数的正负来进行的

除数初始时存放在除数寄存器的高位半边，余数寄存器初始时是存放被除数并放在低位半边的，

从余数寄存器中减去除数，如果结果为正，左移商寄存器，将最新的右位设为1，如果结果为负，将除数寄存器加回到余数寄存器，左移商寄存器，将最新的右位设为0。

这之后将除数寄存器右移一位，重复65次循环后结束。

**Version 2**

![co-5](/../../../../assets/pics/CO/co-5.png)

除数不动，被除数放在128位余数的低位半边

循环开始时，先将余数部分左移一位，然后减去除数，如果结果为正，左移余数寄存器，将最新的右位设为1，如果结果为负，将除数寄存器加回到余数寄存器，左移余数寄存器，将最新的右位设为0。

这样最后余数寄存器低半位就是商，高半位就是余数。

??? example "7/2"
    ![co-6](/../../../../assets/pics/CO/co-6.png)

- Signed Division

Reminder 需要与被除数同号


### Floating Point

32位处理器的数表示范围：

$-2^{31} 到 2^{31}-1$

浮点数的表示：

- Sign
- Significand 增加精确度
- Exponent 增加范围

二进制数的标准表示方法：

$1.xxx * 2^{yyy}$

对单精度浮点数，y有8位，x有23位

对双精度浮点数，y有11位，x有52位

![co-7](/../../../../assets/pics/CO/co-7.png)

第一位的1是一定的，因此不需要计入存储，Exponent是带有Bias的

单精度浮点数Bias为127，双精度浮点数Bias为1023

因此最后总的表示方法是：

$$
(-1)^{\text{Sign}} * (1 + \text{Significand(Fraction)}) * 2^{\text{Exponent - Bias}}
$$

??? example "0.75 和 -0.4375"

    ![co-8](/../../../../assets/pics/co/co-8.png)

    ![co-9](/../../../../assets/pics/co/co-9.png)


- Single Precision Range

指数中 00000000 和 11111111 是特殊值

当Exponent为11111111，Fraction为00000000时，表示无穷大

当Exponent为11111111，Fraction不为00000000时，表示NaN(Not a Number)

因此单精度浮点数的范围是：

Smallest Number: $1.0 * 2^{1-127} \approx 1.2 * 10^{-38}$

Largest Number: $1.111... * 2^{254-127} \approx 2 * 2^{127} \approx 3.4 * 10^{38}$

每一位都是1的时候（1.111...），表示无限接近于2


#### Floating Point Arithmetic

- Addition and Subtraction

1. Alignment

将两个数进行对齐，通常是将较小的数往大的靠近，因为如果将大的向小的对齐，损失的位数是更高的位数，损失的精度更多

2. Addition

3. Normalization

4. Rounding

??? example "0.5 + -0.4375"

    ![co-10](/../../../../assets/pics/CO/co-10.png)



- Multiplication

相对简单，只需要将指数相加，尾数相乘即可。

除法也是类似的



## Instruction

Outline:

- 计算机硬件的操作 Operation
- 计算机硬件的操作数 Operand
- 有符号数和无符号数 Signed and Unsigned Numbers
- 指令的表示 Instruction Representation
- 逻辑操作 Logical Operations
- 决策指令 Making Decisions
- 计算机对过程的支持 Procedure Support
- 指令的寻址 Instruction Addressing

指令->Statement
指令集->Syntax

### Operation

在RISC-V中，每一条指令只支持一个操作

### Operands

在算术运算中，操作数只能是寄存器，也就是我们做运算时，一定要先把数据存放到寄存器中，再对两个寄存器进行运算。

RISC-V中的寄存器是 ```32 x 64-bit``` 的Register File(实验中实现的是32 x 32-bit)

64bit是double word，32bit是word

32个寄存器：

![co-11](/../../../../assets/pics/CO/co-11.png)

#### Memory Operand

Main Memory用来存储复杂的数据，在进行算术运算时，需要```load```将数据从Memory中加载到寄存器中，得到的结果也需要```store```回Memory中。

Memory is byte addressed: 每个地址都是一个8-bit的byte，访问时是按照byte访问的。

- Endian

大小端：就是指数据在内存中存储的顺序。

??? note "Big or Little Endian"

    ![co-12](/../../../../assets/pics/co/co-12.png)

- Word Alignment 

RISC-V中，不要求进行地址对齐，但是对齐的情况是更好的

一个word是四个字节，因此对齐的起始地址一定是4的倍数，也就是0，4，8……，这些地址的二进制码都是末端两个bit为0的，而如果按照halfword访问，地址的末端一个bit为0即可。

??? example "Memory Alignment"

    ![co-13](/../../../../assets/pics/co/co-13.png)


下面是一个例子讲解怎样进行一次运算：讲解的例子都是按照double word（8 bytes 64-bit）进行操作的。

```c
    A[12] = h + A[8]
    //h in x21, base address of A in x22
```


```
    ld x9, 64(x22) 
    add x9, x21, x9
    sd x9, 96(x22)
```

```ld```指令的64是偏移量（offset），x22是基地址，x9是目标寄存器

因为我们的基址是x22，第八个double word的地址就是8 * 8 = 64，所以偏移量是64

同理在```sd```指令中，96是偏移量


#### Immediate Operand

如果说一个常数存在于一个地址中，那么在对一个寄存器增加一个常数时，就需要使用```ld```指令，将常数从Memory中加载到寄存器中，再进行加法运算。

为了减少指令个数（不使用ld指令），RISC-V中引入了立即数（Immediate），立即数是直接编码在指令中的，因此不需要再从Memory中加载。

```
    ld x9, AddressConstant4(x3) // x9 = constant 4
    add x22, x22, x9

    ||

    addi x22, x22, x9
```

### Representation of Instructions

所有的指令在机器中都是用二进制表示的，被称作Machine Code机器码

我们将x0-x31寄存器映射到0-31这些数字，因此寄存器的名字就变成了0-31

RISC-V中，指令的长度是固定的，32-bit

