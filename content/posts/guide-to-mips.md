+++
author = "Christos Kaltsas"
title = "A Guide To MIPS"
date = "2022-06-08"
description = "A dive into MIPS core concepts."
tags = [
    "guide",
    "assembly",
    "mips"
]
+++

# :robot: MIPS

## :handbag: Registers

### Integer Ones

| Symbol  | Actual | Meaning                                   | Usage Conventions          |
| ------- | ------ | ----------------------------------------- | -------------------------- |
| $zero   | $0     | Constant Value Zero                       | Preserved Across Calls     |
| $at     | $1     | Reversed by the Assembler                 | Preserved Across Calls     |
| $v0-$v1 | $2-3   | Expression Eval & Subprogram Return Value | Not Preserved Across Calls |
| $a0-$a3 | $4-7   | Arguments                                 | Not Preserved Across Calls |
| $t0-$t7 | $8-15  | Temporaries                               | Not Preserved Across Calls |
| $s0-$s7 | $16-23 | Saved Values                              | Preserved Across Calls     |
| $t8-$t9 | $24-25 | Temporaries                               | Not Preserved Across Calls |
| $k0-$k1 | $26-27 | Kernel (OS) Registers                     | Dangerous to Use           |
| $gp     | $28    | Global Pointer                            | Dangerous to Use           |
| $sp     | $29    | Stack Pointer                             | Dangerous to Use           |
| $fp     | $30    | Frame Pointer                             | Dangerous to Use           |
| $ra     | $31    | Return Address                            | Dangerous to Use           |

Simple programs should use the following registers:
- `$zero` for the constant 0.
- `$s0 - $s8$` for main program variables
- `$t0 - $t8$` for subprogram variables
- `$a0 - $a3$` for subprogram and syscall parameters
- `$v0, $v1$` for subprogram return values and syscall codes and return values

### Floating-Point Ones

| Register Name | Use                                                                 |
| ------------- | ------------------------------------------------------------------- |
| $f0, $f2      | Floating Point Subprogram Return value - Not Preserved Across Calls |
| $f4 - $f10    | Temporaries - Not Preserved Across Calls                            |
| $f12, $f14    | The First 2 Floating Point Parameters - Not Preserved Across Calls  |
| $f16, $f18    | Temporaries - Not Preserved Across Calls                            |
| $f20 - $f30   | Saved Values - Preserved Across Calls                               |

## :memo: Instructions

| Instruction                 | Example             | Meaning           |
| --------------------------- | ------------------- | ----------------- |
| Add                         | add   $1,$2,$3      | $1=$2+$3          |
| Subtract                    | sub   $1,$2,$3      | $1=$2-$3          |
| Add Immediate               | addi  $1,$2,100     | $1=$2+100         |
| Add Unsigned                | addu  $1,$2,$3      | $1=$2+$3          |
| Subtract Unsigned           | subu  $1,$2,$3      | $1=$2-$3          |
| Add Immediate Unsigned      | addiu $1,$2,100     | $1=$2+100         |
| Multiply (without overflow) | mul   $1,$2,$3      | $1=$2*$3          |
| Multiply                    | mult  $2,$3         | $hi,$low=$2*$3    |
| (Floats) Divide             | div   $2,$3         | $hi,$low=$2/$3    |
| (Floats) Add                | add.s $f0, $f1, $f2 | $f0 := $f1 + $f2  |
| (Floats) Subtract           | sub.s $f0, $f1, $f2 | $f0 := $f1 - $f2  |
| (Floats) Multiply           | mul.s $f0, $f1, $f2 | $f0 := $f1 * $f2  |
| (Floats) Divide             | div.s $f0, $f1, $f2 | $f0 := $f1 / $f2  |
| (Floats) Abs                | abs.s $f0, $f1      | $f0 :=        $f1 |
| (Floats) Negative           | neg.s $f0, $f1      | $f0 := -$f1       |
### Logical

| Instruction         | Example             | Meaning       |
| ------------------- | ------------------- | ------------- |
| and                 | and  $1,$2,$3       | $1=$2&$3      |
| or                  | or   $1,$2,$3 $1=$2 | $3 Bitwise OR |
| and immediate       | andi $1,$2,100      | $1=$2&100     |
| or immediate        | or   $1,$2,100      | $1=$2\|100    |
| shift left logical  | sll  $1,$2,10       | $1=$2<<10     |
| shift right logical | srl  $1,$2,10       | $1=$2>>10     |

### Data Transfer

