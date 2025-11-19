# C Compiler

This repository contains a C compiler built from scratch. It is designed to compile a subset of the C programming language and generate 32-bit Intel assembly code.

## Features

- **Lexical Analysis**: A custom lexer tokenizes C source code.
- **Parsing**: A parser builds an Abstract Syntax Tree (AST) from the tokens.
- **Code Generation**: The compiler generates 32-bit Intel assembly language.
- **Semantic Validation**: A semantic validator ensures code correctness according to C language rules.
- **Preprocessor**: Supports macros like `#define`, `#ifdef`, and `sizeof`, as well as including header files.
- **Language Support**: Compiles a subset of C, including:
    - Pointers
    - Structs and Unions
    - Arrays
    - Functions
    - Loops (`for`, `while`)
    - Conditional statements (`if`, `else`)
- **Standard Library Compatibility**: The generated code is compatible with the GCC standard library.
