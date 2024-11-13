# Single-Cycle Processor

## Intro

指令执行的步骤概述：

- 最开始的两步是相同的：

1. Fetch 从指令内存中拿出指令
   
  
2. Decode and Read Operands 解码并读寄存器
     - 在进行指令解码完成前就可以读取数据，因为数据位置是固定的，并且读数据不会破坏数据，所以无论是否使用，都会先拿出对应的register operands。

3. Executive Control

    所有的指令都会经过ALU，根据指令的类型进行不同的ALU操作：

    
    - 内存引用指令：Memory Reference 
    - 算术逻辑指令：Arithmetic and Logic 
    - 条件分支指令：判断两数是否相等

4. Memory Access 
 
    通过load和store指令访问数据

    - 内存引用指令：访问内存进行读写
    - 算术逻辑指令/加载指令：将内存或者寄存器中的数据写入寄存器
    - 条件分支指令：根据比较结果更新PC：PC+4 或到指定目标地址

5. Write results
    
6. Update PC

!!! note "simple processor"

    ![co-40](/../../../../assets/pics/co/co-40.png)

    三个红色圆圈是多路选择器，因为需要对不同的数据来源进行选择，比如ALU中的输入既可以是R型指令中的两个源寄存器，也可以是I型指令中的一个源寄存器和一个立即数。

!!! note "with control"

    ![co-41](../../../../assets/pics/co/co-41.png)


## Clock Methodology

## Datapath

构建数据通路时，我们关心的是指令中的存储数据而不是控制信号。

### Instruction Fetch

![co-42](/../../../../assets/pics/co/co-42.png)

64bit寄存器，需要64个D触发器。

### R Inst

![co-43](/../../../../assets/pics/co/co-43.png)

- Register File: 寄存器堆，用于存储所有寄存器
    - 进行选择的wire是5位，因为寄存器有32个。
    - 读取数据只需要进行选择，写入数据需要受`RegWrite`信号控制  
- ALU: 进行算术逻辑运算
    - 有一个四位的MUX进行控制，但事实上只需要三位，有一位是浪费的（但我们必须使用四位，因为MUX的输入端口数必须是2的幂次）
  
### Load Store Inst

除了R型指令用到的两个元件，还需要两个

![co-44](/../../../../assets/pics/co/co-44.png)

- Data Memory: 数据内存,不同于Register File，Data Memory同时具有读写控制
- Imm Gen: 从 32 位指令中提取出与立即数相关的位，将这些位按正确的顺序拼接起来，同时对其符号扩展至 64 位

### Branch Inst

ALU 中的 zero 作用是判断是PC+4还是branch target address。

![co-45](/../../../../assets/pics/co/co-45.png)

- 寄存器用于存放两个被比较的源操作数，进入ALU进行比较
- ALU：第一个ALU的作用是进行比较，并且有一个zero用于判断是否相等；第二个ALU用于计算branch target address
- Shift left 1:根据之前学到的，在进行branch target address计算时，我们所需的offset在立即数中是真实值的一半，因此对于Imm Gen输出的立即数，我们需要进行左移一位的操作乘2

### Compose

- 每个数据通路都是单周期的

!!! note "datapath"

    ![co-46](/../../../../assets/pics/co/co-46.png)

!!! note "All types' datapath"

    === "R-type"

        ![co-47](/../../../../assets/pics/co/co-47.png)

        R-type 的指令是完全建立在Register File上的，没有与内存的交互，使用R指令一定是在内存读取后进行的。

    === "I-type"

        ![co-48](/../../../../assets/pics/co/co-48.png)

    === "S-type"

        ![co-49](/../../../../assets/pics/co/co-49.png)

    === "B-type"

        ![co-50](/../../../../assets/pics/co/co-50.png)


    === "J-type"

        ![co-51](/../../../../assets/pics/co/co-51.png)

## Control Unit

只有7+4根信号

实际上只需要处理八个信号

!!! note "8 signals"

    ![co-52](/../../../../assets/pics/co/co-52.png)



#### ALU Control

采用两级解码，根据opcode就可以判断7根信号线的值，这时候 ALU 再看funct3和funct7就可以判断出剩下的4根信号线的值。

![co-53](/../../../../assets/pics/co/co-53.png)

对于我们常用的几种类型的指令，ALU的功能：

- R-type: 取决于opcode
- Branch: sub
- Load/Store: add


# Pipelining Process

流水线Pipeline的本质是提高吞吐量，通过将指令的执行过程分解为多个阶段，每个阶段在不同的时钟周期内执行，从而在每个时钟周期内完成多条指令的执行。

在RISC-V中，流水线Pipeline的实现方式是：

!!! note "pipeline"

    - IF: Instruction Fetch 从指令内存中取出指令
    - ID: Instruction Decode 解码指令
    - EX: Execute 执行指令
    - MEM: Memory Access 访问内存
    - WB: Write Back 写回寄存器

    ![co-54](/../../../../assets/pics/co/co-54.png)

    图形左半边阴影是写入，右半边阴影是读取，全阴影是都有（这里假设在一个时钟周期内，元件的前半个周期可以进行写操作，后半个周期可以进行读操作）



但是不是所有指令都需要执行所有阶段的指令的，比如load和store指令就不需要执行EX和MEM阶段。

性能方面：理想条件下

$$
\text{Time between instructions}_{pipelined} = \frac{\text{Time between instructions}_{non-pipelined}}  {\text{Number of stages}}
$$

## Hazards

### Structure Hazards
流水线问题导致存储器或者ALU访问冲突（内部资源使用冲突）

### Data Hazards

需要等待上一条指令完成数据写入或者读取完成才能进行后续操作




