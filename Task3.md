# Task-3: Implementaion of RISC-V RV32I on Verilog and Verifying execution using waveforms.
## RV32 I
RV32I refers to the 32-bit base integer instruction set architecture (ISA) of RISC-V, an open-source hardware instruction set architecture based on established reduced instruction set computing (RISC) principles.

Block Diagram:
![181293948-beb8622c-7696-4b06-b6c9-eeab9b8ab9d3](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/0f540568-95da-41bc-bc21-16aaf39cd3d2)

## What is Single Cycle Processor?
A single cycle processor is a type of CPU design where each instruction is completed in a single clock cycle. This means that all the steps involved in executing an instruction—fetching it from memory, decoding it, performing the required operation, and writing the result back—happen within one clock pulse.
![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/4a92489d-369d-46f8-a2fb-c27e6c0e23bb)

## What is Pipelining?
We design a pipelined processor by subdividing the 
single-cycle processor into five pipeline stages. 
Thus, five instructions can execute simultaneously, 
one in each stage. Because each stage has only 
one-fifth of the entire logic, the clock frequency is 
approximately five times faster

![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/a98965dc-f49a-4fa6-8628-188a24c3e2fc)

## Starting with Functional Simulation
 - First I installed the iverilog and gtkwave using following commands:
  ```
  sudo apt-get update
  sudo apt-get install iverilog gtkwave
  ```
- Then cloning the provided files
  ```
   git clone https://github.com/vinayrayapati/rv32i
   cd rv32i
  ```
  ![Screenshot 2024-06-01 210518](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/4a23d5cb-7c1b-4199-a750-dd4da5b16329)

  - To simulate and run the verilog code , entered the following commands in the terminal.

```
 iverilog -o rv32i iiitb_rv32i.v iiitb_rv32i_tb.v
 ./iiitb_rv32i
```
![Screenshot 2024-06-01 210646](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/9889522e-bd7c-4870-8d16-f3ca5ae129bf)

## Understanding the verilog code according to the single cycle processor
### Module Declaration
```
module iiitb_rv32i(clk, RN, NPC, WB_OUT);
input clk;
input RN;
output reg [31:0] WB_OUT, NPC;

```
- Module Name: iiitb_rv32i.
-Inputs:
1. clk: Clock signal to synchronize the operations.
2. RN: Reset signal to initialize the processor.
- Outputs:
1. WB_OUT: Result of the write-back stage.
2. NPC: Next Program Counter.

### Internal Registers and Parameters
```
integer k;
wire EX_MEM_COND;
reg BR_EN;

// I_FETCH STAGE
reg[31:0] IF_ID_IR, IF_ID_NPC;

// I_DECODE STAGE
reg[31:0] ID_EX_A, ID_EX_B, ID_EX_RD, ID_EX_IMMEDIATE, ID_EX_IR, ID_EX_NPC;

// EXECUTION STAGE
reg[31:0] EX_MEM_ALUOUT, EX_MEM_B, EX_MEM_IR;

// MEMORY STAGE
reg[31:0] MEM_WB_IR, MEM_WB_ALUOUT, MEM_WB_LDM;

// Register File
reg [31:0] REG[0:31];  // 32 general-purpose registers
reg [31:0] MEM[0:31];  // Instruction memory (IMEM)
reg [31:0] DM[0:31];   // Data memory (DMEM)

// Instruction Opcodes and Functions
parameter ADD=3'd0, SUB=3'd1, AND=3'd2, OR=3'd3, XOR=3'd4, SLT=3'd5,
          ADDI=3'd0, SUBI=3'd1, ANDI=3'd2, ORI=3'd3, XORI=3'd4,
          LW=3'd0, SW=3'd1,
          BEQ=3'd0, BNE=3'd1,
          SLL=3'd0, SRL=3'd1;
parameter AR_TYPE=7'd0, M_TYPE=7'd1, BR_TYPE=7'd2, SH_TYPE=7'd3;
```
- Registers:
1. IF_ID_IR, IF_ID_NPC: Pipeline registers for Instruction Fetch stage.
2. ID_EX_A, ID_EX_B, etc.: Pipeline registers for Instruction Decode stage.
3. EX_MEM_ALUOUT, EX_MEM_B, etc.: Pipeline registers for Execution stage.
4. MEM_WB_IR, MEM_WB_ALUOUT, etc.: Pipeline registers for Memory stage.

- Parameters: Define constants for different instruction types and their function codes.

