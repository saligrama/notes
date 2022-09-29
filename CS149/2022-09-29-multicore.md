# A Modern Multi-core Processor

## Part 1: Speeding up an example sine program

Computes `sin` across an array of `float` values using a Taylor series expansion

```c
void sinx(int N, int terms, float *x, float *y) {
    for (int i = 0; i < N; i++) {
        float value = x[i];
        float numer = x[i] * x[i] * x[i];
        int denom = 6; // 3!
        int sign = -1;

        for (int j = 1; j <= terms; j++) {
            value += sign * numer / denom;
            numer *= x[i] * x[i];
            denom *= (2 * j + 2) * (2 * j + 3);
            sign *= -1;
        }

        y[i] = value;
    }
}
```

This compiles to some assembly:

```arm
ld  r0, addr[r1] // x[i]
mul r1, r0, r0
mul r1, r1, r0
// ...
st  addr[r2], r0 // y[i]
```

### Pre-multicore era processor

* Majority of chip transistors used to perform ops that help make *single* instruction streams run fast
    - Out of order control logic
    - Fancy branch predictor
    - Memory prefetcher
    - More transistors = larger cache

### Multicore-era processor

* Idea: use transistor count to add more cores to the processor
    - Simple cores: each core maybe slowre at running a single instruction stream than original fancy core (e.g. 25% slower)
    - However, with two cores: 2 * 0.75 = 1.5 = 50% speedup!
* However: C program will compile to single instruction stream that runs on only one thread on one core

#### Transforming a program to multicore

```cpp
typedef struct {
    int N,
    int terms;
    float *x;
    float *y;
} my_args;

void my_thread_func(my_args *args) {
    sinx(args->N, args->terms, args->x, args->y); // do work
}

void parallel_sinx(int N, int terms, float *x, float *y) {
    std::thread my_thread;
    my_args args;
    args.N = N/2;
    args.terms = terms;
    args.x = x;
    args.y = y;

    my_thread = std::thread(my_thread_func, &args); // launch thread
    sinx(N - args.N, terms, x + args.N, y + args.N); // do work on main thread

    /* NOTE: because of this waiting, we can only ever get 2x speedup!! */
    my_thread.join(); // wait for thread to complete
}
```

#### Data-parallel expressions

```c
void sinx(int N, int terms, float *x, float *y) {
    // original:
    // for (int i = 0; i < N; i++) {

    // new: fictituous forall construct that tells the compiler
    // that each of these iterations are independent
    forall (int i from 0 to N) {
        float value = x[i];
        float numer = x[i] * x[i] * x[i];
        int denom = 6; // 3!
        int sign = -1;

        for (int j = 1; j <= terms; j++) {
            value += sign * numer / denom;
            numer *= x[i] * x[i];
            denom *= (2 * j + 2) * (2 * j + 3);
            sign *= -1;
        }

        y[i] = value;
    }
}
```

With such a construct, a compiler might be able to autogenerate threaded code.

### SIMD processing

* Single instruction, multiple data
* Additional execution units (ALUs) increase compute capability
* Idea: Amortize cost/complexity of managing an instruction stream across many ALUs
    - Same instruction broadcast to all ALUs
    - Operation executed in parallel on all ALUs
