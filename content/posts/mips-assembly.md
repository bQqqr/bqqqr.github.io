+++
author = "Christos Kaltsas"
title = "MIPS Assembly"
date = "2022-06-09"
description = "A dive into MIPS core concepts."
tags = [
    "guide",
    "assembly",
    "mips"
]
+++


- [🤖 MIPS](#-mips)
  - [💼 Registers](#-registers)
  - [🗣️ Instructions](#-instructions)
    - [🔢 Arithmetic](#arithmetic)
    - [🤔 Logical](#logical)
    - [🚂 Data Transfer](#data-transfer)
    - [🛣️ Conditional Branch](#conditional-branch)
    - [⚖️ Comparison](#comparison)
    - [🦘 Unconditional Jump](#unconditional-jump)
    - [🩲 System Calls](#system-calls)
    - [🔠 Assembly Directives](#assembly-directives)
  - [⚒️ Methodology](#methodology)
    - [📞 Calling Conventions](#calling-conventions)
    - [💭 If Else](#if-else)

# 🤖 MIPS

MIPS is often used for pedagogical purposes in universities because it has a clean and small implementation. Also, it is a stepping stone to learning other major assembly languages such as ARM.

Let's dive into the details!

![MIPS meme](/img/mips.gif "MIPS meme")

## 💼 Registers

Registers have the following usage conventions:

- When a register is designated as **preserved across calls**, it means that the caller can count on the register having the same contents before and after a subprogram call. 

- If a register is designated as **not preserved across calls**, it means that the caller cannot count on the register having the same contents before and after a subprogram call. 

- If a register is designated as **dangerous to use**, it means that the register is used either by the operating system or the assembler.

```txt
| Symbol  | Actual     | Meaning                 | Usage Conventions |
| ------- | ---------- | ----------------------- | ----------------- |
| $zero   | $0         | Constant Value Zero     | Preserved         |
| $at     | $1         | Assembler's Register    | Preserved         |
| $v0-$v1 | $2-3       | Syscalls & Return Value | Not Preserved     |
| $a0-$a3 | $4-7       | Arguments               | Not Preserved     |
| $t0-$t7 | $8-15      | Temporaries             | Not Preserved     |
| $s0-$s7 | $16-23     | Saved Values            | Preserved         |
| $t8-$t9 | $24-25     | Temporaries             | Not Preserved     |
| $k0-$k1 | $26-27     | OS Registers            | Dangerous         |
| $gp     | $28        | Global Pointer          | Dangerous         |
| $sp     | $29        | Stack Pointer           | Dangerous         |
| $fp     | $30        | Frame Pointer           | Dangerous         |
| $ra     | $31        | Return Address          | Dangerous         |
| -       | $f0, $f2   | FP Return value         | Not Preserved     |
| -       | $f4-$f10   | Temporaries             | Not Preserved     |
| -       | $f12, $f14 | FP Arguments            | Not Preserved     |
| -       | $f16, $f18 | Temporaries             | Not Preserved     |
| -       | $f20-$f30  | Saved Values            | Preserved         |
```

Simple programs should use the following registers:
- `$zero` for the constant 0.
- `$s0 - $s8$` for main program variables
- `$t0 - $t8$` for subprogram variables
- `$a0 - $a3$` for subprogram and syscall parameters
- `$v0, $v1$` for subprogram return values and syscall codes and return values

## 🗣️ Instructions

###  🔢 Arithmetic
```
| Instruction             | Example             | Meaning             |
| ----------------------- | ------------------- | ------------------- |
| Add                     | add   $1,  $2,  $3  | $1 = $2 + $3        |
| Subtract                | sub   $1,  $2,  $3  | $1 = $2 - $3        |
| Add Immediate           | addi  $1,  $2,  100 | $1 = $2 + 100       |
| Add Unsigned            | addu  $1,  $2,  $3  | $1 = $2 + $3        |
| Subtract Unsigned       | subu  $1,  $2,  $3  | $1 = $2 - $3        |
| Add Immediate Unsigned  | addiu $1,  $2,  100 | $1 = $2 + 100       |
| Multiply (/wo overflow) | mul   $1,  $2,  $3  | $1 = $2 * $3        |
| Multiply                | mult  $2,  $3       | $hi, $low = $2 * $3 |
| (float) Divide          | div   $2,  $3       | $hi, $low = $2 / $3 |
| (float) Add             | add.s $f0, $f1, $f2 | $f0 :=  $f1 + $f2   |
| (float) Subtract        | sub.s $f0, $f1, $f2 | $f0 :=  $f1 - $f2   |
| (float) Multiply        | mul.s $f0, $f1, $f2 | $f0 :=  $f1 * $f2   |
| (float) Divide          | div.s $f0, $f1, $f2 | $f0 :=  $f1 / $f2   |
| (float) Abs             | abs.s $f0, $f1      | $f0 :=  $f1         |
| (float) Negative        | neg.s $f0, $f1      | $f0 := -$f1         |
```

### 🤔 Logical

```
| Instruction   | Example        | Meaning        |
| ------------- | -------------- | -------------- |
| And           | and  $1,$2,$3  | $1 = $2 & $3   |
| Or            | or   $1,$2,$3  | $1 = $2 \| $3  |
| And Immediate | andi $1,$2,100 | $1 = $2 &  100 |
| Or Immediate  | or   $1,$2,100 | $1 = $2 \| 100 |
| Shift Left    | sll  $1,$2,10  | $1 = $2 << 10  |
| Shift Right   | srl  $1,$2,10  | $1 = $2 >> 10  |
```

### 🚂 Data Transfer

```
| Instruction          | Example             | Meaning                                |
| -------------------- | ------------------- | -------------------------------------- |
| Load Word            | lw    $1, 100($2)   | $1 = Memory[$2+100]                    |
| store Word           | sw    $1, 100($2)   | Memory[$2+100] = $1                    |
| Load Upper Immediate | lui   $1, 100       | $1 = 100 x 2^16                        |
| Load Address         | la    $1, label     | $1 = Address of label                  |
| Load Immediate       | li    $1, 100       | $1 = 100                               |
| Move From hi         | mfhi  $2            | $2 = hi                                |
| Move From lo         | mflo  $2            | $2 = lo                                |
| Move                 | move  $1, $2        | $1 = $2                                |
| Load Word            | l.s   $f0, 100($t2) | Load word into $f0 from addr $t2+100   |
| Store Word           | s.s   $f0, 100($t2) | Store word from $f0 into addr $t2+100  |
| Move                 | mov.s $f0, $f2      | Move between FP registers              |
| Move From            | mfc1  $t1, $f2      | Move from FP registers (no conversion) |
| Move to              | mtc1  $t1, $f2      | Move to FP registers (no conversion)   |
| Convert to Integer   | cvt.w.s $f2, $f4    | Convert from single FP to int          |
| Convert from Integer | cvt.s.w $f2, $f4    | Convert from int to single FP          |
```

### 🛣️ Conditional Branch

```
| Instruction  | Example         | Meaning                                   |
| ------------ | --------------- | ----------------------------------------- |
| Branch on == | beq $1,$2,100   | if true goto PC+4+100                     |
| Branch on != | bne $1,$2,100   | if true goto PC+4+100                     |
| Branch on >  | bgt $1,$2,100   | if true goto PC+4+100                     |
| Branch on >= | bge $1,$2,100   | if true goto PC+4+100                     |
| Branch on <  | blt $1,$2,100   | if true goto PC+4+100                     |
| Branch on <= | ble $1,$2,100   | if true goto PC+4+100                     |
| Compare ==   | c.eq.s $f2, $f4 | if $f2 == $f4 then code = 1 else code = 0 |
| Compare <=   | c.le.s $f2, $f4 | if $f2 <= $f4 then code = 1 else code = 0 |
| Compare >    | c.lt.s $f2, $f4 | if $f2 < $f4 then code = 1 else code = 0  |
| Branch on 0  | bc1f label      | if code == 0 then jump to label           |
| Branch on 1  | bc1t label      | if code == 1 then jump to label           |
```

### ⚖️ Comparison

```
| Instruction        | Example          | Meaning                          |
| ------------------ | ---------------- | -------------------------------- |
| Set on <           | slt  $1, $2, $3  | if($2 < $3)  $1 = 1; else $1 = 0 |
| Set on < Immediate | slti $1, $2, 100 | if($2 < 100) $1 = 1; else $1 = 0 |
```

### 🦘 Unconditional Jump

```
| Instruction   | Example  | Meaning                       |
| ------------- | -------- | ----------------------------- |
| Jump          | j 1000   | Go to address 1000            |
| Jump Register | jr $1    | Go to address stored in $1    |
| Jump and Link | jal 1000 | $ra = PC+4 Go to address 1000 |
```

### 🩲 System Calls

```
| Service      | Operation             | v0  | Arguments                       | Results      |
| ------------ | --------------------- | --- | ------------------------------- | ------------ |
| print_int    | Print integer number  | 1   | $a0 = integer printed           | None         |
| print_float  | Print float number    | 2   | $f12 = float printed            | None         |
| print_double | Print dobule number   | 3   | $f12 = double printed           | None         |
| print_string | Print null-term str   | 4   | $a0 = addr(str)                 | None         |
| read_int     | Read int from user    | 5   | None                            | Int    ($v0) |
| read_float   | Read float from user  | 6   | None                            | Float  ($f0) |
| read_double  | Read double from user | 7   | None                            | Double ($f0) |
| read_string  | Same as fgets()       | 8   | $a0 = addr(str), $a1 = len(str) | None         |
| sbrk         | Returns a mem block   | 9   | $a0 = size                      | Addr   ($v0) |
| exit         | Stop program          | 10  | None                            | None         |
| print_char   | Print character       | 11  | $a0 = char to be printed        | None         |
| read_char    | Read character > user | 12  | None                            | Char   ($v0) |
| exit2        | Exits /w return code. | 17  | $a0 = return code               | None         |
```

### 🔠 Assembly Directives

```
| Directive         | Result                                                     |
| ----------------- | ---------------------------------------------------------- |
| .word w1, ..., wn | Store n 32-bit values in successive memory words           |
| .half h1, ..., hn | Store n 16-bit values in successive memory words           |
| .byte b1, ..., bn | Store n 8-bit values in successive memory words            |
| .ascii str        | Store the ASCII string str in memory                       |
| .asciiz str       | Store the ASCII string str in memory and null-terminate it |
| .space n          | Leave an empty n-byte region of memory for later use       |
| .align n          | Align the next datum on a 2^n byte boundary.               |
```

## ⚒️ Methodology

### 📞 Calling Conventions

{{< tabgroup >}}
  {{< tab name="Caller Store" >}}
  ```asm
  addiu $sp, $sp, -64
  sw $t0, 60($sp)
  sw $t1, 56($sp)
  sw $t2, 52($sp)
  sw $t3, 48($sp)
  sw $t4, 44($sp)
  sw $t5, 40($sp)
  sw $t6, 36($sp)
  sw $t7, 32($sp)
  sw $t8, 28($sp)
  sw $t9, 24($sp)
  sw $a0, 20($sp)
  sw $a1, 16($sp)
  sw $a2, 12($sp)
  sw $a3, 8($sp)
  sw $v0, 4($sp)
  sw $v1, 0($sp)
  ```
  {{< /tab >}}
  {{< tab name="Caller Restore" >}}
  ```asm
  lw $t0, 60($sp)
  lw $t1, 56($sp)
  lw $t2, 52($sp)
  lw $t3, 48($sp)
  lw $t4, 44($sp)
  lw $t5, 40($sp)
  lw $t6, 36($sp)
  lw $t7, 32($sp)
  lw $t8, 28($sp)
  lw $t9, 24($sp)
  lw $a0, 20($sp)
  lw $a1, 16($sp)
  lw $a2, 12($sp)
  lw $a3, 8($sp)
  lw $v0, 4($sp)
  lw $v1, 0($sp)
  addiu $sp, $sp, 64
  ```
  {{< /tab >}}
  {{< tab name="Callee Store" >}}
  ```asm
  addiu $sp, $sp, -36
  sw    $s7, 32($sp)
  sw    $s6, 28($sp)
  sw    $s5, 24($sp)
  sw    $s4, 20($sp)
  sw    $s3, 16($sp)
  sw    $s2, 12($sp)
  sw    $s1, 8($sp)
  sw    $s0, 4($sp)
  sw    $ra, 0($sp)
  ```
  {{< /tab >}}
  {{< tab name="Callee Restore" >}}
  ```asm
  lw $s7, 32($sp)
  lw $s6, 28($sp)
  lw $s5, 24($sp)
  lw $s4, 20($sp)
  lw $s3, 16($sp)
  lw $s2, 12($sp)
  lw $s1, 8($sp)
  lw $s0, 4($sp)
  lw $ra, 0($sp)
  addiu $sp, $sp, 36
  jr $ra
  ```
  {{< /tab >}}
{{< /tabgroup >}}

### 💭 If Else

{{< tabgroup >}}
  {{< tab name="Dennis's C" >}}
  ```c
  int main()
  {
      int a = 5; 
      int b = 3; 
      int c = 0;

      if (a == b)
          c = a + b;
      else
          c = a - b;

      printf("%d", c);

      return 0;
  }
  ```
  {{< /tab >}}
  {{< tab name="MIPS" >}}
  ```asm
  .text
    main:
        li $t0,5       #a
        li $t1,3       #b
        li $t2,3       #c
        beq $t0,$t1,if
        j else
    if:
        add $t2,$t0,$t1
    else:
        sub $t2,$t0,$t1
        print:
        li $v0,1
        move $a0,$t2
        syscall 

  ```
  {{< /tab >}}
{{< /tabgroup >}}

To be continued..