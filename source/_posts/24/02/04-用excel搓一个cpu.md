---
title: 用Excel搓一个16位CPU
date: 2024-02-04 11:24:53
categories:
tags:
    - 硬件
    - 算法
---


以前的一篇[文章](https://mp.weixin.qq.com/s?__biz=MzA3NzMyNTIyOA==&mid=2651481772&idx=2&sn=13eb63165816082484cc1a0fd03dce73&chksm=84ad706fb3daf9799b692a5c8fa249e2c150732f4cfbfbfcbcad7c2442380f2b78c6823e27ee#rd) 介绍了一个大佬用差不多600行C语言代码实现了RISC-V CPU核，今天又看到GitHub上有牛人用Excel搓了一个16位CPU。

## [Excel 16-Bit CPU](https://github.com/InkboxSoftware/excelCPU)

Excel 16 位 CPU 存储库包含以下主要文件：

```plaintext
CPU.xlsx - The main spreadsheet which contains the CPU
ROM.xlsx - The ROM spreadsheet used read by the CPU when the read ROM switch is turned on
InstructionSet.xlsx - Explains the ISA of the CPU
compileExcelASM16.py - The Excel-ASM16 compiler
Excel-ASM16.xml - Markdown for the Excel-ASM16 language compatible with Notepad++
Sample Programs - Folder of sample programs for the Excel CPU
```

<!--more-->

CPU.xlsx 文件具有 16 位 CPU、16 个通用寄存器、128KB RAM 和 128x128 显示器。

必须打开迭代计算。这可以通过转到“文件”-“>选项”-“>公式”->然后“启用迭代计算”并将“最大迭代次数”设置为“1”来完成

CPU 运行 B2 中设置的时钟信号。此时钟信号将在 Excel 电子表格中重新计算的正常条件下更新。按 F9 键将重新计算电子表格。

F2单元格是复位按钮，如果设置为 true，则 F2 单元格中的重置按钮会将 PC 寄存器重置回 0。

CPU.xlsx文件中的计算机可以在自动或手动模式下进行控制。这由 J2 中的按钮控制。如果设置为 true，则当来自 B2 的时钟信号为高电平时，CPU 将执行单元格 D8 中 Fetch Unit 的覆盖插槽中指定的操作。如果为 false，则 CPU 将执行从 PC 寄存器指定的内存表中检索到的操作。

复位RAM按钮如果设置为 true，则重置 RAM 按钮会将每个内存单元重置为 0。

Read ROM如果设置为 true，则 Read ROM 按钮会将 ROM.xlsx 电子表格中内存表的值复制到 CPU.xlsx 电子表格的 RAM 表上。

CPU 的正常操作包括将 Reset Button 设置为高电平，打开并再次打开和关闭 Reset RAM 或 Read ROM 按钮（导致 RAM 复位或将 ROM 读入 RAM 表），然后关闭 Reset Button。然后将 CPU 设置为在手动模式下运行程序，或执行 RAM 中指定的程序。

CPU 设计为根据 InstructionSet.xlsx 电子表格中指定的指令集架构运行。

警告：不可能简单地尽可能快地按 F9 键，Excel 更新这么多单元格需要时间，建议等到可以看到 Excel 左下角的文本“就绪”后再继续按 F9 键。

或者，可以用 Excel-ASM16 语言编写程序并编译为 ROM.xlsx 电子表格。

Excel-ASM16 具有 24 种不同的不区分大小写的指令。 每条指令中使用了三种不同的操作数

```plaintext
REG	; refers to any of the 16 general purpose registers
E.G. R0, R1, R15 &c.

MEM	; refers to any 16-bit addressable memory unit (formatted in hexadecimal)
E.G. @0000, @F000, @FFFF, &c.

IMD	; refers to an immediate number usually 16-bits long, except in the case of ROL and ROR
    ; can be defined either in decimal or hexadecimal
E.G. #0000, $0CCC, #60340, $FF10, &c.
```

### LOAD

```plaintext
LOAD REG MEM	; loads the specified memory unit into REG
LOAD REG IMD	; load specified 16-bit immediate value into REG
LOAD REG REG	; loads memory unit at the address stored in REGB into REGA
```

### STORE

```plaintext
STORE REG MEM	; stores the value of REG to the address specified
STORE REG REG 	; stores the value of REGA into the memory unit at the address in REGB
```

### JUMP

```plaintext
JMP IMD		; sets PC to the immediate 16-bit value
JEQ IMD		; if ZF = 0, sets PC to the immediate 16-bit value
JLT IMD		; if CF = 0, sets PC to the immediate 16-bit value 
JGE IMD		; if CF = 1 or ZF = 1, sets PC to the immediate 16-bit value 
```

### TRAN

```plaintext
TRAN REG REG	; transfers value from REGA to REGB
```

### 代数指令

### ADD

```plaintext
ADD REG REG	; REGA + REGB + CF, result stored in REGA
```

### SUB

```plaintext
SUB REG REG	; (REGA - REGB) - CF, result stored in REGA
```

### MUL

```plaintext
MULT REG REG	; REGA * REGB, low 16-bit result stored in REGA, high 16-bit result stored in REGB
```

### DIV

```plaintext
DIV REG REG	; REGA / REGB result stored in REGA, REGA MOD REGB stored in REGB
```

### INC

```plaintext
INC REG	; REGA++, CF not affected
```

### DEC

```plaintext
DEC REG	; REGA--, CF not affected
```

### 位指令

### AND

```plaintext
AND REG REG	; REGA AND REGB, result stored in REGA
```

### OR

```plaintext
OR REG REG		; REGA OR REGB, result stored in REGA
```

### XOR

```plaintext
XOR REG REG	; REGA XOR REGB, result stored in REGA
```

### NOT

```plaintext
NOT REG 		; NOT REGA, result stored in REGA
```

### 移位指令

### ROL

```plaintext
ROL REG IMD	; leftwise roll of bits of REGA carried out IMD times
            ; IMD is a 4-bit value
```

### ROR

```plaintext
ROR REG IMD	; rightwise roll of bits of REGA carried out IMD times
            ; IMD is a 4-bit value
```
### 标志指令

```plaintext
CLC			; sets CF to 0
STC			; sets CF to 1 
```

### NOP

```plaintext
NOP			; does not effect any registers or memory
```

### ORG

```plaintext
ORG IMD		; sets the location of the next instruction
            ; must be further than the current length of program
```

### INC

```plaintext
INC "file.bin"	; copies the binary file into the program
```

### 编译

编写程序后，使用命令行指令对其进行编译

```powershell
py compileExcelASM16.py program.s ROM.xlsx
```

其中 program.s 是用户的程序文件，ROM.xlsx 是 ROM 电子表格。

编译成功后，可以通过翻转电子表格顶部的“读取 ROM”按钮将程序传输到 CPU.xlsx 程序中。请注意，必须打开ROM.xlsx文件才能正确更新数据。
