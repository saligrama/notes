# Semantic Analysis

## Purpose of semantic analysis

Why separate semantic analysis?

* Parsing cannot catch some errors
* Some language constructs are not context-free

What does semantic analysis do? Checks constructs depending on language

`coolc` checks:

1. All identifiers are declared
2. Types
3. Inheritance relationships
4. Classes defined only once
5. Class methods only defined once
6. Reserved identifiers are not misused
7. etc.

## Types of semantic analysis checks

### Scope

* Matching identifier declarations with uses
    - Important static analysis step in most languages, including COOL
* The scope of an identifier is the portion of a program in which that identifier is accessible
* Same identifier may refer to different things in different parts of the program
* An identifier may have restricted scope
* Static scope: depends only on program text, not run-time behavior (most languages, including COOL)
    - Uses of an identifier refer to closest enclosing definition in program text
* Dynamic scope: depends on program execution (LISP, SNOBOL)
    - Uses of an identifier refer to closest encloding binding in *program execution*
* In COOL:
    - Not all kinds of identifiers follow most-closely-nested rule
    - e.g. class names can be used before definition
        ```cool
        Class Foo {
            ...let y: Bar in...
        };

        Class Bar {
            ...
        };
        ```
* Implementing the most-closely-nested rule
    - Much of semantic analysis can be expressed as a recursive descent of AST
        - Before: process an AST node *n*
        - Recurse: process the children of *n*
        - After: finish processing the AST node *n*
    - When performing semantic analysis on a portion of the AST, we need to know which identifiers are defined
    - e.g. scope of `let` bindings is one subtree of the AST: `let x: Int <- 0 in e`
        - `x` is defined in subtree `e`
        - Before processing `e`, add definition of `x` to current definitions, overriding any definition of `x`
        - Recurse
        - After processing `e`, remove definition of `x` and restore old definition of `x`
        - Symbol table: data structure that tracks current binding of identifiers
            - Implemented using stack

### Types

* What is a type? Consensus is
    - A set of values
    - A set of operations on those values
* Classes are one instantiation of the modern notion of type
* A language's type system specifies which operations are valid for which types
    - e.g. Should not be adding function pointer and integer in C
    - Enforces intended interpretation of values
* Types of typing:
    - Static typing: all or almost checking of types is done as part of compilation (e.g. C, Java, COOL)
        - Advantages: catch many programming errors at compile tyme, avoids overhead of runtime type checks
    - Dynamic typing: almost all checking of types is done at runtime (Scheme, Python)
        - Advantages: makes rapid prototyping difficult
    - Untyped: machine code

# Type Checking

## Cool types

* Types: 
    - Class names
    - `SELF_TYPE`
* User declares types for identifiers
* Compiler infers types for expressions
    - Infers a type for every expression

## Type checking and type inference

* Type checking: process of verifying fully typed programs
* Type inference: process of filling in missing type information