* e.g. [Original scalar program](#todays-example-program) processes one array element using scalar instructions on scalar registers
* Instead, use vector operations (i.e. `__m256` types and `__mm256` arith and load operations)
    - These are intrinsic datatypes and functions available to C programmers
    - These operate on vectors of eight 32-bit values (e.g. vector of 8 floats)
    * Compiled program processes eight array elements simultaneously using vector instructions on 256-bit vector register
* Note: with [Data-parallel expression](#data-parallel-expressions) example code, the `forall` abstraction can also facilitate autogeneration of SIMD instructions
    - In addition to multi-core parallel code!

#### Conditional execution with SIMD

Consider the following example

```cpp
forall (int i from 0 to N) {
    float t = x[i];
    // unconditional code
    if (t > 0.0) {
        t = t * t;
        t = t * 50.0;
        t = t + 100.0;
    } else {
        t = t + 30.0;
        t = t / 10.0;
    }

    // resume unconditional code
    y[i] = t;
}
```

* How to process this on a vector-only CPU?
    - Mask (discard) output of ALU
    - Run all with `true` branches, discard output of `false` branches
    - Run all with `false` branches, discard output of `true` branches
    - Worst case: 1/8 of peak performance
        - Can create this by making the `if` branch extremely costly, but processor is still forced to send all the branch that way
            ```c
            if (i == 0) {
                do_something_super_costly();
            } else {
                do_something_normal();
            }
            ```

#### SIMD jargon

* Instruction stream coherence ("coherent execution")
    - Property of a program where same instruction stream applies to many data elements
    - NECESSARY for SIMD processing to be used efficiently
    - NOT NECESSARY for efficient parallelization across different cores
* Divergent execution
    - Lack of instruction stream coherence

#### Modern CPU examples of SIMD

* AVX2 (Intel): 256b operations: 8x32bit or 4x64bit
* AVX512 (Intel): 512b: 16x32bit
* ARM Neon: 128b: 4x32bit
* Instructions generated by complier
    - Requested by programmer using intrinsics
    - Or parallel language semantics (e.g. `forall`)
    - Inferred by dependency analysis of loops by auto-vectorizing compiler
* Explicit SIMD: SIMD parallelization performed at compile time
    - SIMD instructions in binary itself (e.g., `vstoreps`, `vmulps`, etc.)

##### Example: Intel i7-7700K (Kaby Lake, 2017)

* 4 core CPU
* Three 8-wide SIMD ALUs per core for AVX2
* 4 cores x 8-wide SIMD * 3 * 4.2 GHz = 400 GFLOPs


#### SIMD on GPUs

* "Implicit SIMD"
    - Compiler generates binary with scalar instructions
    - But N instances of program always run together on processor
    - Hardware, not compiler, responsible for executing same instruction on multiple program instances on different data on SIMD ALUs
* SIMD width of most modern GPUs ranges from 8 to 32
    - Divergent execution can result in 1/32 of peak capacity

## Part 2: accessing memory

### Load/store: instructions that access memory

```arm
// load 4b value in memory starting from address stored by register r2
// and put it into register r0
ld r0 mem[r2]

// store 4b value in r0 into address stored by register r2
st r0 mem[r2]
```

### Memory terminology

* Memory address space
    - Memory organized as sequence of bytes
    - Each byte identified by address in memory
    - Address space: total addressable memory of a program
* Memory access latency
    - Amount of time it takes memory system to provide data to the processor
    - e.g. 100 clock cycles, 100 nsec
* Stalls
    - A processor "stalls" when it cannot run the next instruction in an instruction stream due to dependency on a previous incomplete instruction
    - e.g. Accessing memory can cause stalls
        ```arm
        ld r0 mem[r2]
        ld r1 mem[r3]
        // Dependency: cannot execute `add` instruction until data 
        // from mem[r2] and mem[r3] have been loaded from memory
        add r0, r0, r1
        ```
    - Memory access times: ~hundreds of cycles
        - Measure of latency

### Caches

* On-chip storage that maintains copy of subset of values in memory
* Address is "in the cache": processor can load and store address more quickly than if data resided in memory
* Hardware implementation detail that does not impact program output; only performance
* For this class, abstraction: assume cache of size N keeps last N addresses accessed
    - "LIFO" policy: on each memory access, throw out oldest data in cache to make room for newly accessed data
    - In real world, other policies:
        - Direct mapped cache
        - Set-associative cache
        - Cache line
* Reduce length of stalls and memory access latency
    - Specifically: when accessing data that had recently been accessed
* e.g. Data access times, Intel Kaby Lake (2017)
    - L1 cache: 4 cycles
    - L2 cache: 12 cycles
    - L3 cache: 38 cycles
    - DRAM: best case ~248 cycles

### Data prefetching

* Logic on modern CPUs for guessing what data will be accessed in the future
    - Prefetch this data into caches
    - Dynamically analyze program memory access patterns to make predictions
* Reduces stalls since data is already resident in cache when accessed
    - However: can reduce perf. if guess is wrong (consumes bandwidth, pollutes caches)

### Multithreading to reduce stalls

* Interleave processing of multiple threads on same core to hide stalls
    - Can't make process on one thread? Work on another one
    - Once hitting a stall on one thread, immediately switch over another one
* Idea: potentially increase time to complete a single thread's work in order to increase overall system throuput when running multiple threads
* However: this requires storing execution contexts
    - Tradeoff between memory caching and storing these execution contexts