### Reset and Fetch Stage
```
always @(posedge clk or posedge RN) begin
    if (RN) begin
        NPC <= 32'd0;
        BR_EN <= 1'd0; 
        // Initialize registers
        REG[0] <= 32'h00000000;
        REG[1] <= 32'd1;
        REG[2] <= 32'd2;
        REG[3] <= 32'd3;
        REG[4] <= 32'd4;
        REG[5] <= 32'd5;
        REG[6] <= 32'd6;
    end else begin
        NPC <= BR_EN ? EX_MEM_ALUOUT : NPC + 32'd1;
        BR_EN <= 1'd0;
        IF_ID_IR <= MEM[NPC];
        IF_ID_NPC <= NPC + 32'd1;
    end
end
```
- Reset: When RN is high, the NPC and BR_EN signals are reset, and registers are initialized to specific values.
- Fetch Stage:
1. NPC is updated based on whether a branch was taken (BR_EN).
2. IF_ID_IR (Instruction Register) is loaded with the instruction at the address pointed to by NPC.
3. IF_ID_NPC is updated to the address of the next instruction.

### Instruction Memory Initialization
```
always @(posedge RN) begin
    MEM[0] <= 32'h02208300;  // add r6, r1, r2
    MEM[1] <= 32'h02209380;  // sub r7, r1, r2
    MEM[2] <= 32'h0230a400;  // and r8, r1, r3
    MEM[3] <= 32'h02513480;  // or r9, r2, r5
    MEM[4] <= 32'h0240c500;  // xor r10, r1, r4
    MEM[5] <= 32'h02415580;  // slt r11, r2, r4
    MEM[6] <= 32'h00520600;  // addi r12, r4, 5
    MEM[7] <= 32'h00209181;  // sw r3, r1, 2
    MEM[8] <= 32'h00208681;  // lw r13, r1, 2
    MEM[9] <= 32'h00f00002;  // beq r0, r0, 15
    MEM[25] <= 32'h00210700; // add r14, r2, r2
end
```
- Instruction Memory: Initializes specific memory locations with predefined instructions.

### Decode Stage
```
always @(posedge clk) begin
    ID_EX_A <= REG[IF_ID_IR[19:15]];
    ID_EX_B <= REG[IF_ID_IR[24:20]];
    ID_EX_RD <= REG[IF_ID_IR[11:7]];
    ID_EX_IR <= IF_ID_IR;
    ID_EX_IMMEDIATE <= {{20{IF_ID_IR[31]}}, IF_ID_IR[31:20]};
    ID_EX_NPC <= IF_ID_NPC;
end
```
- Decode Stage:
1. Reads source register values from the register file.
2. Extracts the immediate value from the instruction.
3. Passes the decoded values to the next stage via pipeline registers.

### Execution Stage
```
always @(posedge clk) begin
    EX_MEM_IR <= ID_EX_IR;
    case (ID_EX_IR[6:0])
        AR_TYPE: begin
            if (ID_EX_IR[31:25] == 7'd1) begin
                case (ID_EX_IR[14:12])
                    ADD: EX_MEM_ALUOUT <= ID_EX_A + ID_EX_B;
                    SUB: EX_MEM_ALUOUT <= ID_EX_A - ID_EX_B;
                    AND: EX_MEM_ALUOUT <= ID_EX_A & ID_EX_B;
                    OR : EX_MEM_ALUOUT <= ID_EX_A | ID_EX_B;
                    XOR: EX_MEM_ALUOUT <= ID_EX_A ^ ID_EX_B;
                    SLT: EX_MEM_ALUOUT <= (ID_EX_A < ID_EX_B) ? 32'd1 : 32'd0;
                endcase
            end else begin
                case (ID_EX_IR[14:12])
                    ADDI: EX_MEM_ALUOUT <= ID_EX_A + ID_EX_IMMEDIATE;
                    SUBI: EX_MEM_ALUOUT <= ID_EX_A - ID_EX_IMMEDIATE;
                    ANDI: EX_MEM_ALUOUT <= ID_EX_A & ID_EX_B;
                    ORI: EX_MEM_ALUOUT <= ID_EX_A | ID_EX_B;
                    XORI: EX_MEM_ALUOUT <= ID_EX_A ^ ID_EX_B;
                endcase
            end
        end
        M_TYPE: begin
            case (ID_EX_IR[14:12])
                LW: EX_MEM_ALUOUT <= ID_EX_A + ID_EX_IMMEDIATE;
                SW: EX_MEM_ALUOUT <= ID_EX_IR[24:20] + ID_EX_IR[19:15];
            endcase
        end
        BR_TYPE: begin
            case (ID_EX_IR[14:12])
                BEQ: begin 
                    EX_MEM_ALUOUT <= ID_EX_NPC + ID_EX_IMMEDIATE;
                    BR_EN <= (ID_EX_IR[19:15] == ID_EX_IR[11:7]) ? 1'd1 : 1'd0;
                end
                BNE: begin 
                    EX_MEM_ALUOUT <= ID_EX_NPC + ID_EX_IMMEDIATE;
                    BR_EN <= (ID_EX_IR[19:15] != ID_EX_IR[11:7]) ? 1'd1 : 1'd0;
                end
            endcase
        end
        SH_TYPE: begin
            case (ID_EX_IR[14:12])
                SLL: EX_MEM_ALUOUT <= ID_EX_A << ID_EX_B;
                SRL: EX_MEM_ALUOUT <= ID_EX_A >> ID_EX_B;
            endcase
        end
    endcase
end
```
- Execution Stage:
1. Performs arithmetic, logic, memory, branch, and shift operations.
2. Uses the case statement to differentiate between different instruction types and execute them accordingly.

