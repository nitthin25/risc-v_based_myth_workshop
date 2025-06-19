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

To simulate the program and verify the steps

![Image](https://github.com/user-attachments/assets/1e001a39-925a-4594-b7d6-26bbb1498e43)

![Image](https://github.com/user-attachments/assets/eeb33dc0-c44e-4d31-9da7-ddb821f0e28f)

change the format to fast

![Image](https://github.com/user-attachments/assets/de676d52-4012-4852-b15c-ed857c71f469)

![Image](https://github.com/user-attachments/assets/7e7327fc-3119-451a-8736-a41190382fcd)

![Image](https://github.com/user-attachments/assets/fe175c1e-54e7-4b04-932c-c6a96db2017a)


Simulation with spike

```
riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o 1ton.o 1ton.c        
spike pk 1ton.o
```

## Day 2: Introduction to ABI and Basic Verification Flow

### Introduction to ABI

An Application Binary Interface (ABI) specifies the detailed rules that govern how compiled code and software components, such as libraries, communicate and function together at the binary level. It acts as a blueprint that ensures independently developed modules can integrate and operate smoothly.

This includes aspects such as:

How functions are called and parameters are passed (calling conventions),

Which registers are used and how they’re preserved,

How memory is organized and how data types are represented.

The typical flow from software to hardware follows this path:
Application Layer → Standard Libraries → Operating System → Instruction Set Architecture (e.g., RISC-V, ARM, x86) → Hardware (RTL design).

![Image](https://github.com/user-attachments/assets/19db819a-67bb-46db-ae18-082abc8bb3e1)

## Memory Allocation for Double Words

There are only 32 registers available in RISC-V architecture

On RV64 (XLEN=64), double words (64-bit values) can be:

1)stored directly in a 64 bit register:

Stored in memory across eight consecutive bytes (m[0] holds LSB, m[7] holds MSB in little-endian order).

Were the RISC-V is based on “LITTELE-ENDIAN ” memory addressing

For an array of three double words:
Bytes 0–7   → First double word Bytes 8–15  → Second double word Bytes 16–23 → Third double word

![Image](https://github.com/user-attachments/assets/190d6c99-c57a-40b2-acd0-7c39aae861d3)

## DAY-2 Labs

### C Program with Assembly Function

Flow of a sum of numbers in c program

![Image](https://github.com/user-attachments/assets/3d27a755-d264-414f-a6bd-a0384bb4209d)

open the gedit by creating the file name 1_to_9.c

![Image](https://github.com/user-attachments/assets/568e8842-0d43-4c89-b590-127d15ded280)

The C program code :

``` c
#include <stdio.h>

extern int load(int a, int b);

int main() {
    int result = 0;
    int count = 9;
    result = load(0x0, count + 1);
    printf("Sum of number from 1 to %d is %d\n", count, result);
}
```

To perform load.s:

```
gedit load.s &
```

By accomplishing the above bash code we would get the assembly instructions of the C code-

```
.section .text
.global load
.type load, @function

load:
    add     a4, a0, zero     // Initialize sum register a4 with 0x0
    add     a2, a0, a1       // Store count of 10 in register a2. Register a1 is loaded with 0xa (decimal 10) from main
    add     a3, a0, zero     // Initialize intermediate sum register a3 by 0
loop:
    add     a4, a3, a4       // Incremental addition
    addi    a3, a3, 1        // Increment intermediate register by 1
    blt     a3, a2, loop     // If a3 is less than a2, branch to label named <loop>
    add     a0, a4, zero     // Store final result to register a0 so that it can be read by main program
    ret

```
### New C Program With Function Call=

Using cat command-
```
cat 1_to_9.c
cat load.s
```
To compile and assemble-

```
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o 1to9_custom.o 1to9_custom.c load.S
```
to simulate spike function-

```
spike pk 1to9_custom.o
```
![Image](https://github.com/user-attachments/assets/ea3d7d6d-8dfe-49df-b080-0755c64503ac)

![Image](https://github.com/user-attachments/assets/a30f5e7c-ddbd-4be7-868f-a8f347822453)

to dissasemble-
```
riscv64-unknown-elf-objdump -d 1to9_custom.o | less
```

![Image](https://github.com/user-attachments/assets/53a25db5-4386-481f-9729-289ed71514f2)

## Program On RISC-V CPU-

Cloning-

```
git clone https://github.com/kunalg123/riscv_workshop_collaterals.git
cd riscv_workshop_collaterals/labs
```

Inspect and Prepare Files-

```
ls -ltr
vim picorv32.v       
vim testbench.v      
vim rv32im.sh
```
![Image](https://github.com/user-attachments/assets/9a38f801-8aa2-4ea2-8928-48eb94484604)

Snippet of rv32im.sh -

![Image](https://github.com/user-attachments/assets/9c4820a2-3247-4d6a-b47d-1b41fe6e2513)

### Run Verification Script-

```
chmod +x rv32im.sh
./rv32im.sh
```

## Day3: Digital Logic with TL-Verilog and Makerchip

### Logic gates-

A logic gate is a basic component of digital electronics that functions like an electronic switch, taking binary inputs (0 or 1) and producing a binary output according to a particular logic rule. These gates are the physical implementation of Boolean algebra and serve as the foundation for building more advanced digital systems, including computers and various electronic devices. Using basic gates, we can construct more complex ones such as NAND, NOR, XOR, and XNOR.

### Combinational Logic

A combinational logic circuit is a digital system in which the output is solely determined by the present state of the inputs, without any regard for previous input values. It does not store any data or have memory elements. Such circuits are constructed using logic gates like AND, OR, NOT, NAND, NOR, and others.

### Sequential Logic-

A sequential logic circuit is a digital circuit whose output depends not only on the present input but also on the past inputs and the internal state of the circuit. It incorporates memory elements, like flip-flops or latches, to store the previous state, allowing the circuit to "remember" past inputs. This contrasts with combinational logic, where the output is solely determined by the current inputs.

### Storage elements: Flip-Flops

In digital electronics, a flip-flop is a fundamental memory circuit that can store a single bit of information (0 or 1) and can be toggled between these two states. It's like a basic switch that can be flipped on or off, retaining that state until an external signal (like a clock pulse) triggers a change. Flip-flops are bistable devices, meaning they have two stable states. They can hold a specific binary value (0 or 1) until a control signal causes them to change.

### Validity

Validity is the device meets the needs and requirements of its intended users and the intended use environment. In a chip there are maximum things that are not useful and sit idle in the chip, but the power still goes in them, which is a wastage of electricity. Validity fixes this problem by eliminating the non-useful gates.


## Day 3 Labs-

### Combinational Calculator-

![Image](https://github.com/user-attachments/assets/05152c50-4eea-4406-af02-1f55de3d06e5)

### Sequential Calculator-

![Image](https://github.com/user-attachments/assets/efe538b9-a82c-4941-a4e4-e0fdc0745d8d)

### Cycle Calculator-

![Image](https://github.com/user-attachments/assets/c1f6aafb-3cd3-4ded-94a6-e8802c6033a6)

### Total Distance-

![Image](https://github.com/user-attachments/assets/6683e4a0-f1e2-4334-8e65-9aa908b2e554)

### validity-

![Image](https://github.com/user-attachments/assets/ab0f1f22-b72a-426d-b81c-e607f8b4e524)

### cycle calculator with vailidity condition-

![Image](https://github.com/user-attachments/assets/cf7415ad-ac0a-4fed-aec1-4c9b61f29db9)


## Day 4: Basic RISC-V CPU Micro-architecture

### Micro-architecture of Single-Cycle RISC-V CPU

single-cycle RISC-V CPU → instruction completes in one clock cycle—fetch, decode, execute, memory access, and write-back stages all occur in parallel hardware structures.

![Image](https://github.com/user-attachments/assets/1302ede8-b7a2-4413-9927-74a26441b327)

### Implementation Plan and Lab for PC

![Image](https://github.com/user-attachments/assets/f95b1f83-7f24-4d8d-8542-8b01a3d34d7d)

### Lab for Instruction Fetch Logic

![Image](https://github.com/user-attachments/assets/89fb5ec7-3ebd-4bfa-a803-3247af35d9fc)

### Lab for Instruction Immediate Decode Logic for RV-ISBUJ-

![Image](https://github.com/user-attachments/assets/35c1f167-0a07-41ff-93a6-06aaacf666bc)

###  Register File Read

![Image](https://github.com/user-attachments/assets/d0ec26a1-c643-4d61-a204-a34299e08b86)

### ALU Operations for ADD/ADDI-

![Image](https://github.com/user-attachments/assets/fa4b4448-1f2d-4e1f-83e4-acb964fbedcc)

### Register File Write

![Image](https://github.com/user-attachments/assets/80f6326d-8ec3-4c55-95a0-5323e1607a9e)

### Implementing Branch Instructions

![Image](https://github.com/user-attachments/assets/829a54bf-ac5a-4bd6-8c8f-bff1fabef45d)
























