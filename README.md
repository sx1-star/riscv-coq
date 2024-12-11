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
UStatus、UIE、UTVec、UScratch、UEPC、UCause、UTVal、UIP、FCsr、FFlagsFRM、FCSR、cycle、time、instret（已定义）  
hpmCounter3 ~ hpmCounter31（未定义）  

CSRField文件：各个CSR的字段 -- 用于配置和监控处理器的运行状态 及其访问权限 


# 指令
execute文件：通过模式匹配来处理不同的指令  
