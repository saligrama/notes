---
math: true
---

# Data-parallel thinking

## Motivation

* Idea: express algorithms in terms of operations on sequences of data
    - e.g. map, filter, fold/reduce, scan/segmented scan, sort, groupby, join, partition/flatten
    - High-performance parallel versions exist; so can we reframe programs in these terms to make them run efficiently on a parallel machine?
* In general: applications need to expose a large amount of data parallelism to make use of
    - High core counts
    - Multiple machines
    - SIMD processing
    - GPU architectures

## Key terms and operations

* Data-parallel model, sequences, `map`: recall [Lecture 4's intro on data parallelism](/notes/cs149/2022-10-06-parallel-models/#data-parallel-model)

### Fold (fold left)

* Apply binary operation `f` to each element and an accumulated value; seeded by initial value of type `b`
    ```
    f :: (b, a) -> b
    fold :: 
        b -> # initial element
        ((b, a) -> b) -> # function to fold
        seq a -> # input sequence
        b # output
    ```
    - e.g. `fold(10, OP_ADD, [3, 8, 4, 6, 3, 9, 2, 8])` results in `sum([10, 13, 21, 25, 31, 34, 43, 45, 53]) = 53`

#### Parallel fold

* In addition to `f`, need additional binary "combiner" function
    - Need IV `b` (must be identity for `f` and `comb`)
    ```
    f :: (b, a) -> b
    b :: (b, b) -> b
    fold_par ::
        b ->
        ((b, a) -> b) ->
        ((b, b) -> b) -> # combiner
        seq a ->
        b
    ```
    - Idea: break up input into `nthreads` partial sequences, apply sequential fold to each, and then combine using ground-up merging of pairs of outputs

### Scan

* Like fold, but without an initial value and outputs a sequence
    ```
    f :: (a, a) -> a
    scan ::
        a ->
        ((a, a) -> a) ->
        seq a ->
        seq a
    ```
* Serial implementation:
    ```c++
    float op_add(float a, float b) { return a + b };
    
    void scan_inclusive(
        float *in,
        float *out,
        int N, 
        float(*op)(float, float)
    ) {
        out[0] = in[0];
        for (int i = 1; i < N; i++) {
            out[i] = op(out[i-1], in[i]);
        }
    }

    // scan_inclusive([3, 8, 4, 6, 3, 9, 2, 8], [], N, op_add)
    // returns [3, 11, 15, 21, 24, 33, 35, 43]
    ```

#### Parallel scan

* Naive parallelization of operation results in $O(n \log n)$ runtime: inefficient vs serial algorithm
    ![Naive parallel scan](https://archive.ph/vBlNa/8759a7506eb7bfa99a66c1ebee37c541caf3789d.jpg)
* Instead: work-efficient parallel scan ($O(n)$ work) algorithms exist, however they do not make efficent use of SIMD instructions
    ![Work-efficient, poor SIMD, parallel scan](https://archive.ph/N4wVi/14dc4ba39a4db5f393b0345627321255fccf55dd.jpg)
* SIMD implementation (in CUDA):
    ```c++
    __device__ int scan_warp(int *ptr, const unsigned int idx) {
        const unsigned int lane = idx % 32; // index of thread in warp

        if (lane >= 1) ptr[idx] += ptr[idx - 1];
        if (lane >= 2) ptr[idx] += ptr[idx - 2];
        if (lane >= 4) ptr[idx] += ptr[idx - 4];
        if (lane >= 8) ptr[idx] += ptr[idx - 8];
        if (lane >= 16) ptr[idx] += ptr[idx - 16];

        return (lane > 0) ? ptr[idx-1] : 0;
    }
    ```
    - Despite doing $O(n \log n)$ work, this actually takes 2x fewer instructions on a SIMD machine as the work-efficient version

### Segmented scan

* Common problem: operate on sequence of sequences
* Segmented scan: simultaneously perform scans on contiguous partitions of input sequence
    - e.g. `segmented_scan_exclusive(OP_ADD, [[1, 2], [6], [1, 2, 3, 4]]) = [[0, 1], [0], [0, 1, 3, 6]]`
* Useful for sparse matrix multiplication

### Gather/scatter

* Gather: `gather(index, input, output): output[i] = input[index[i]]`
    - Implemented as SIMD native instruction in AVX2 (2013)
* Scatter: `scatter(index, input, output): output[index[i]] = input[i]`
    - Implmenented as SIMD native instruction in AVX512 (2016)