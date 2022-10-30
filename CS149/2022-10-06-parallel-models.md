# Models of Parallel Programming

Three types of models:

* Shared address space: very little structure to communication
* Message passing: communication is structured in the form of messages
    - Communication explicit in source code
* Data parallel structure: more rigid structure to computation
    - Same function on collection of large elements

## Shared address space model

* All threads access the same memory
    - Requires coordinated access to shared data using locks, e.g.
        ```cpp
        // thread 1
        int x = 0;
        Lock my_lock;

        spawn_thread(foo, &x, &my_lock);

        my_lock.lock();
        x++;
        my_lock.unlock();

        // thread 2
        void foo (int *x, Lock *my_lock) {
            my_lock->lock();
            x++;
            my_lock->unlock();

            std::cout << x << std::endl;
        }
        ```
    - Locks are needed because instructions to do adds and prints may get interleaved; locks make programmer-intended semantics atomic
        - Note: can sometimes specify atomicity in other ways:
            - Language-supported atomicity: `atomic { ... }`
            - Hardware-supported atomic read-modify-write intrinsics: `atomicAdd(x, 10)`
* Any processor can directly reference any memory location
    - On some processors, ring or grid bus connects cores to enable this access
    - Non-uniform memory access (NUMA): latency of accessing a memory location may be different from different physical cores

## Message-passing model

* Threads operate within own private address space
* Communication via sending/receiving messages
    - *send*: specifies recipient, buffer to be transmitted, identifier (tag)
    - *receive*: sender, specifies buffer to store data, identifier (tag)
* This allows hardware to operate in parallel without performing systemwide loads and stores
    - Can create a large parallel machine using connected message-passing commodity machines
        - e.g. Clusters and supercomputers do this

## Data-parallel model

* Organize computation as operations on sequences of elements
    - e.g. same function on all elements of a sequence
* Common example: numpy `C = A + B`, where `C,A,B` are vecs of equal length
* Sequence: key data type of the model
    * C++-like: `Sequence<T>`
    * Scala: `List[T]`
    * Haskell: `seq T`
    * numpy: `ndarray`
* Program can only access elements of sequence through sequence operators, e.g. `map`, `reduce`, `scan`, `shift`, etc

### Map

* Function that takes a function as an argument that operates on sequences
* Applies function `f :: a -> b` to elements of input sequence, to produce output sequence of same length
    - Function should be side-effect-free
* Parallelization: due to side-effect-freeness, applying `f` to all elements of the sequence can be done in any order without changing program correctness
    - Map implementation can reorder or parallelize element processing when convenient

### Data parallelism in ISPC

```c
export void absolute_value(
    uniform int N,
    uniform float *x,
    uniform float *y
) {
    // loop body is the function that gets mapped over x
    // by the foreach loop
    foreach (i = 0...N) {
        if (x[i] < 0)
            y[i] = -x[i];
        else
            y[i] = x[i];
    }
}
```

# Parallel programming basics

## Creating a parallel program

* Process:
    1. Identify work that can be performed in parallel
    2. Partition work and associated data
    3. Manage data access, communication, synchronization
* Goal: maximize speedup
    - Speedup(P processors) = Time(1 processor)/Time(P processors)

### Problem decomposition

* Break up problem into tasks that *can* be performed in parallel
    - Need to create enough tasks to keep all execution units on a machine busy
    - Challenge: identifying dependencies
        - Amdahl's law: dependencies limit maximum speedup due to parallelism
            - If `S` is the fraction of work that must be performed in serial, the maximum speedup is at most `1/S`
* e.g. two-step computation on NxN image
    - Steps: multiply brightness of all pixels by two, then compute average of all pixel values
    - Sequential implementation: time is ~2N<sup>2</sup>
    - Parallelization:
        - Step 1: N<sup>2</sup>/P
        - Step 2: compute partial sums in parallel, combine serially
            - P + N<sup>2</sup>/P
        - Maximum speedup: 2N<sup>2</sup>/((2N<sup>2</sup>/P) + P)
* Usually: programmer responsible for program decomposition
    - Automatic decomposition: open research programming

### Partitioning and assigning work

* Assigning tasks (things to do) to threads (workers)
* Goals: achieve good workload balance and low comm. costs
* Can be done statically (pre-execution) or dynamically
* Assignment is usually the responsibility of the language

#### Assignment in ISPC

```c
void foo(
    uniform float *input,
    uniform float *output,
    uniform int N
) {
    launch[100] my_ispc_task(input, output, N);
}
```
* ISPC task abstraction: managed assignment of tasks to threads
    - After completing current task, worker thread inspects list and assigns itself next remaining task
    - Thread pool abstraction: no need to keep respawning threads
        - **Note: ISPC tasks are an abstraction for *work*, not threads!!**
        - ISPC tasks are units of work that are put on a queue, that are then pulled off the queue by worker threads
        - The number of worker threads is the number of execution units on the hardware

### Thread orchestration

* Involves communication structuring, dependency synchronization, data structure organization in memory, task scheduling
* Goal: reduce comm/sync costs, preserve data reference locality, reduce overhead
* May be impacted by machine details

### Mapping threads to hardware

* Can be done at various points in the stack
    - OS: thread to HW execution context on CPU core
    - Compiler: map ISPC program instances to SIMD vector lanes
    - Hardware mapping: map CUDA thread blocks to GPU cores
* Mapping decisions:
    - Place related threads on the same processor for better data reference
    - Place unrelated threads on the same processor (e.g., bandwidth limits vs compute limits) to improve efficiency