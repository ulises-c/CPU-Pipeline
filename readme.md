# CSEN 210 - Design a Pipelined CPU [Max Version]

## Purpose
Design a 32-bit pipelined CPU for the given SCU Instruction Set Architecture (SCU ISA). The SCU ISA is described below.
- Register file size: 64 registers, each register has 32 bits.
- PC: 32 bits
- 2’s complement is assumed.
- Instruction format: Each instruction is 32-bit wide, and consists of five fields: `opcode`, `rd`, `rs`, `rt`, and `unused`. The format is as follows.

| opcode (4 bits) | rd (6 bits) | rs (6 bits) | rt (6 bits) | unused (10 bits) |
| --------------- | ----------- | ----------- | ----------- | ---------------- |

## SCU ISA
13 Instructions are defined below
| Instruction        | Symbol         | opcode | rd  | rs  | rt  | Function            |
| ------------------ | -------------- | ------ | --- | --- | --- | ------------------- |
| No operation       | NOP            | 0000   | -   | -   | -   | No operation        |
| Save PC            | SVPC rd, y     | 1111   | rd  | y   | y   | xrd <- PC + y       |
| Load               | LD rd, rs      | 1110   | rd  | rs  | -   | xrd <- M[xrs]       |
| Store              | ST rt, rs      | 0011   | -   | rs  | rt  | M[xrs] <- xrt       |
| Add                | ADD rd, rs, rt | 0100   | rd  | rs  | rt  | xrd <- xrs + xrt    |
| Increment          | INC rd, rs, y  | 0101   | rd  | rs  | y   | xrd <- xrs + y      |
| Negate             | NEG rd, rs     | 0110   | rd  | rs  | y   | xrd <- -xrs         |
| Subtract           | SUB rd, rs, rt | 0111   | rd  | rs  | rt  | xrd <- xrs - xrt    |
| Jump               | J rs           | 1000   | -   | rs  | -   | PC <- xrs           |
| Branch if zero     | BRZ rs         | 1001   | -   | rs  | -   | PC <- xrs, if Z = 1 |
| Jump memory        | JM rs          | 1010   | -   | rs  | -   | PC <- M[xrs]        |
| Branch if negative | BRN rs         | 1011   | -   | rs  | -   | PC <- xrs, if N = 1 |
| Max                | MAX rd, rs, rt | 0001   | rd  | rs  | rt  | See note*           |

*Note: Max{memory[xrs], memory[xrs + 1], …, memory[xrs + xrt -1]}

*Note: `-` doesn't matter 