| Instruction          | Example           | Meaning                                     |
| -------------------- | ----------------- | ------------------------------------------- |
| load word            | lw   $1,100($2)   | $1=Memory[$2+100]                           |
| store word           | sw   $1,100($2)   | Memory[$2+100]=$1                           |
| load upper immediate | lui  $1,100       | $1=100x2^16                                 |
| load address         | la   $1,label     | $1=Address of label                         |
| load immediate       | li   $1,100       | $1=100                                      |
| move from hi         | mfhi $2           | $2=hi                                       |
| move from lo         | mflo $2           | $2=lo                                       |
| move                 | move $1,$2        | $1=$2                                       |
| load word            | l.s $f0, 100($t2) | load word into $f0 from address $t2+100     |
| store word           | s.s $f0, 100($t2) | store word from $f0 into address $t2+100    |
| move                 | mov.s $f0, $f2    | move between FP registers                   |
| move from            | mfc1 $t1, $f2     | move from FP registers (no conversion)      |
| move to              | mtc1 $t1, $f2     | move to FP registers (no conversion)        |
| convert to integer   | cvt.w.s $f2, $f4  | convert from single precision FP to integer |
| convert from integer | cvt.s.w $f2, $f4  | convert from integer to single precision FP |

### Conditional Branch

| Instruction                     | Example         | Meaning                                   |
| ------------------------------- | --------------- | ----------------------------------------- |
| branch on equal                 | beq $1,$2,100   | if true goto PC+4+100                     |
| branch on not equal             | bne $1,$2,100   | if true goto PC+4+100                     |
| branch on greater than          | bgt $1,$2,100   | if true goto PC+4+100                     |
| branch on greater than or equal | bge $1,$2,100   | if true goto PC+4+100                     |
| branch on less than             | blt $1,$2,100   | if true goto PC+4+100                     |
| branch on less than or equal    | ble $1,$2,100   | if true goto PC+4+100                     |
| compare equal                   | c.eq.s $f2, $f4 | if $f2 == $f4 then code = 1 else code = 0 |
| compare less or equal           | c.le.s $f2, $f4 | if $f2 <= $f4 then code = 1 else code = 0 |
| compare larger than             | c.lt.s $f2, $f4 | if $f2 < $f4 then code = 1 else code = 0  |
| branch on code == 0             | bc1f label      | if code == 0 then jump to label           |
| branch on code == 1             | bc1t label      | if code == 1 then jump to label           |

### Comparison

| Instruction                | Example        | Meaning                   |
| -------------------------- | -------------- | ------------------------- |
| set on less than           | slt  $1,$2,$3  | if($2<$3)$1=1; else $1=0  |
| set on less than immediate | slti $1,$2,100 | if($2<100)$1=1; else $1=0 |

### Unconditional Jump

| Instruction   | Example  | Meaning                      |
| ------------- | -------- | ---------------------------- |
| jump          | j 1000   | go to address 1000           |
| jump register | jr $1    | go to address stored in $1   |
| jump and link | jal 1000 | $ra=PC+4; go to address 1000 |

### System Calls

| Service      | Operation             | v0  | Arguments                       | Results      |
| ------------ | --------------------- | --- | ------------------------------- | ------------ |
| print_int    | Print integer number  | 1   | $a0 = integer printed           | None         |
| print_float  | Print float number    | 2   | $f12 = float printed            | None         |
| print_double | Print dobule number   | 3   | $f12 = double printed           | None         |
| print_string | Print null-term str   | 4   | $a0 = addr(str)                 | None         |
| read_int     | Read int from user    | 5   | None                            | Int - $v0    |
| read_float   | Read float from user  | 6   | None                            | Float - $f0  |
| read_double  | Read double from user | 7   | None                            | Double - $f0 |
| read_string  | Same as fgets()       | 8   | $a0 = addr(str), $a1 = len(str) | None         |
| sbrk         | Returns a mem block   | 9   | $a0 = size                      | Addr - $v0   |
| exit         | Stop program          | 10  | None                            | None         |
| print_char   | Print character       | 11  | $a0 = char to be printed        | None         |
| read_char    | Read character > user | 12  | None                            | Char - $v0   |
| exit2        | Exits /w return code. | 17  | $a0 = return code               | None         |

### Assembly Directives

| Directive         | Result                                                     |
| ----------------- | ---------------------------------------------------------- |
| .word w1, ..., wn | Store n 32-bit values in successive memory words           |
| .half h1, ..., hn | Store n 16-bit values in successive memory words           |
| .byte b1, ..., bn | Store n 8-bit values in successive memory words            |
| .ascii str        | Store the ASCII string str in memory                       |
| .asciiz str       | Store the ASCII string str in memory and null-terminate it |
| .space n          | Leave an empty n-byte region of memory for later use       |
| .align n          | Align the next datum on a 2^n byte boundary.               |
