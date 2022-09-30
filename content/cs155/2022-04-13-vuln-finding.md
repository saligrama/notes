# Finding vulnerabilities using fuzzing, dynamic, and static analysis

## Conceptualizing vulnerabilities

* Computer programs can be thought of as finite state machines
* The code we *want* to write represents states we intend to reach
* However, code can also have unintended states if miswritten
    - Bugs exist when there are reachable states in the runnable state machine (the code) that have no corresponding state in the intended state machine (the design)
* Vulnerabilities live in this unintended space; exploitation is making the program do "interesting" transitions in the unintended state space
* Types of bugs:
    - Design issue: the conceptual state machine does not meet intended goals
        - e.g. hardcoded admin password
    - Functionality issue: the code has bad transitions between validly represented states
        - e.g. save button code broken, no transition to "saving file" state
    - Implementation issue: code introduces new states not represented in conceputal state machine
        - e.g. lack of length check introduces new "stack corruption" state
* Other ways to reach unintended states:
    - Hardware fault: hardware suffers a glitch that causes a transition to an unintended state even if the code is perfect
        - e.g. a cosmic ray flips a bit in memory, causing an otherwise impossible state
    - Transmission error: the code is correct but is corrupted in-flight
        - e.g. a program downloaded from the internet suffers packet corruption, so the program that is run differs from what was intended to run

## Fuzzing

* Observe: for interesting programs, impossible to manually explore full state space to find unintended states
* Idea: find bugs in a program by feeding random, corrupted, or unexpected data
    - Random inputs will explore a large part of the state space
    - Some unintended states are observable as crashes (e.g. `SIGSEGV`, `abort()`)
    - Any crash is a bug, but only some crashes are observable
* Fuzzing works best on programs that parse files or process complex data
* Can be simple as `cat /dev/random | head -c 512 > rand.jpeg; open rand.jpeg`
    - However, will not cover much of search space - will be rejected because invalid jpeg
    - Can randomly corrupt real JPEG files, create "JPEG-looking" data, and measure JPEG parser to see how deep we're getting into the code

### Common fuzzing strategies

* Mutation-based fuzzing: randomly mutate test cases from corpus of input files
    - Steps:
        1. Collect a corpus of inputs that explores as many states as possible
        2. Perturb inputs randomly, possibly guided by heuristics
            - Modify: bit flips, integer increments
            - Substitute: small integers, large integers, negative integers
        3. Run the program on the inputs and check for crashes
        4. Goto step 2
    - Advantages:
        - Simple to set up and run
        - Can use off-the-shelf software for many programs
    - Limitations:
        - Results depend strongly on the quality of the initial corpus
        - Coverage may be shallow for formats for checksums or validation
    - Still can be successful: fully random mutation based-fuzzing found many exploitable-looking crashes in PDF viewers in 2010
* Generation-based (smart) fuzzing: generate test cases based on a specification for the input format
    - Steps:
        1. Convert a specification of the input format (RFC, etc.) into a generative procedure
        2. Generate test cases according to the procedure and introduce random perturbations
        3. Run the program on the inputs and check for crashes
        4. Goto step 2
    - e.g. Syzkaller: kernel system calll fuzzer that uses test case generation and coverate
        - Test cases are sequences of syscalls generated from syscall descriptions
        - Runs the test case program in a VM
        - Kernel crashes in the VM indicate possible LPE vulnerabilities
    - Advantages:
        - Can get deeper coverage faster by leveraging knowledge of the input format
        - Input format/protocol complexity is not a limit on coverage depth
    - Limitations:
        - Requires a lot of effort to set up
        - Succesful fuzzers are often domain-specific;
        - Coverage limited by accuracy of the spec, where implementation may diverge
* Coverage-guided fuzzing
    - Key insight: code coverage is a useful metric; why not use it as feedback to guide fuzzing?
    - Types:
        - Basic block coverage: has this basic block in the CFG been run?
        - Edge coverage: has this branch been taken?
        - Path coverage: has this particular path through the program been taken?
    - Advantages:
        - Very good at finding new program states, even if the initial corpus is limited
        - Combines well with other fuzzing strategies
        - Successful track record
    - Limitations:
        - Not a panacea to bypass strong checksums or input validation
        - Still doesn't find all types of bugs (e.g. race conditions)

### American Fuzzy Lop (AFL)

1. Compile the program with instrumentation to measure coverage
2. Trim the test cases in the queue to the smallest size that doesn't change program behavior
3. Create new test cases by mutating the files in the queue using traditional fuzzing strategies
4. If new coverage is found in a mutated file, add it to the queue
5. Goto step 2

## Dynamic analysis

* Analyze a program's behavior by actually running its code
    - May be combined with compile-time modifications like instrumentation
    - Can modify program behavior dynamically
        - Useful for rapid experimentation
    - Often complements fuzzing very well
* e.g. AddressSanitizer (ASan)
    - Fast memory error detecter for C/C++ using compiler instrumentation and a runtime library that replaces `malloc` to surround allocations with redzones
    - Catches the following:
        - Out-of-bounds accesses
        - Use-after-free
        - Use-after-return
        - Use-after-scope
        - Double-free, invalid-free
        - Memory leaks
* e.g. Frida
    - Dynamic instrumentation for closed-source binaries
    - Execute custom scripts inside analyzed process
    - Hook functions, trace execution, modify behavior
    - Great way to fuzz internal functions without writing a harness

## Static analysis

* Using a tool to analyze a program's behavior without actually running it
    - Test whether a certain property holds or find places where it is violated
* Static analysis can *prove* some properties about the program that fuzzing and dynamic analysis can't
    - e.g. *prove* that a program has no NULL pointer dereferences
* Still lots of room for improvement
* However, cannot be perfect - reduces to halting problem
    - Can either be sound (everything found is a bug, but some bugs may be missed) or complete (finds every bug, but may be false positives)
    - Most are neither sound nor complete

### Data flow analysis

* Determine possible values of variables at points in the control flow graph
    - Needs approximations; express precise set of possible values may be arbitrarily complex
* Taint analysis: identify sources of "tainted data"
    - User/attacker input
    - Reads from files/network
    - Check if tainted data flows into a "trusted sink" - `memcpy`, `free`, `bzero`
* e.g. Clang static analyzer
    - Check for common security issues with a static analysis framework in the compiler
    - Built in checkers:
        - Bufer overflows
        - Refcount errors
        - `malloc` integer overflows
        - Insecure API use
        - Uninitialized value use
* e.g. CodeQL
    - Query language for finding patterns in large codebases
    - Works best with a specific bade code pattern in mind

## Manual analysis

* Reverse engineering: looking at a compiled program in order to figure out what it does and how it works
    - Usually assisted by disassembler, decompiler, `strings`
    - Often aided by dynamic analysis (i.e. tracing)
* e.g. IDA Pro: disassembly, decompilation, binary analysis, scripting
* e.g. Ghidra: like IDA Pro, but open source and written by the NSA

## Tips for writing secure software

* Software tests: one of the most effective ways to reduce bugs
    - Unit tests: check that each piece of code behaves as expected in isolation
        - Goal: Unit tests should cover all code, including error handling
        - Would eliminate many exploitable bugs
    - Regression tests: check that old bugs haven't been reintroduced
    - Integration tests: check that modules work together as expected
* General other tips:
    - Use modern, memory safe languages where possible: Go, Rust, Swift, etc.
    - Understand and document threat model early in design process
    - Design APIs and systems so that the easiest way to use them is the safe way
    - Treat all outside input adversarially, even if sender is trusted
    - Use clean, consistent style through codebase