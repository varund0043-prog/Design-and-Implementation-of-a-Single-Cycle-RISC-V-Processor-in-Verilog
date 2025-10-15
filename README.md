# Design-and-Implementation-of-a-Single-Cycle-RISC-V-Processor-in-Verilog
PROJECT SETUP:

Open Vivado → Create New Project

Select Project Type: RTL Project

Add Sources: Skip (add later)

Choose FPGA/Board (Artix-7, Zynq-7000, etc.)

Finish Project Creation

ADD DESIGN FILES:

Create/Add Source Files:

Main RISC-V Processor Module

ALU Module

Register File (32 registers)

Control Unit

Instruction Memory ROM

Data Memory RAM

Add Constraint Files (.xdc) for pin mapping

LOAD RISC-V CODE:

Convert Assembly to Machine Code (hex)

Initialize Instruction Memory with your code:

sub x4,x1,x2

and x5,x1,x2

or x6,x1,x2

lw x7,0(x0)

sw x7,4(x0)

beq x1,x2,8

addi x8,x1,10

xor x9,x1,x2

CREATE TESTBENCH:

Write Verilog Testbench

Initialize: x1=5, x2=3, Memory[0]=0x12345678

Generate Clock and Reset Signals

Add Monitoring for registers/memory

RUN SIMULATION:

Launch Behavioral Simulation

Run for sufficient time (e.g., 500ns)

Observe Waveform for:

Register writes (x3-x9 changes)

Memory writes (address 0x4)

PC increment

ALU results

VERIFY RESULTS:

Check final register values:

x4=2, x5=1, x6=7

x7=0x12345678, x8=15, x9=6

Verify Memory[4]=0x12345678

Confirm branch not taken (x1≠x2)

IMPLEMENTATION (OPTIONAL):

Run Synthesis

Run Implementation

Generate Bitstream

Program FPGA

. Initialization Phase
1.	The simulation starts with reset = 1, initializing all memories and registers to 0.
2.	In inst_memory, the instruction memory (i_memo) is cleared using a for loop.
3.	In data_memory, all 64 memory locations are set to zero.
4.	Once reset is deasserted (reset = 0), a predefined set of RISC-V instructions are loaded into the instruction memory (i_memo[0] to i_memo[4]).
5.	Initial register values are manually set in the testbench — for example, x1 = 5, x2 = 3, and Memory[0] = 0x12345678.
6.	The Program Counter (PC) starts from 0x00000000, and the first instruction fetch occurs.
________________________________________
 2. Instruction Fetch and Decode
7.	On every clock edge, the processor fetches the instruction from instruction memory using the current PC (inst_out = i_memo[PC[7:2]]).
8.	The instruction is decoded to identify the opcode, funct3, funct7, rs1, rs2, and rd fields.
9.	The control unit sets control signals (ALUSrc, RegWrite, MemRead, MemWrite, Branch, MemtoReg, ALUOp) based on opcode type (R-type, I-type, Load, Store, Branch).
10.	The register file provides operands rd1 and rd2 for the ALU using the decoded rs1 and rs2 register numbers.
________________________________________
 3. ALU Operation Phase
11.	The ALU performs the required operation (add, sub, and, or, xor, etc.) based on ALUOp and funct fields.
12.	The ALU result is used either for register writing (R/I-type), memory access (LW/SW), or branch comparison.
13.	The Zero flag from the ALU is used to determine if a branch should be taken.
________________________________________
4. Memory Access Phase
14.	For Load (LW) instructions, the data memory reads data from the address specified by the ALU result.
15.	For Store (SW) instructions, the data memory writes the data from register rd2 into memory at the ALU result address.
16.	For all other instructions (like ADD, SUB, AND, OR, etc.), the memory stage is bypassed.
________________________________________
5. Write Back Phase
17.	If the instruction requires a register write (RegWrite = 1), the final result is written into the destination register (rd).
18.	For LW, the data read from memory is written to the destination register.
19.	For R-type and I-type, the ALU result is written to the destination register.
________________________________________
 6. Program Counter Update
20.	Normally, PC increments by 4 for the next instruction (PC = PC + 4).
21.	If a branch instruction (BEQ) is executed and the Zero flag is set, the PC jumps to the branch target address (PC = PC + immediate).
22.	In your simulation, since x1 != x2, the branch is not taken, and PC continues sequentially

