# Programming for Hardware Specialization on ASICs and FPGAs

Recall: [performance and power](/notes/cs149/2022-11-29-heterogeneous-processing-and-domain-specific-languages/#performance-and-power), from 11/29's lecture

## Designing an accelerator for an algorithm

Instead of becoming an expert in VHDL or Verilog, Chisel, etc:

* High-level synthesis (HLS): Vivado HLS, Intel OpenCL, Xilinx SDAccel
    - Restricted C with pragmas
    - These tools sacrifice performance and are difficult to use
* Spatial: High-level language for designing HW accelerators 
    - Designed to enable specification of:
        - Parallelism: specialized compute
        - Locality: specialized memories and data movement

## Spatial: DSL for Accelerator design

* Simplify configurable accelerator design
    - Constructs to expressed:
        - Parallel patterns as parallel and pipelined data paths
        - Hierarchical control
        - Explicit memory hierarchies
        - Explicit parameters
    - All parameters exposed to compiler
    - Simple APIs to manage communication between CPU and accelerator
* Allows programmers to focus on "interesting stuff"
    - Designed for performance-oriented programmers to exploit parallelism and locality
    - More intuitive than CUDA: dataflow model rather than threading model

### Memory and register templates

```scala
// Memory hierarchy; typed storage templates
val buffer = SRAM[UInt8](C)
val image = DRAM[UInt8](H, W)

// Registers
val accum = Reg[Double]
val fifo = FIFO[Float](D)
val lbuf = LineBuffer[Int](R, C)
val pixels = ShiftReg[UInt8](R, C)

// Explicit transfers across memory hierarchy
// Dense and sparse access
buffer load image(i, j::j+c)
buffer gather image(a, 10)
```

### Inner product in Spatial

```scala
// set up host and mem ptrs
val output = ArgOut[Int]
val vec1 = DRAM[Int](N)
val vec2 = DRAM[Int](N)

// Create accelerator
Accel {
    // allocate on-chip memories
    val tile1 = SRAM[Int](tileSize)
    val tile2 = SRAM[Int](tileSize)

    // specify outer loop
    Reduce(output)(N by tileSize) { t =>
        // prefetch data
        tile1 load vec1(t :: t + tileSize)
        tile2 load vec2(t :: t + tileSize)

        // Multiply-accumulate data
        val accum = Reg[Int](0)
        Reduce(accum)(tileSize by 1 par 1) { i =>
            tile1(i) * tile2(i)
        }{a, b => a + b}
    }{a, b => a + b}
}
```
* Generates multi-step controllers
* Manages communication with DRAM
* Complete app generates three-step control: Load -> Intra-tile accumulate -> full accumulate