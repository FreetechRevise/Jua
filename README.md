# Jua

[![Java](https://img.shields.io/badge/language-Java-green.svg)]()
[![lua](https://img.shields.io/badge/language-lua-blue.svg)]()

A simple Lua written in Java.

## Introduction

Jua is a toy Lua (5.3) implementation written in Java. 

The goals of this project is learning:

- Data Structure
- The Principles of Register-based Virtual Machine
- Compilers
- Lua grammar & Standard Library

## Features

- [x] Virtual Machine: (Instruction Set & Runner)
- [x] Compiler: Lexer, AST Parser & Code Generator
- [ ] Standard Library
    - [ ] Math
    - [ ] Table
    - [ ] String
    - [ ] UTF-8
    - [ ] OS
    - [ ] ...
- [ ] Metaprogramming
- [ ] Package & Module
- [ ] Coroutine
- [ ] Thread

### Instruction Set (completed)

| No. | Symbol | Opcode   | Mode | Desc |
| ------ | ----------- | ----------- | ----------- | ----------- |
| 0    | OP_MOVE     | 0x00       | iABC   | `R(A) := R(B)` |
| 1   | OP_LOADK    | 0x01       | iABx   | `R(A) := Kst(Bx)` |
| 2  | OP_LOADKX   | 0x02       | iABx   | `R(A) := Kst(extra arg)` |
| 3 | OP_LOADBOOL | 0x03       | iABC   | `R(A) := (bool)B; if (C) pc++` |
| 4 | OP_LOADNIL  | 0x04       | iABC   | `R(A), R(A+1), ..., R(A+B) := nil` |
| 5 | OP_GETUPVAL | 0x05       | iABC   | `R(A) := UpValue[B]` |
| 6 | OP_GETTABUP | 0x06       | iABC   | `R(A) := UpValue[B][RK(C)]` |
| 7 | OP_GETTABLE | 0x07       | iABC   | `R(A) := R(B)[RK(C)]` |
| 8 | OP_SETTABUP | 0x08       | iABC   | `UpValue[A][RK(B)] := RK(C)` |
| 9 | OP_SETUPVAL | 0x09       | iABC   | `UpValue[B] := R(A)` |
| 10 | OP_SETTABLE | 0x0A       | iABC   | `R(A)[RK(B)] := RK(C)` |
| 11 | OP_NEWTABLE | 0x0B       | iABC   | `R(A) := {} (size = B,C)` |
| 12   | OP_SELF     | 0x0C       | iABC   | `R(A+1) := R(B); R(A) := R(B)[RK(C)]` |
| 13    | OP_ADD      | 0x0D       | iABC   | `R(A) := RK(B) + RK(C)` |
| 14    | OP_SUB      | 0x0E       | iABC   | `R(A) := RK(B) - RK(C)` |
| 15    | OP_MUL      | 0x0F       | iABC   | `R(A) := RK(B) * RK(C)` |
| 16    | OP_MOD      | 0x10       | iABC   | `R(A) := RK(B) % RK(C)` |
| 17    | OP_POW      | 0x11       | iABC   | `R(A) := RK(B) ^ RK(C)` |
| 18    | OP_DIV      | 0x12       | iABC   | `R(A) := RK(B) / RK(C)` |
| 19   | OP_IDIV     | 0x13       | iABC   | `R(A) := RK(B) // RK(C)` |
| 20   | OP_BAND     | 0x14       | iABC   | `R(A) := RK(B) & RK(C)` |
| 21    | OP_BOR      | 0x15       | iABC   | `R(A) := RK(B)` |
| 22   | OP_BXOR     | 0x16       | iABC   | `R(A) := RK(B) ~ RK(C)` |
| 23    | OP_SHL      | 0x17       | iABC   | `R(A) := RK(B) << RK(C)` |
| 24    | OP_SHR      | 0x18       | iABC   | `R(A) := RK(B) >> RK(C)` |
| 25    | OP_UNM      | 0x19       | iABC   | `R(A) := -R(B)` |
| 26   | OP_BNOT     | 0x1A       | iABC   | `R(A) := ~R(B)` |
| 27   | OP_NOT      | 0x1B       | iABC   | `R(A) := not R(B)` |
| 28    | OP_LEN      | 0x1C       | iABC   | `R(A) := length of R(B)` |
| 29 | OP_CONCAT   | 0x1D       | iABC   | `R(A) := R(B).. ... ..R(C)` |
| 30    | OP_JMP      | 0x1E       | iAsBx  | `pc+=sBx; if (A) close all upvalues >= R(A - 1)` |
| 31     | OP_EQ       | 0x1F       | iABC   | `if ((RK(B) == RK(C)) ~= A) then pc++` |
| 32     | OP_LT       | 0x20       | iABC   | `if ((RK(B) <  RK(C)) ~= A) then pc++` |
| 33     | OP_LE       | 0x21       | iABC   | `if ((RK(B) <= RK(C)) ~= A) then pc++` |
| 34   | OP_TEST     | 0x22       | iABC   | `if not (R(A) <=> C) then pc++` |
| 35 | OP_TESTSET  | 0x23       | iABC   | `if (R(B) <=> C) then R(A) := R(B) else pc++` |
| 36   | OP_CALL     | 0x24       | iABC   | `R(A), ... ,R(A+C-2) := R(A)(R(A+1), ... ,R(A+B-1))` |
| 37 | OP_TAILCALL | 0x25       | iABC   | `return R(A)(R(A+1), ... ,R(A+B-1))` |
| 38 | OP_RETURN   | 0x26       | iABC   | `return R(A), ... ,R(A+B-2)` |
| 39 | OP_FORLOOP  | 0x27       | iABC   | `R(A)+=R(A+2); if R(A) <?= R(A+1) then { pc+=sBx; R(A+3)=R(A) }` |
| 40 | OP_FORPREP  | 0x28       | iAsBx  | `R(A)-=R(A+2); pc+=sBx` |
| 41 | OP_TFORCALL | 0x29       | iABC   | `R(A+3), ... ,R(A+2+C) := R(A)(R(A+1), R(A+2));` |
| 42 | OP_TFORLOOP | 0x2A       | iAsBx  | `if R(A+1) ~= nil then { R(A)=R(A+1); pc += sBx }` |
| 43 | OP_SETLIST  | 0x2B       | iABC   | `R(A)[(C-1)*FPF+i] := R(A+i), 1 <= i <= B` |
| 44 | OP_CLOSURE  | 0x2C       | iABx   | `R(A) := closure(KPROTO[Bx])` |
| 45 | OP_VARARG   | 0x2D       | iABC   | `R(A), R(A+1), ..., R(A+B-2) = vararg` |
| 46 | OP_EXTRAARG | 0x2E       |    | `extra (larger) argument for previous opcode` |

### Standard Library

...

## Usage

JDK 1.8+, Maven 3+ Required.

```shell
mvn test
mvn clean install
```

## Links

- [lua.go](https://github.com/zxh0/lua.go)
- [Lua 5.3 Reference Manual](http://www.lua.org/manual/5.3/manual.html)
- [Lua 5.3 Test suites](http://www.lua.org/tests/lua-5.3.4-tests.tar.gz)
- ...

## License

See LICENSE file.