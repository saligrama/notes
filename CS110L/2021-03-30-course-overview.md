# Course info
Course site: cs110l.stanford.edu

## Topics
How to prevent common mistakes in systems programming?
* Memory safety and common errors in C/C++
* Preventing common memory safety errors in Rust
* Avoiding multiprocessing pitfalls
* Avoiding multithreading pitfalls
* Networked systems

## Grading
Course is pass/fail and the pass threshold is 70%

Components:
* Lecture (2x 50min/week)
* Weekly exercises (40%)
    - Small programming problems to reinforce week's lecture material
    - Alternatively: blog posts about course material
    - Expected time: 1-3 hours per week
* Projects (40%)
    - Mini GDB
    - High-performance web server
* Participation (20%)

# Why Rust?
* Languages like C/C++ commonly used for systems code
* Issues like buffer overflow can create massive security holes leading to issues like remote code execution
* Many overflows are obvious, but some are extremely subtle and hard to spot, as below:

## C buffer overflow example
```C
char buffer[128];
int bytesToCopy = packet.length;
if (bytesToCopy < 128) {
    strncpy(buffer, packet.data, bytesToCopy);
}
```

Where's the overflow? 

Overflow occurs because `bytesToCopy` is signed and passing it as an argument to strncpy causes it to be cast to unsigned. If a query has a `packet.length` of -1, then the cast will cause `bytesToCopy` to appear to be a very large number. A payload of larger than 128 bytes could pass the bounds check and cause a buffer overflow.

## How to prevent making such mistakes?
* Checking of data types and values at each runtime; a la Python/other interpreted languages. 
    - Disadvantage: these languages are significantly slower than compiled language and can be prohibitive for running on large inputs
* Dynamic analysis: Run program, watch what it does, look for problematic behavior
    - Disadvantage: Can find problematic behavior only when inputs used to test display problems. Sometimes, humans need to craft inputs to exhibit these issues.
    - Commonly combined with techniques such as fuzzing to test *lots* of inputs, but no guarantees that code is bug-free.
* Static analysis: Read the source code and find problematic parts. 
    - Easy with simple cases (for example, if a program uses `gets()`, we know it can display problematic behavior)
    - Impossible in the general case (i.e., Halting Problem)

Rust tries to make static analysis more tractable and helpful. It analyzes programs and attempts to disallow buffer-overflow or use-after-free prone code.