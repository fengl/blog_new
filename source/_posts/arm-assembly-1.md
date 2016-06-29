---
title: 学习ARM汇编 - 寄存器（一）
date: 2016-05-20 10:41:07
tags: arm, assembly
---

工作需要，学习一下ARM汇编，以下内容主要来摘自于网络，主要翻译、摘取参考中的几个地址。

### 寄存器
ARM处理器有32个寄存器，每个32位。在用户模式下，可以访问16个寄存器，这个是现阶段主要要了解的主要内容。

这16个寄存器分别叫做r0-r15，其中最后4个比较特殊：
- r12: IP (Intra-Procedure), 栈寄存器。该寄存器被链接器当作暂存寄存器，在不同程序间调用时使用。每个程序在返回时不可以修改该寄存器的值。
- r13: SP (Stack Pointer), 栈指针。这里的栈是一块用于存储局部函数（local function-specific storage）的存储区域，当函数返回时，该存储区域被回收。该寄存器指向堆栈的顶端。如果需要在栈上分配一块空间，需要对该寄存器进行减操作。分配32位的空间，需要将该寄存器减4。
- r14: LR（Link Register), 链接寄存器。该寄存器存储子过程的返回值。当调用一个子过程时，LR的值为程序计数器。
- r15: PC (Program Counter), 该寄存器存储了当前正在执行的内存地址。

另外，还有一个寄存器，叫状态寄存器，CPSR(Current Program Status Register), 该寄存器保存了一些标志，如负值、零、进位等。

在汇编中，这是所有可以访问的变量。

move指令：

mov r0, r1                ; r0 = r1

mov r0, r1, lsl #2        ; r0 = r1 \<\< 2

mov r0, r1, lsr #3        ; r0 = (int) (r1 \>\> 3)

mov r0, r1, lsl r2        ; r0 = (int) (r1 \<\< r2)

#### 从内存加载数据
ldr r0, .L3               ; 从地址标签为.L3的位置拷贝内容给r0,  r0 = \*(.L3)

ldr r0, [r1]             ; r0 = \*(r1)

ldr r0, [r1, #4]         ; r0 = \*(r1 + 1), 一次移动32位

ldr r0, [r1, r2]         ; r0 = \*(r1 + (r2/4)), 以一个寄存器为基准进行偏移

ldr r0, [r1, -r2]        ; 如上一条，也可以是负的

ldr r0, [r1, r2, lsl #4] ; r0 = \*(r1 + ((r2 \<\< 4)/4)), r1加上r2逻辑左移4位之后赋值给r0

lsl,  logical shift left

lsr, logical shift right

asl, arithmetic shift left

asr, arithmetic shift right

ror, rotate right

ldr r0, [r1], #4         ; like a++,    r0=\*(r1), r1=r1+1

ldr r0, [r1, #4]!        ; like ++a,    r1=r1+4, r0=\*(r1)

ldr是从内存读数据到寄存器，而str是从寄存器存储到内存

str r0, [r1]             ; \*(r1) = r0

str r0, [r1], #4         ; \*(r1) = r0, r1=r1 + 1

str r0, [r1], r2         ; \*(r1) = r0, r1 = r1 + r2

一知半解，如有不妥，后续更新……












---- 
### 参考
1. [http://www.eggwall.com/2011/09/android-arm-assembly-registers-memory.html](http://www.eggwall.com/2011/09/android-arm-assembly-registers-memory.html)