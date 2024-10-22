!!! abstract 
这里是一些我在开始CO_2024后开始记录的Verilog语法笔记，可能不是太系统，但是我会尽量把我学到的东西记录下来。

## Grammar

- Module
```verilog

module module_name(
    input wire input_name,
    output reg output_name
);
    // statements  
endmodule

```
- Always block
```verilog
always @(posedge clk) begin
    // statements
end
```
当always中的条件满足时，会执行其中的语句。
必须使用always block的情况：
1. 时序逻辑
2. 复杂到不能用assign的逻辑

```always``` 块适用于描述需要时钟边沿触发的行为或者复杂的逻辑计算，而对于简单的组合逻辑和不涉及时钟的简单操作，则 ```assign``` 语句更合适。

- Wire & Reg
```verilog
wire a;
assign a = b & c;

reg [7:0] count;
always @(posedge clk) begin
  count <= count + 1;
end
```

```wire``` :
适用于组合逻辑和模块间的连接
不储存值只表示信号的连接
用assign语句赋值


```reg``` : 
适用于需要存储状态的时序逻辑
可以存储值，通常用于描述寄存器、计数器等
用always语句赋值,沿时钟触发


- = and <=
```=``` : blocking assignment
```<=``` : non-blocking assignment      
```=``` 会在同一个时钟周期内立即赋值，而 ```<=``` 会在下一个时钟周期内赋值。




```Slice``` 模块

主要用于将一个信号分解为多个位，或者取信号的某一部分。

```verilog 
// 假设有一个32位的输入信号 Din
wire [31:0] Din;
wire [7:0] Dout;

// 从 Din 中提取第7到第0位
assign Dout = Din[7:0];

//只取第7位
assign Dout = Din[7];


```

```Utility Vector Logic``` 模块

Utility Vector Logic 模块用于基本的逻辑运算，如 AND、OR、NOT 等操作。

可以通过直接在代码中写逻辑运算，也可以使用 ```Utility Vector Logic``` 模块。

```verilog
// 假设有两个输入信号 A 和 B，分别是1位
wire A, B;
wire Res;

// 进行逻辑 AND 运算
assign Res = A & B; // 逻辑 AND

// 如果是 NOT 运算
assign Res = ~A; // 逻辑 NOT

// 其他运算如 OR、XOR
assign Res = A | B; // 逻辑 OR
assign Res = A ^ B; // 逻辑 XOR
```

```Concat``` 模块

Concat 模块用于将多个信号拼接成一个更大的信号。它在处理多个小信号组合为一个大信号时非常有用。

直接用 ```{}``` 来拼接信号
```verilog
// 假设有两个信号 A 和 B，分别是8位
wire [7:0] A;
wire [7:0] B;
wire [15:0] Dout;

// 将 A 和 B 拼接，形成16位的 Dout
assign Dout = {A, B}; // A 位于高8位，B 位于低8位

```Constant``` 模块

Constant 模块用于生成固定值的常数信号。在 Verilog 中，可以直接用参数或直接赋值来实现。

```verilog
// 直接赋值
assign Dout = 16'h1234; // 16'h1234 表示16进制的 1234

// 用参数
parameter Dout = 16'h1234;

// 生成一个4位的常数 4'b1010
wire [3:0] Const;
assign Const = 4'b1010;
```

- FSM 

有限状态机是一种数学模型，用来描述一个系统在不同状态之间的转换及其行为。它由以下几个部分组成：

- 状态集：所有可能的状态。
- 输入集：状态机根据输入决定下一步的状态。
- 状态转移规则：当前状态和输入的组合决定状态的变化。
- 初始状态：状态机启动时的状态。
- 输出：状态机可以产生输出，输出通常和当前状态或输入有关。



```verilog
module fsm_example (
    input wire clk,          // 时钟信号
    input wire reset,        // 异步复位信号
    input wire in,           // 输入信号
    output reg [1:0] state,  // 当前状态输出
    output reg out           // 状态机输出信号
);

// 状态编码
localparam S0 = 2'b00;  // 状态 0
localparam S1 = 2'b01;  // 状态 1
localparam S2 = 2'b10;  // 状态 2

reg [1:0] next_state;   // 下一个状态

// 状态转移逻辑 (组合逻辑)
always @(*) begin
    case (state)
        S0: begin
            if (in)
                next_state = S1;
            else
                next_state = S0;
        end
        
        S1: begin
            if (in)
                next_state = S2;
            else
                next_state = S0;
        end

        S2: begin
            if (in)
                next_state = S2;
            else
                next_state = S1;
        end

        default: next_state = S0;  // 任何未知状态回到 S0
    endcase
end

// 状态寄存器 (时序逻辑)
always @(posedge clk or posedge reset) begin
    if (reset)
        state <= S0;  // 复位时进入初始状态 S0
    else
        state <= next_state;  // 否则切换到下一个状态
end

// 输出逻辑 (Moore 型，只依赖于当前状态)
always @(state) begin
    case (state)
        S0: out = 0;
        S1: out = 1;
        S2: out = 0;
        default: out = 0;
    endcase
end

endmodule

```

