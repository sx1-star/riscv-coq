# riscv-coq
# 项目介绍 
将RISC-V 指令集架构的形式规范用 Coq 证明助手来实现，利用Coq强大的逻辑验证能力来确保RISC-V指令集的正确性无误  
RISC-V 语义的 Haskell 实现（用 Haskell 编程语言来形式化描述和模拟 RISC-V 指令集架构） &&  使用 hs-to-coq 工具将 Haskell 代码转换为 Coq 代码  
支持RV32I（32位整数指令集架构）和RV64I以及与A扩展和M扩展的任意结合  
A扩展：引入原子指令 ， M扩展：引入整数乘法、除法指令  
# 目录结构
![9616405bc1d47e5f617edc106d632cae](https://github.com/user-attachments/assets/3ebc1651-c7e1-46d7-9411-65d4243d423e)
deps：存放项目依赖的库  
src/ricv：存放RISC-V实现的具体规范文件  
export：存放导出的文件  

# 寄存器
通用寄存器（32个），系统寄存器（M模式、S模式、U模式）  
（CSR文件）
M模式下的系统寄存器：  
MHartID、MISA、MStatus、MTVec、MEDeleg、MIDeleg、MIP、MIE、MCycle、MInstRet、MCounterEn、MScratch、MEPC、MCause、MTVal、MHPMCounter3 到 MHPMCounter31（已定义）  
mvendorid、marchid、mimpid、mconfigptr、mtinst、mtval2（未定义）  

S模式：  
SStatus、SEDeleg、SIDeleg、STVec、SIP、SIE、SCounterEn、SScratch、SEPC、SCause、STVal、SAtp（已定义）  
senvcfg、scontext（未定义）  

U模式：  
UStatus、UIE、UTVec、UScratch、UEPC、UCause、UTVal、UIP、FCsr、FFlags、FRM、FCSR、cycle、time、instret（已定义）  
hpmCounter3 ~ hpmCounter31（未定义）  

CSRField文件：各个CSR的字段 -- 用于配置和监控处理器的运行状态 及其访问权限 


# 指令
execute文件：通过模式匹配来处理不同的指令  

# U模式下的寄存器
1.系统寄存器  
fflags ： 浮点数累积异常  既被定义在CSR类型，又被定义在CSRField类型  
在risc-v的架构设计中，它是fcsr（浮点数控制和状态寄存器）的一部分，在低五位，用来表示浮点数运算的状态标志（上溢、下溢等），既可以作为独立的寄存器被访问，也可以作为字段被访问  
frm同理（fcsr[7:5]）表示浮点数舍入模式  
均为URW（U模式具有读写权限）  
WARL和RW的区别：前者为写任意、读合法，即可以写入任意值，但会对其进行合法检查，并将其转化为合法值，这样从该寄存器读取到的均为合法值；后者不会进行合法检查  
2.通用寄存器  
x0到x31：32个64位通用寄存器，都具有别名，书写汇编时可以直接使用别名  
每个寄存器都是一个连续的存储单元，没什么特殊的字段划分，每一位都可以用来存储数据  
x0 -- zero ：寄存器的内容全是0，用作源寄存器或目的寄存器  
x1 -- ra ：链接寄存器，保存函数返回地址  
x2 -- sp ：栈指针寄存器，指向栈的地址  
x3 -- gp ：全局寄存器  
x4 -- tp ：线程寄存器，保存进程控制块  
# 指令
1.加载指令  
lb rd, offset（rs1） : 把rs1 + offset地址处的数据从内存加载到通用寄存器  
eg：若该地址处数据为12（1100），则rd为0.....0 00001100  
2.逻辑左移指令  
sll rd rs1, rs2 : 将rs1中的值左移rs2位并将结果存储到rd中（逻辑左移：最高位丢弃，低位补0）  
eg：rs1 为 x5，x5中的值为15（F），rs2值为4，rd中值即为0x0...0F0  
3.与操作指令  
and rd rs1,rs2 ： rs1和rs2中的两个操作数按位与，得到的结果存入rd中  
eg：rs1为x5，值为0x0...0F；rs2为x6，值为0x0...03，rd中值即为0x0...03  
4.加法指令  
add rd rs1, rs2 : 
