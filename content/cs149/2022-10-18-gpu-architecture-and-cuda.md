# GPU architecture and CUDA

## GPU history

### Graphical purpose of a GPU

* Initially designed for:
    - Input: description of a scene; mathematical description
        - e.g. 3D surface geometry, surface materials, lights, camera, etc.
    - Output: image of the scene

### Rendering task

* Real-time graphics primitives (entities)
    - Surfaces represented as 3D triangle meshes: vertices (points in space), primitives (points, lines, triangles)
* Goal: compute how each triangle in 3D mesh contributes to overall image
    - Subtask workload: given triangle, determine where it lies on screen given position of virtual cameras
    - For all output image pixels covered by triangle, compute color of surface at that pixel
* Shader program: run once per fragment (per pixel covered by triangle)
    - Inputs: variable values that change per pixel
    - Outputs: colors at those pixels
    - Pixels covered by multiple surfaces contain output from surfaces closest to camera
* To optimize: GPUs designed with multiple core, high-throughput (lots of SIMD and multithreading) architecture

### GPUs for scientific and compute task

* Initial observation (2001-2003): GPUs are *very fast* processors for performing same computation in parallel on large collections of data
    - Data parallelism!
    - Packing more transistors on the same chip = more parallelism
* Lead to early GPU-based scientific computation: hack 
    - Map 512x512 array onto on "image"
    - Render two triangles that exactly cover screen
    - Apply "shader" computation on the collection
* Brook Stream programming language (Stanford, 2004): abstracted GPU hardware as a data-parallel processor
    - Translated generic stream program into graphics commands that could be run on GPUs
    - However: programs limited to graphics-specific APIs; needed to set image sizes, vertices, etc. and use "drawing" abstractions for computation

## CUDA: Modern GPU compute

* NVIDIA Tesla architecture (2007)
    - First alternative, "compute mode" interface to GPU hardware
    - Application can allocate buffers in GPU memory and copy data to/from buffers
    - Application (via graphics driver) provides GPU a single kernel program binary
    - Application tells GPU to run kernel in SPMD fashion (i.e., run N instances of the kernel): `launch(myKernel, N)`

### Terminology

* CUDA: the abstraction above; "C-like" language to express GPU programs using compute hardware interface
    - Now subset of C++
    - Low-level; abstractions match capabilities/perf. characteristics of modern GPUs
* **Note on CUDA thread**: similar API abstraction as `pthread` corresponding to logical thread of control
    - However: **very different implementation!!**

### CUDA progams and syntax

* Hierarchy of concurrent threads
    - Thread IDs can be up to 3-dimensional; abstraction useful for naturally N-dimensional programs
* Example: `matrixAdd`
    ```c++
    // begin host code: C/C++ on CPU
    const int Nx = 12;
    const int Ny = 6;

    dim3 threadsPerBlock(4, 3);
    dim3 numBlocks(Nx/threadsPerBlock.x, Ny/threadsPerBlock.y);
    // end host code

    // assume A, B, C **on GPU** allocated as Nx x Ny float arrays
    // call launches 72 CUDA threads: 6 thread blocks of 12 threads each
    // "launch a grid of CUDA thread blocks, return when all threads are terminated"
    matrixAdd<<<numBlocks, threadsPerBlock>>>(A, B, C)

    // kernel definition: runs on GPU
    // __global__ denotes CUDA kernel function for device
    // A, B, C are pointers/references to GPU memory
    __global__ void matrixAdd(float A[Ny][Nx], float B[Ny][Nx], float C[Ny, Nx]) {
        // each thread computes overall grid thread id from position in block (threadIdx) and block position in grid (blockIdx)
        int i = blockIdx.x * blockDim.x + threadIdx.x;
        int j = blockIdx.y * blockDim.y + threadIdx.y;

        C[j][i] = A[j][i] + B[j][i];
    }
    ```
    - Note: number of SPMD "CUDA threads" is explicit in the program
        - Number of kernel invocations not determined by size of data collection
        - Kernel launch not specified by `map(kernel, collection)` like with graphics shader processing
* Compiled CUDA device binary includes program text (instructions), and information about required resources
    - Threads per block
    - Bytes of local data per thread
    - Shared space per thread block

### CUDA memory model

* CPUs and GPUs have different memory address spaces!
* To reconcile: `memcpy` primitive, like message-passing setup
    ```c++
    float *A = new float[N]; // allocate buffer in host CPU memory
    for (int i = 0; i < N; i++) // fill the buffer in CPU host space
        A[i] = (float) i;
    
    // allocate buffer in device address space
    int bytes = sizeof(float) * N;
    float *deviceA;
    cudaMalloc(&deviceA, bytes);

    // populate deviceA
    cudaMemcpy(deviceA, A, bytes, cudaMemcpyHostToDevice);

    // note: cannot manipulate deviceA[i] from host (invalid operation), due to different address spaces
    ```

### CUDA device memory model

* Three distinct address spaces visible to kernels:
    - Per-block shared memory: r/w by all threads in block
    - Per-thread private memory: r/w by thread
    - Device global memory: r/w by all threads
* Address spaces represent different regions of locality
    - Has implications on CUDA implementation efficiency
    - e.g. consider scheduling of threads based on prior knowledge of which threads access the same variables

