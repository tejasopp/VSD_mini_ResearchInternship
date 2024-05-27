# RISC-V Instructions

## ISA
ISA stand for **Instruction Set Architechture** . RISC-V is an open standard instruction set architecture (ISA) based on established **reduced instruction set computer** (RISC) principles.
ISA is an abstract interface between the hardware and the lowest level software that encompasses all the information necessary to write a machine language program that will run correctly. 
Above this machine language is assembly language that we humans can read. Each catagory of RISC-V instructions is associated with **constructs** that appear in programming languages.

## Assembly language and Machine language
**Assembly language** is a low-level programming language that is closely related to the machine code instructions specific to a computer architecture. 
It provides a way to write human-readable code that can be translated directly into machine language. Assembly language uses mnemonics, 
which are symbolic names for instructions and registers, making it easier to write and understand than raw binary code.

**Machine language** is the set of binary instructions that a computer's CPU can execute directly. 
It is the most fundamental level of programming and is specific to a given processor architecture. 
Machine language instructions are composed of binary digits (0s and 1s) and are executed directly by the hardware.

![Screenshot 2024-05-27 170027](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/86bff8e8-ac50-481e-9d0e-53a81f6499b4)

## What is a Register?
**Register** refers to a small, fast storage location within the CPU that is used to hold data and instructions that are currently being processed. 
Registers are crucial components in the architecture of the CPU as they allow for quick access to frequently used values and instructions.
The size of a register in the RISC-V architecture is 64 bits; groups of 64 bits occur so frequently that they are given the name **doubleword** in the RISC-V architecture.
Another popular size is a group of 32 bits, called a **word** in the RISC-V architecture.

## What is an Operand?
An **operand** refers to the data stored in a register that is used by a CPU instruction to perform a specific operation. There are basically two types of register operands:
1. Source Register Operands: Registers that provide the input data for an operation. Example, In the instruction **'add x1, x2, x3'**, **'x2'** and **'x3'** are source register operands.
2. Destination Register Operands: The register where the result of the operation is stored. Example, In the instruction **'add x1, x2, x3'**, **'x1'** is the destination register operand.

## Instructions
The words of a computer’s language are called **instructions**, and its vocabulary is called an **instruction set**. 
All instruction sets are quite similar, because all computers are constructed from hardware technologies based on similar underlaying principles and also because there are very few basic operations that all computers must provide
By convention, RISCV instructions are each  **1 word = 4 bytes = 32 bits**.
Divide the 32 bits of an instruction into **“fields”**. 
We define 6 *instruction formats* in RISC-V which are discussed later.

![Screenshot 2024-05-27 171007](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/fd3a30a3-5ab4-48a0-b71d-637915591279)

### Stored-program concept:
The idea that instructions and data of many types can be stored in memory as numbers and thus be easy to change, leading to stored-program concept.

## Types of Instruction formats in RISC-V 
### 1. R-Format Instructions
R-Format instructions are divided into 6 fields which are:
1. Opcode (7-bit) : Partially specifies operation. Eg, R-types have opcode  **0b0110011**.
2. rd (5-bit) : **“destination register”** — receives the result  of computation.
3. func3 (3-bit) : combined with opcode, this field describes what operation to perform.
4. rs1 (5-bit) : 1st operand (source register 1).
5. rs2 (5-bit) : 2nd operand (source register 2).
6. func7 (7-bit) : combined with opcode and fun3, this field describes what operation to perform.

   **Hence total bits = 7 + 5 + 3 + 5 + 5 + 7 = 32 bits**

![Screenshot 2024-05-27 172850](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/f5ca3f3d-e035-408d-807c-3faf0f5755ab)
![Screenshot 2024-05-27 172943](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/098f84d7-9d7f-4891-8392-85cfd3284731)

As RISC-V has 32 registers, and 5 bit can represent exactly 32 fields hence rs1, rs2, and rd have 5-bits.

**Example, *add* instruction**
![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/bf6ac33f-1dac-4a3c-9bbc-1444f953f489)

Hex representation: 0x 0073 02B3
Decimal representation: 7,537,331
Called a **Machine Language Instruction**

#### All RV32 R-format instructions:

![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/d2460a62-c443-4961-86a8-55ef7ea4249b)

### 2. I-Format Instructions
#### What are immediates?
An **immediate** (often abbreviated as "imm") is a constant value embedded directly within an instruction. 
Unlike values stored in registers or memory, immediate values are hard-coded into the instruction itself.
I-format instructions deals with these immediates.

