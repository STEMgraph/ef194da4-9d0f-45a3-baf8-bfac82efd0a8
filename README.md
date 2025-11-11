<!---
{
  "id": "ef194da4-9d0f-45a3-baf8-bfac82efd0a8",
  "teaches": "Comparing and Branching in C and Assembly",
  "depends_on": [
    "800c7dd9-5ccf-42c1-b9ea-c2764579cf5d",
    "cc424af3-2bd7-491f-b678-89e18904bf58"
  ],
  "author": "Stephan Bökelmann",
  "first_used": "2025-06-12",
  "keywords": ["assembly", "stack", "variables", "addresses", "x86_64", "GAS"]
}
--->

# Comparing and Branching in C and Assembly

> In this exercise you will explore how different types of comparisons and logical operations written in C are translated into x86\_64 assembly. You will compile a C program and inspect the resulting object file to discover how control flow and boolean logic emerge from instructions like `cmp`, `test`, and conditional jumps.

## Introduction

When writing C code, it is common to use relational operators (`==`, `>=`, `!=`, etc.) to compare values, as well as logical (`||`, `&&`) and bitwise (`&`, `|`, `^`) expressions to make decisions. These operators appear deceptively simple, but in assembly, their evaluation requires manipulating CPU flags and branching explicitly based on those outcomes.

At the hardware level, the CPU cannot evaluate high-level expressions directly. Instead, it uses dedicated instructions such as `cmp` (compare), `test` (bitwise AND for flags), and conditional jumps (`je`, `jne`, `jge`, etc.) to achieve the same result. The outcome of a comparison does not produce a value like `true` or `false` — instead, it sets one or more flags in the FLAGS register, which the CPU then uses to decide what to do next.

In this exercise, you will write a simple C program containing a variety of comparison and logic operations. You will compile and disassemble the output to correlate each construct in C with the instructions and branches produced in the generated assembly. By learning to read this low-level control flow, you build intuition for what your code really does under the hood.

### Further Readings and Other Sources

* [https://en.wikibooks.org/wiki/X86\_Assembly/Control\_Flow](https://en.wikibooks.org/wiki/X86_Assembly/Control_Flow)
* [https://godbolt.org/](https://godbolt.org/) (interactive compiler explorer)
* [https://cs.lmu.edu/\~ray/notes/asm/](https://cs.lmu.edu/~ray/notes/asm/)

## Task

### 1. Relational Comparisons

Write and compile the following program:

```c
#include <stdio.h>

int main() {
    int x = 10, y = 20;
    if (x == y) printf("Equal\n");
    if (x != y) printf("Not Equal\n");
    if (x >= y) printf("x >= y\n");
    if (x < y)  printf("x < y\n");
    return 0;
}
```

Then run:

```sh
gcc -O0 -c compare_rel.c -o compare_rel.o
objdump -d compare_rel.o
```

#### Observe:

* Each `if` results in a `cmp` instruction followed by a conditional jump:

  * `je` (jump if equal)
  * `jne` (jump if not equal)
  * `jge` (jump if greater or equal)
  * `jl` (jump if less)

Compare the branch labels and targets to the sequence of `printf` calls. Note how the compiler branches over them unless the condition is true.

### 2. Bitwise and Logical Operators

Write and compile this second example:

```c
#include <stdio.h>

int main() {
    int x = 10, y = 20;
    if (x & y) printf("Bitwise AND true\n");
    if (x | y) printf("Bitwise OR true\n");
    if (x ^ y) printf("Bitwise XOR true\n");
    if (x || y) printf("Logical OR true\n");
    if (x && y) printf("Logical AND true\n");
    return 0;
}
```

Compile and disassemble:

```sh
gcc -O0 -c compare_logical.c -o compare_logical.o
objdump -d compare_logical.o
```

#### Observe:

* Bitwise expressions often use `test` or `or` or `xor` followed by `jne`.
* Logical OR and AND often involve short-circuit patterns or sequential `test` with conditional branching.

### Analysis Prompt

For each `if` in both programs:

* Identify the setup (`mov`, `cmp`, `test`, etc.)
* Identify the branch (`je`, `jne`, `jg`, etc.)
* Map back to the original C code line

## Questions

1. What flags do `cmp` and `test` manipulate?
2. Why do bitwise operations use `test` instead of `cmp`?
3. How are logical ORs (`||`) and ANDs (`&&`) implemented in terms of control flow?
4. What instruction would be used for `if (!x)`?
5. If `-O2` optimization is applied, does the structure simplify or become more complex?

## Advice

Each type of comparison in C maps to a recognizable assembly idiom. For relational comparisons, `cmp` sets the stage and a conditional jump follows. For bitwise and logical evaluations, the compiler uses `test`, `or`, or `xor`, combined with `jne` or short-circuiting jumps. Once you’ve matched these patterns, you can reverse-engineer intent or design highly predictable branches for low-level programs.
