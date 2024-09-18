这里是一些我在开始CO_2024后开始记录的Verilog笔记，可能不是太系统，但是我会尽量把我学到的东西记录下来。

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


