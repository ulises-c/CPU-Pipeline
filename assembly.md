# Project Max - Assembly Code - Phase 1
CSEN 210 - Professor Yan Cui - Fall 2024
Group members: [John Barckley](jbarckley@scu.edu) | [Ulises Chavarria](uchavarria@scu.edu) | [Nishant Misal](nmisal@scu.edu)

## Objective
Without using labels and the table of instructions find the max within a list of numbers.

## SCU ISA
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


## Assembly code
```nasm
SVPC x1, x0             ; Save current PC in x1 for later use
INC x2, x1, 0x39        ; Set x2 as a jump target for loop end
INC x3, x1, 0x49        ; Set x3 as a branch target when max needs updating
INC x4, x1, 0x59        ; Set x4 as a branch target for loop completion
LD x5, x7               ; Load counter (list length) into x5
INC x7, x7, x9          ; Move x7 to the start of list (next element)
INC x7, x0, 0x80000000  ; Initialize x7 with the smallest possible integer as initial max
SVPC x8, x9             ; Set x8 as the loop return address
LD x10, x7              ; Load current element (pointed by x7) into x10
SUB x11, x10, x7        ; Calculate x11 = x10 - x7 to compare x10 and x7
BRN x3                  ; If x11 < 0, skip the update as x7 is greater than or equal to x10
INC x7, x10, 0x0        ; Update max in x7 if x10 > x7
INC x7, x7, x9          ; Move to the next element in the list
INC x5, x5, 0xFFFFFFFF  ; Decrement counter x5 (remaining list length)
BRZ x4                  ; If x5 is zero, jump to end (x4)
J x8                    ; Jump back to start of the loop (x8)
ST x7, x12              ; Store final max value in memory at x12
```