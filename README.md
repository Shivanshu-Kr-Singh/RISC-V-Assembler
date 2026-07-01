# RISC-V Assembler

A Python implementation of a **two-pass RISC-V assembler** that converts a subset of **RV32I assembly instructions** into their corresponding 32-bit binary machine code.

## Features

* Supports a subset of the RV32I ISA:

  * **R-Type:** `add`, `sub`, `slt`, `srl`, `or`, `and`
  * **I-Type:** `addi`, `lw`, `jalr`
  * **S-Type:** `sw`
  * **B-Type:** `beq`, `bne`, `blt`
  * **J-Type:** `jal`
* Label handling using a **two-pass assembly process**
* Supports:

  * Decimal immediates
  * Hexadecimal immediates (`0x...`)
  * Binary immediates (`0b...`)
* Register aliases (`zero`, `ra`, `sp`, `a0`, etc.)
* Error detection for:

  * Invalid instructions
  * Invalid register names
  * Undefined labels
  * Incorrect instruction formats
  * Branch and jump offsets out of range
* Virtual halt instruction support.

---

## Project Structure

```text
.
├── assembler.py        # Main assembler implementation
├── input.asm           # Input RISC-V assembly program
├── output.bin          # Generated binary instructions
└── README.md
```

---

## Supported Instructions

### R-Type Instructions

| Instruction | Format             |
| ----------- | ------------------ |
| add         | `add rd, rs1, rs2` |
| sub         | `sub rd, rs1, rs2` |
| slt         | `slt rd, rs1, rs2` |
| srl         | `srl rd, rs1, rs2` |
| or          | `or rd, rs1, rs2`  |
| and         | `and rd, rs1, rs2` |

---

### I-Type Instructions

| Instruction | Format               |
| ----------- | -------------------- |
| addi        | `addi rd, rs1, imm`  |
| lw          | `lw rd, offset(rs1)` |
| jalr        | `jalr rd, rs1, imm`  |

---

### S-Type Instructions

| Instruction | Format                |
| ----------- | --------------------- |
| sw          | `sw rs2, offset(rs1)` |

---

### B-Type Instructions

| Instruction | Format                |
| ----------- | --------------------- |
| beq         | `beq rs1, rs2, label` |
| bne         | `bne rs1, rs2, label` |
| blt         | `blt rs1, rs2, label` |

---

### J-Type Instructions

| Instruction | Format          |
| ----------- | --------------- |
| jal         | `jal rd, label` |

---

## Register Support

The assembler supports both:

### ABI Names

```text
zero ra sp gp tp
t0-t6
s0-s11
a0-a7
fp
```

### Register Numbers

```text
x0 - x31
```

Example:

```assembly
add x1, x2, x3
add ra, sp, t0
```

---

## Labels

Labels are resolved during the first pass.

Example:

```assembly
loop:
    addi t0, t0, 1
    blt t0, t1, loop
```

---

## Example Program

### Input (`input.asm`)

```assembly
main:
    addi t0, zero, 10
    addi t1, zero, 20
    add t2, t0, t1
    beq zero, zero, 0
```

### Output (`output.bin`)

```text
00000000101000000000001010010011
00000001010000000000001100010011
00000000011000101000001110110011
00000000000000000000000001100011
```

---

## Virtual Halt Instruction

The assembler treats:

```assembly
beq zero, zero, 0
```

as a **Virtual Halt** instruction.

Binary representation:

```text
00000000000000000000000001100011
```

---

## Assembly Process

### Pass 1

* Reads all assembly lines.
* Records labels and their addresses.

### Pass 2

* Encodes instructions into binary.
* Resolves branch and jump offsets.
* Writes machine code to the output file.

---

## Usage

### Run from Terminal

```bash
python assembler.py input.asm output.bin
```

### Example

```bash
python assembler.py program.asm machine_code.txt
```

---

## Output

The generated file contains one 32-bit binary instruction per line:

```text
00000000010100000000001010010011
00000000101000000000001100010011
...
```

---

## Error Handling

The assembler reports errors such as:

* Invalid instruction names
* Invalid registers
* Undefined labels
* Incorrect operand counts
* Invalid memory access syntax
* Branch offsets out of range
* Jump offsets out of range

Example:

```text
Error at line 5: Undefined label: loop
```

---

## Limitations

* Supports only a subset of the RV32I instruction set.
* Does not support:

  * Pseudo instructions
  * Directives (`.data`, `.text`, etc.)
  * Macros
  * Floating-point instructions
  * Compressed instructions (RVC)

---

## Future Improvements

* Full RV32I support
* Pseudo-instruction expansion
* Data segment support
* Hexadecimal machine code output
* Better error reporting
* Instruction simulator integration
* ELF generation support

---

## Author

Developed as part of a project to understand the internals of the **RISC-V ISA**, instruction encoding, and assembler design principles.
