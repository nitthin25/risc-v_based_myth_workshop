# risc-v_based_myth_workshop

## Introduction

"RISC-V based MYTH (Microprocessor for You in Thirty Hours)” workshop offers a step-by-step learning experience in RISC-V architecture, bridging software and hardware concepts through interactive lab sessions. Starting with C programming basics, participants explore the GCC toolchain and Spike simulator, then move on to understanding number systems and assembly language. As the course progresses, learners dive into combinational and sequential logic, pipeline design, and the inner workings of a single-cycle RISC-V processor. Hands-on activities cover tasks such as decoding instructions, working with register files, implementing ALUs, and addressing control flow hazards. In the final phase, focus shifts to load/store mechanisms, memory handling, and building a CPU testbench. Spanning five days, the workshop delivers a thorough foundation in microprocessor design and verification.

## What is RISC_V

RISC-V (pronounced "RISC-five") is an open-source, royalty-free instruction set architecture (ISA) based on the principles of Reduced Instruction Set Computing (RISC). Designed for flexibility and scalability, it enables developers and organizations to build custom processors for a wide range of applications—from embedded systems to high-performance computing—without licensing restrictions. Its openness and modularity make it a significant force in democratizing hardware design and driving innovation in the semiconductor industry.

## Day-1:Introduction to RISC-V ISA and GNU compiler toolchain

### RISC-V flow

RISC-V Architecture (C) → Implementation → Layout .
Layer mapping: Application layer → System software → Hardware layer.

![Image](https://github.com/user-attachments/assets/3b99258b-64b6-480a-93f1-4098a6158f91)

### Apps to Hardware

### RISC-V instruction set architechture → RTL and synthesis of RISC-V based cpu core-picorv32 → physical design impementation of picorv32

The journey from RISC-V Instruction Set Architecture (ISA) to the physical design of a RISC-V-based CPU core like PicoRV32 involves multiple stages in digital design. It begins with the RISC-V ISA, which defines the set of instructions the CPU can execute. Using this ISA, a hardware description of the CPU—such as PicoRV32—is written in a language like Verilog (RTL design). This RTL code is then passed through a synthesis tool to convert it into a gate-level netlist. Finally, the netlist undergoes physical design implementation using tools like Qflow, where steps such as placement, routing, and layout generation are performed to prepare the design for chip fabrication. This entire process bridges the gap from abstract instruction definitions to a tangible silicon-ready CPU core.

![Image](https://github.com/user-attachments/assets/bff4cc08-954b-49af-a1ad-05d724dacaad)

## DAY-1 LAB

1)compute sum from 1 to n

```c
  #include <stdio.h>
    int main() {
    int i, sum = 0, n = 100;
    for (i=1; i <= n; ++i) {
    sum += i; }
    printf("Sum of numbers from 1 to %d is %d\n", n, sum);
    return 0;
  }
```

This is the c code for adding numbers, make a file named 1ton.c and paste this in that file.

Steps to run this

  1)gcc 1ton.c
  2)./a.out
  
![Image](https://github.com/user-attachments/assets/8c239925-ead7-4d92-90dc-0fc9079307bf)