I-Format instructions are divided into 5 fields which are:
1. Opcode (7-bit) :uniquely specifies the instruction.
2. rd (5-bit) : **“destination register”** — receives the result  of computation.
3. func3 (3-bit) : combined with opcode, this field describes what operation to perform.
4. rs1 (5-bit) : specifies a register operand.
5. immediate (12-bit) : 12 bit number (**constant**). imm[11:0] can hold values in range (-2^11 , +2^11)

 **Hence total bits = 7 + 5 + 3 + 5 + 12 = 32 bits**
 ![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/23f15aa4-49b5-4a10-9482-ceb237d6a405)
 ![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/571ae84f-54c8-4ce7-911f-b51e1f568254)

 **Example, *addi* instruction**
 ![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/c2dda800-ba02-47fa-9119-2539b43adb06)

Hex representation: 0x FCE0 8793
Decimal representation: 4,242,573,203

#### All RISCV I-Type Arithmatic Instructions.

![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/ed6bdd36-481a-4c6b-a49e-f671df1923f9)

### 3. S-Format instructions
Used for **storing** data.
Store needs to read two registers, rs1 for base memory address, and rs2 for data to be stored, as well as need immediate offset.
Store don’t write a value to the register file, no rd. RISC-V design decision is move low 5 bits of immediate to 
where rd field was in other instructions – keep rs1/rs2 fields in same place.

![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/eacb302c-9bd2-4492-83e6-d06ea522a1f6)

**Example, *sw* instruction**
![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/5bba1292-a8c6-4ad0-b488-137fb9e2ca94)

#### All RV32 Store Instructions

![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/2ba41d75-ff17-4166-b37e-b9d2e4b1eb6b)

### 4. B-Format instructions
These are **Branching instructions**. We Need to specify an address to go to, also take two registers to compare. Branches typically used for loops (if-else, 
while, for).B-format is mostly same as S-Format, with two 
register sources (rs1/rs2) and a 12-bit immediate. But now immediate represents values -2^12 to +2^12-2 in 2-byte increments.

![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/1b84d4d3-e590-4f66-8fa8-1bb2cf5ea7dd)

Example,
RISCV Code:
Loop: beq x19,x10,End
 add x18,x18,x10
 addi x19,x19,-1
 j Loop
End: <target instr>
![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/c191ebc4-f0b5-4483-b57c-bba311d51395)

#### All RISC-V Branch Instructions

![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/7bb36944-1b98-4903-a7f9-c4045cd05064)

### 5. U-Format instructions
1. These are **"Upper Immediate"** instructions.
2.  Has 20-bit immediate in upper 20 bits of 32-bit instruction word
3.  One destination register, rd
4. Used for two instructions
– LUI – Load Upper Immediate &
 AUIPC – Add Upper Immediate to PC
![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/b08bebab-806c-4a13-a7f4-c5153ba78877)

Example, *lui* writes the upper 20 bits of the destination with the immediate value, and clears the lower 12 bits. *lui x10, 0x87654*

### 6. J-Format instrustions
For branches, we assumed that we won’t want to branch too far, so we can specify a **change** in the PC. These changes are done with help of **jumps**
with J-format instructions. Ideally, we would specify a 32-bit memory address 
to jump to. Unfortunately, we can’t fit both a 7-bit opcode and a 32-bit address into a single 32-bit word. Also, when linking we must write to an rd register.
![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/9a7995cb-66d6-4a64-a11b-33fdab0b85fd)

**Example, *jal* instruction.**

## Summary of RISC-V Instruction Formats
![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/8c4911e6-b403-48dd-bda6-ca9802530e5d)

## Now, 32-bit instruction code for following instructions:
1. **ADD r6, r2, r1**(R-type)
    - `0000000 00001 00010 000 00110 0110011`
2. **SUB r7, r1, r2**(R-type)
    - `0100000 00010 00001 000 00111 0110011`
3. **AND r8, r1, r3**(R-type)
    - `0000000 00011 00001 111 01000 0110011`
4. **OR r9, r2, r5**(R-type)
    - `0000000 00101 00010 110 01001 0110011`
5. **XOR r10, r1, r4**(R-type)
    - `0000000 00100 00001 100 01010 0110011`
6. **SRL r16, r14, r2**(R-type)
    - `0000000 00010 01110 101 10000 0110011`      
7. **SLT r11, r2, r4**(R-type)
    - `0000000 00100 00010 010 01011 0110011`
8. **SLL r15, r1, r2**(R-type)
    - `0000000 00010 00001 001 01111 0110011`
9. **ADDI r12, r4, 5**(I-type)
    - `000000000101 00100 000 01100 0010011`
10. **LW r13, r1, 2**(I-type)
    - `000000000010 00001 010 01101 0000011`
11. **SW r3, r1, 2**(S-type)
    - `0000000 00010 00001 010 00011 0100011`
12. **BNE r0, r1, 20**(B-type)
    - `0000000 01010 00001 001 00000 1100011`
13. **BEQ r0, r0, 15**(B-type)
    - `0000000 01111 00000 000 00000 1100011`

## References

1. Book, **Computer Organization and Design (RISC-V Edition)**
2. https://inst.eecs.berkeley.edu/~cs61c/resources/su18_lec/Lecture7.pdf
3. ChatGpt










   







