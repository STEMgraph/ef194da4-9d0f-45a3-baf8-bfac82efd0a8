<!---
{
  "id": "ef194da4-9d0f-45a3-baf8-bfac82efd0a8",
  "depends_on": [
    "AND",
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

In this exercise, you will write a simple C program containing various comparison and logical expressions, then compile and disassemble it to understand exactly how the compiler represents those constructs at the assembly level. Recognizing the structure of comparisons and control flow in compiled output is a foundational skill in reverse engineering, optimization, and systems programming.

### Further Readings and Other Sources

* [https://en.wikibooks.org/wiki/X86\_Assembly/Control\_Flow](https://en.wikibooks.org/wiki/X86_Assembly/Control_Flow)
* [https://godbolt.org/](https://godbolt.org/) (interactive compiler explorer)
* [https://cs.lmu.edu/\~ray/notes/asm/](https://cs.lmu.edu/~ray/notes/asm/)

## Task

### Write, Compile, and Disassemble

Create a file `compare.c` with the following contents:

```c
#include <stdio.h>

int main() {
    int x = 10, y = 20;
    if (x == y) printf("Equal\n");
    if (x >= y) printf("x >= y\n");
    if (x & y) printf("Bitwise AND true\n");
    if (x || y) printf("Logical OR true\n");
    return 0;
}
```

Then compile and disassemble:

```sh
gcc -O0 -c compare.c -o compare.o
objdump -d compare.o
```

### Inspect:

* Look for `cmp` and `test` instructions — they evaluate relationships or bits.
* Look for conditional jumps like `je`, `jge`, `jne` — these alter flow based on CPU flags.
* Try matching each C `if` to a cluster of instructions in the `.text` section.

## Questions

1. What flag comparisons does `cmp` affect?
2. How does `x == y` differ from `x & y` at the machine level?
3. What register values are used in `test`?
4. Can you match the structure of `if (x || y)` to specific jumps?
5. What happens if you change `>=` to `<` and recompile?

## Advice

Understanding how C comparisons compile down to conditional logic in assembly gives you deep insight into performance, branching, and binary behavior. Once you recognize the patterns, you’ll find them recurring in hand-written and compiler-generated assembly alike. Experiment with changing operators or adding `else` clauses to see how control structures evolve.
