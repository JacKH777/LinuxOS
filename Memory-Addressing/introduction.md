---
title: introduction
date: 2024-09-17
type: Other
tags: ["LinuxOS"]
description: Ch1
---
# introduction
- Intel 64 vs. IA-64
  - IA-64 跟其他都不相容
- Intel X86 Registers
  - General Purpose Registers
    - 8種 (EAX, ECX, ...)
    - 當下在執行的程式使用
  ![General Purpose Registers](../../../../../assets/images/GeneralPurposeRegisters.png)
  - Instruction Pointer
    - 接下來的程式使用，指到下一個指令的位置
  ![Instruction Pointer](../../../../../assets/images/InstructionPointer.png)
  - EFLAG Register
  - Segment Registers
  - Table Registers (System Address Registers)
  ![Table Registers](../../../../../assets/images/TableRegisters.png)
  - Control Registers
- Intel X86-64 Registers
  - 這門課主要在 32 bit CPU
  - Traditional General Purpose Registers 
    ![Traditional General Purpose Registers](../../../../../assets/images/TraditionalGeneralPurposeRegisters.png)
![Traditional General Purpose Registers2](../../../../../assets/images/TraditionalGeneralPurposeRegisters2.png)
  - Instruction Pointer 

- IA32 Real Mode vs Protected Mode
  - 兩個比較先跳過
  - Protected Mode
    - How to Switch to Protected Mode 
![alt text](<../../../../../assets/images/How to Switch to Protected Mode .png>)
  參考Table Registers (System Address Registers)

      從Real Mode 到 Protected Mode 這四個值都會用到
  ![Table Registers](../../../../../assets/images/TableRegisters.png)
- Long Mode 跳過
- Endian Order 重要
  - Depending on which computing system you use, you will have to consider the byte order in which multi-byte numbers are stored, particularly when you are writing those numbers to a file. 
  - The two orders are called Little Endian and Big Endian.
  - Little Endian 
    ![Little Endian](<../../../../../assets/images/Little Endian.png>)
    ![Little Endian2](<../../../../../assets/images/Little Endian2.png>)
  
  - Big Endian 
    ![Big Endian](<../../../../../assets/images/Big Endian.png>)
- Linux Source Code Tree Overview
![Linux Source Code Tree](<../../../../../assets/images/Linux Source Code Tree.png>)
  - 初始化在 init 的 [start_kernel](https://elixir.bootlin.com/linux/v3.9/source/init/main.c#L470)
  ![init](../../../../../assets/images/init.png)
  - process 是軟硬體的資源(Ex. CPU...)，提供給 porgram 使用
-  System Boot up
1:09 要畫圖
- x32 Calling Conventions
![linux procresslayout and stack operations](<../../../../../assets/images/linux procresslayout and stack operations.png>)

  EIP 指到 main 就會配置 main 的 stack frame, 指到 G 就會配置 G, 同時 ebp 跟 esp 也會改變
![stack frame](<../../../../../assets/images/stack frame.png>)
  - 如果在 H(int b) 中 使用 Ex. H(3), 會在 H(int b) 的 stack 下面動態新增 H(3) 的 stack
  - EIP 指到的一定是最下面的 frame stack 
- x64 Calling Conventions 略

0923
## IA32 Process Address Space Layout

### Address Space of A Process
- The total address space of a Linux process could be 4 Giga bytes.
- The address range of the first 3 Giga bytes (0x00000000 ~ 0x BFFFFFFF) is called the **user address space**.
- The address range of the fourth Giga bytes (0xC0000000 ~ 0x FFFFFFFF) is called the **kernel address space**.

![IA32 Linux Process Address Space Layout ](<IA32 Linux Process Address Space Layout .png>)
  - 右排是 virtual address (固定的)
  - 每個 process 的 program 不一樣, layout 就不一樣
  - 前 3Gb 不可能一樣 -> 轉換的 physical address 不同(除非 share memory)
  - 最後 1Gb 是一樣的 -> 轉換的 physical address 一樣

# Execution Mode of IA32
- Even though 80x86 microprocessors have four different execution states, all standard Unix kernels use only 
 **kernel mode** and **user mode**.
- Different modes represent different privileges.
- A process could be in user mode or in kernel mode, but **can not in both modes simultaneously**.

## Execution Modes vs. Address Space 
Execution Modes 在系統中運行時的權限級別, Address Space 則指的是一個進程或應用程式在記憶體中可以訪問的位址範圍 

### User Mode & User Address Space
- The following components of a process are stored in the user address space of the process: 
  - user-level functions
  - variables
  - user-level data
  - library functions
  - the heap
  - the user-level stack
- A process could access these entities when it is either in user mode or kernel mode. 

### Kernel Mode & Kernel Address Space 
- The following components are stored in the kernel address space and could be accessed only when a process (thread) is in kernel mode.
  - Kernel data
  - Kernel functions
  - each process’s kernel-level stack

## Mode Switch
- A process in user mode can not access kernel data or functions directly. In order to do so, **it must utilize a system call to change its mode to kernel mode and to get the service**.
- A process in kernel mode can access data and functions in its user address space.
- A process usually executes in user mode and switches to kernel mode only when requesting a service provided by it. When the kernel satisfied the request, it puts the process  back in user mode.  

## Kernel Threads
- Always run in kernel mode in the kernel address space.
- Not interact with users.
- Not require terminal devices, such as monitors and keyboard.
- Usually are created during system startup and killed when the system is shut down.

## Uniprocessors vs. Multiprocessing