### Memory Stage
```
always @(posedge clk) begin
    MEM_WB_IR <= EX_MEM_IR;
    case (EX_MEM_IR[6:0])
        AR_TYPE, SH_TYPE: MEM_WB_ALUOUT <= EX_MEM_ALUOUT;
        M_TYPE: begin
            case (EX_MEM_IR[14:12])
                LW: MEM_WB_LDM <= DM[EX_MEM_ALUOUT];
                SW: DM[EX_MEM_ALUOUT] <= REG[EX_MEM_IR[11:7]];
            endcase
        end
    endcase
end
```
- Memory Stage:
1. Handles memory read (LW) and write (SW) operations.
2. Moves data from the execution stage to the write-back stage via pipeline registers.

### Write Back Stage
```
always @(posedge clk) begin
    case (MEM_WB_IR[6:0])
        AR_TYPE, SH_TYPE: begin 
            WB_OUT <= MEM_WB_ALUOUT;
            REG[MEM_WB_IR[11:7]] <= MEM_WB_ALUOUT;
        end
        M_TYPE: begin
            case (MEM_WB_IR[14:12])
                LW: begin
                    WB_OUT <= MEM_WB_LDM;
                    REG[MEM_WB_IR[11:7]] <= MEM_WB_LDM;
                end
            endcase
        end
    endcase
end
```
- Write Back Stage:
1. Writes the result of ALU operations or loaded data back to the register file.
2. Updates the WB_OUT with the data being written back.

### Summary
The module represents a simple pipelined RISC-V processor with the following stages:

- Instruction Fetch (IF): Fetches the instruction from the instruction memory.
- Instruction Decode (ID): Decodes the instruction and reads register values.
- Execution (EX): Executes the instruction using the ALU and other execution units.
- Memory (MEM): Performs memory operations (load/store).
- Write Back (WB): Writes the result back to the register file.
The module uses pipeline registers to pass data between stages, ensuring smooth flow and separation of concerns within each stage. The reset logic initializes the necessary registers and program counter to start execution. The hardcoded instruction memory allows for testing specific instructions and sequences.

## The Output Waveforms
- For seeing the output waveform I used the following command:
  ```
  gtkwave iiitb_rv32i.vcd
  ```
- Here are the output waveforms:
  1. add r6,r2,r1
     ![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/d556356e-1048-4560-bc10-8ca2c3feddbc)

  2. sub r7,r1,r2
     ![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/0e1dcd7c-b6f8-42de-9755-2ea079167315)

  3. and r8,r1,r3
     ![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/4a21f366-5a7b-4ad8-8696-83acba8a3281)

  4. or r9,r2,r5
     ![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/18b6b35b-e8a0-4b55-95ec-81fce662a6f3)

  5. xor r10,r1,r4
     ![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/b4bd2bda-7e5c-4660-bdaa-87b3e5c56495)

  6. addi r12,r4,5
     ![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/ea371474-1269-4e54-b431-114b8d8779c5)

  7. subi r3,r1,2
     ![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/68a5bb89-a1b2-4b27-88db-bd0670dc52e9)

  - Final All output Waveforms:
    ![Screenshot 2024-06-01 212126](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/5bd88605-5617-43dc-bcbd-be9d0614adca)

    


     


  
  