### CUDA example: 1D Convolution

* Convolution: each output is an average of the inputs surrounding it
    - `output[i] = (input[i] + input[i+1] + input[i+2]) / 3.f`
* CUDA, version 1: one thread per output element
    - CUDA kernel:
        ```C++
        #define THREADS_PER_BLK 128

        __global__ void convolve(int N, float *input, float *output) {
            int index = blockIdx.x * blockDim.x + threadIdx.x; // thread-local variable

            float result = 0.0f; // thread-local variable
            for (int i = 0; i < 3; i++)
                result += input[index + i]; // thread computes result for one element
            
            output[index] = result / 3.f; // write result to global memory
        }
        ```
    - Host code:
        ```c++
        int N = 1024 * 1024;
        cudaMalloc(&devInput, sizeof(float) * (N+2)); // allocate in dev mem
        cudaMalloc(&devOutput, sizeof(float) * N); // allocate in dev mem

        // omitted: initialize arrays here

        convolve<<<N/THREADS_PER_BLK, THREADS_PER_BLK>>>(N, devInput, devOutput);
        ```
    - Lots of overhead from threads only computing result for one element (more loads per thread than necessary)
* CUDA, version 2: one thread per output element: stage input data in per-block shared memory
    - CUDA kernel:
        ```C++
        #define THREADS_PER_BLK 128

        __global__ void convolve(int N, float *input, float *output) {
            __shared__ float support[THREADS_PER_BLK+2]; // per-block allocation
            int index = blockIdx.x * blockDim.x + threadIdx.x; // thread-local variable

            // all threads cooperatively load block's support region from global mem to shared mem: 130 load instructions vs 3*128
            support[threadIdx.x] = input[index];
            if (threadIdx.x < 2) {
                support[THREADS_PER_BLK + threadIdx.x] = input[index + THREADS_PER_BLK];
            }

            __syncthreads(); // barrier: all threads in block

            float result = 0.0f; // thread-local variable
            for (int i = 0; i < 3; i++)
                result += support[threadIdx.x + i]; // thread computes result for one element
            
            output[index] = result / 3.f; // write result to global memory
        }
        ```
    - Host code:
        ```c++
        int N = 1024 * 1024;
        cudaMalloc(&devInput, sizeof(float) * (N+2)); // allocate in dev mem
        cudaMalloc(&devOutput, sizeof(float) * N); // allocate in dev mem

        // omitted: initialize arrays here

        convolve<<<N/THREADS_PER_BLK, THREADS_PER_BLK>>>(N, devInput, devOutput);
        ```
    - Faster, due to less load operations per thread

### CUDA synchronization constructs

* `__syncthreads()`: barrier, wait for all threads in the block to arrive at this point
* Atomic operations
    - e.g. `float atomicAdd(float *addr, float amount)`
    - Provided on both global and per-block shared mem addrs
* Host/device synchronization: implicit barrier across threads at kernel return

### Assigning work

* Desirable for CUDA program to run on any size of GPU without modification
* Thread-block assignment:
    - Assumption: thread block execution can be carried out in any order w/o dependencies between blocks
    - GPU implementation maps thread blocks ("work") to cores using dynamic scheduling policy respecting resource requirements
    - Just like thread-pool model!

## GPU architecture and threading

### Sub-core

* A group of 32 threads in a thread block is called a *warp*
    - Thread IDs numbered consecutively: warp with 0-31, warp with 32-63, etc.
    - A block with 256 CUDA threads is mapped 8 warps
    - Each sub-core can schedule and interleave execution of up to 16 warps (NVIDIA V100, 2017)
* Threads in a warp executed SIMD if they share the same instruction
    - Otherwise, performance suffers due to divergent execution

### Streaming multiprocessor (SM)

* SM architecture each clock:
    - Each sub-core selects one runnable warp (from 16 warps in partition)
    - Each sub-core runs next instruction for CUDA threads in warp
        - May apply to all or subset of CUDA threads in warp due to divergence
* NVIDIA V100 (2017) has 80 SMs
    - Overall architecture: 1.245 GHz per clock, 80 SM cores per chip: 5120 fp32 mul-add ALUs = 12.7 TFLOPs
    - Up to 5120 interleaved warps per chip (163,840 CUDA threads/chip)

## Running a CUDA program on a GPU

* Running a CUDA kernel has execution requirements, e.g. for `convolve`:
    - Each thread block must execute 128 CUDA threads
    - Each thread block must alloc `130 * sizeof(float)` bytes of shared mem.
    - Assume `N` very large, so host-side kernel launch generates thousands of thread blocks
    - Assume fictituous two-core GPU

### Execution steps

1. Host sends CUDA device a command: execute kernel
    ```
    EXECUTE: convolve
    ARGS: N, input_array, output_array
    NUM_BLOCKS: 1000
    ```
2. Scheduler maps block 0 to core 0: reserves execution contexts for required resources
    ```
    NEXT = 1
    TOTAL = 1000
    ```
3. Scheduler continues blocks to available execution contexts in interleaved fashion
    ```
    NEXT = 2
    TOTAL = 1000
    ```
4. If next thread block won't fit on a core (e.g. due to insufficient storage): wait for a block to finish, then schedule the next block on that core