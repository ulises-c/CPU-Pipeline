# Project Max - Assembly Code
CSEN 210 - Professor Yan Cui - Fall 2024
Group members: [John Barckley](mailto:jbarckley@scu.edu) | [Ulises Chavarria](mailto:uchavarria@scu.edu) | [Nishant Misal](mailto:nmisal@scu.edu)

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

## C Code
```c
 int findmax(int arr[], int size){
    int max = arr[0]; 
    for(int i = 1; i < size; i++){
        if (arr[i] - max >= 0){
            max = arr[i]; 
        }
    }
    return max;
}
```

## Assembly Code
This program finds the max integer in an array “arr” of length “size”

### Assumptions and Restrictions
1. x10 = &arr[0]
2. x11 = size
3. At any given instruction, the PC holds the address of the next instruction
4. No labels allowed

```asm
SVPC x1, 0x0
INC x12, x1, 0x 18  # x12 = &Loop
INC x13, x1, 0x30   # x13 = &LoopIncrement
INC x14, x1, 0x3c   # x14 = &LoopEnd
INC x5, x0, 0x1     # x5 => i = 1
LD x6, x10          # x6 => max = arr[0]
INC x7, x10, 0x4    # x7 = &arr[i] => aka &arr[1]

#Loop
SUB x15, x5, x11    # x15 => i - size
BRZ x14             # if (i == size) => end of array, so exit loop
LD x15, x7          # x15 = arr[i]
SUB x16, x15, x6    # x16 = arr[i] - max
BRN x13             # when arr[i] < max, go to next iteration of loop
ADD x6, x0, x15     # x6 => max=arr[i]

#LoopIncrement
INC x5, x5, 0x1     # x5 => i++
INC x7, x7, 0x4     # x7 => &arr[i++]
J x12               # start next iteration of Loop

#LoopEnd
ADD x10, x6, x0     # store max in return register
```

## Hardware Loop - Phase 1
This program finds the max integer in an array “arr” of length “size”

Assumptions
1. x10 = &arr[0]
2. x11 = size

```asm
MAX x10, x10, x11
```

## Hardware Loop - Phase 2
Using the `MAX` instruction find the maximum number out of `n` numbers (like finding the max in an array).

### Assumptions and Restrictions
1. `x1` - Maximum value
2. `x10` - Base address of array (&arr[0])
3. `x11` - Size of input array

```asm
LD x11, x10        # load the size of array from base address to x11
INC x10, x10, 0x4  # move current position to the first element of the array
MAX x1, x10, x11   # find maximum element of the array using MAX instruction
```

## Truth Table for Control Logic
| Instruction        | RegWrite | MemWrite | MemRead | ALUSrc1 | ALUSrc2 | BRN | BRZ | JUMP | ADD | SUB | NEG | MemToReg |
| ------------------ | -------- | -------- | ------- | ------- | ------- | --- | --- | ---- | --- | --- | --- | -------- |
| No Operation       | 0        | 0        | 0       | 0       | 0       | 0   | 0   | 0    | 0   | 0   | 0   | 0        |
| Save PC            | 1        | 0        | 0       | 0       | 1       | 0   | 0   | 0    | 1   | 0   | 0   | 1        |
| Load               | 1        | 0        | 1       | 0       | 0       | 0   | 0   | 0    | 0   | 0   | 0   | 0        |
| Store              | 0        | 1        | 0       | 0       | 0       | 0   | 0   | 0    | 0   | 0   | 0   | 0        |
| Add                | 1        | 0        | 0       | 0       | 1       | 0   | 0   | 0    | 1   | 0   | 0   | 1        |
| Increment          | 1        | 0        | 0       | 1       | 1       | 0   | 0   | 0    | 1   | 0   | 0   | 1        |
| Negate             | 1        | 0        | 0       | 0       | 1       | 0   | 0   | 0    | 0   | 0   | 1   | 1        |
| Subtract           | 1        | 0        | 0       | 0       | 1       | 0   | 0   | 0    | 0   | 1   | 0   | 1        |
| Jump               | 0        | 0        | 0       | 0       | 0       | 0   | 0   | 1    | 0   | 0   | 0   | 0        |
| Branch if Zero     | 0        | 0        | 0       | 0       | 0       | 0   | 1   | 0    | 0   | 0   | 0   | 0        |
| Branch if Negative | 0        | 0        | 0       | 0       | 0       | 1   | 0   | 0    | 0   | 0   | 0   | 0        |
| Max                | 1        | 0        | 1       | 0       | 1       | 0   | 0   | 0    | 0   | 0   | 0   | 0        |

