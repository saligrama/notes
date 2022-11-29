# Heterogeneous Processing, Hardware Specialization, and Domain-Specific Languages

## Motivation

* A CPU is inefficient, even when using it as efficiently as possible
    - Lots of CPU time is spent on control-related tasks, rather than FP math - can this be amortized?
* Instead - think of a computer as a "swiss army knife" - some tasks may be faster on a CPU or a GPU, even within the same overall program
* Heterogeneous (specialized) hardware designs improve efficiency, but difficult to map applications to hardware

## Examples of heterogeneity

* Intel CPU: CPU cores + GPU (multiple cores itself), shared memory system and L3 cache enabling low-latency, high-bandwidth communication between CPU and GPU
* Workstation: add high-end discrete GPU across PCIe bus
    - Also itself heterogeneous! Some units are purely for graphical processing, not just for SIMD processing
* Mobile: System-on-a-Chip (SoC), dedicated cores for GPU, neural network accel., video compression/decompression, etc.
* Digital signal processors (DSPs): programmable processors, but with simpler instruction stream control paths
    - Complex instructions: perform many ops per instruction (amortize cost of control)
        - SIMD
        - Very Large Instruction Word (VLIW) - single instruction specifies multiple operations
* Anton supercomputer for molecular dynamics (D.E. Shaw Research)
    - Simulates protein evolution over time
    - ASIC for computing particle-particle interactions (512 in a machine)
* Google TPU pods for ML
* Google Pixel Visual Core
    - Programmable image-processing unit (IPU)
    - Each core: 16x16 grid of 16-bit mul-add ALUs
    - 10-20x more efficient than GPU at image processing tasks
* FPGAs (Field-Programmable Gate Arrays)
    - Middle ground between ASIC and processor: provides array of logic blocks, connected by interconnect
    - Programmer-defined logic implemented directly by FPGA

## Performance and power

* Note: performance is not the only goal, power consumption is important too!
    - Not just battery life for mobile devices, but ***heat***!
* Power = (ops/sec) * (joules/op), where ops/sec is performance and joules/op is energy efficiency
    - The amount of power usable is **fixed**
        - Either by regulation (phone case temperature)
        - Or b/c the processor itself can physically degrade if too hot
    - Specialization (fixed function) leads to better energy efficiency
* Data movement has high energy cost
    - Reading 64 bits from small local SRAM is ~26x energy cost as an integer op
    - Reading 64 bits from LPDDR is ~1200x energy cost as an integer op
        - Suggests that recomputing values, rather than storing and reloading them, is better when optimizing code for energy efficiency

## Efficiency benefits of compute specialization

* Rules of thumb: compared to high-quality C code on a CPU
* Throughput-maximized processor architecture e.g. GPU cores:
    - Approximately 10x improvement in perf/watt
    - Assuming code maps well to wide data-parallel execution and is compute bound
* Fixed-function ASIC (application-specific integrated circuit)
    - Can approach 100-1000x or greater improvement in perf/watt
    - Assuming code is compute bound and is not floating-point math

## Challenges of heterogeneous designs

* Challenges for developers: mapping applications onto heterogeneous collection of resources
    - Esp. scheduling
* Challenges for hardware designers: what is the right mixture of resources?

# Domain-Specific Programming Languages

* The ideal programming language balances performance, productivity, and generality - but this is impossible!
    - Performance, generality: C/C++, Java, Rust, CUDA
    - Productivity, generality: Python, Ruby, JS, PHP
    - Performance, productivity: SQL, Matlab, PyTorch
* DSLs: Programming languages with restricted expressiveness for a given domain
    - Main idea: raise level of abstraction for expressing programs
        - Goal: write one program, run it efficiently on different machines
    - Introduce high-level programming primitives specific to application domain
        - Productive: intuitive to use, portable across systems, primitives correspond to domain-specific behaviors
        - Performant: system uses domain knowledge to provide efficient, optimized implementation(s)
            - System can optimize code for the given task behind the scenes
    - Cost: loss of generality/completeness
* e.g. Halide language: simple domain-specific language embedded in C++ for describing secuences of image processing operations
    - Used in Android camera app