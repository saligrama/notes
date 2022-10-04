# Parallel Abstractions and Implementation

## ISPC

* Intel SPMD (single program, multiple dat) Program Compiler: https://ispc.github.io

e.g. `sinx` using ISPC

```cpp
export void ispc_sinx(
    // uniform: type modifier for optimization, not necessary for correctness
    uniform int N,
    uniform int terms,
    uniform float *x,
    uniform float *result
) {
    // programCount: keyword, number of simultaneously executing gang instances
    for (uniform int i = 0; i < N; i += programCount) {
        // programIndex: id of current gang instance
        int idx = i + programIndex;
        float value = x[idx];
        float numer = x[idx] * x[idx] * x[idx];
        uniform int denom = 6;
        uniform int sign = -1;

        for (uniform int j = 1; j <= terms; j++) {
            value += sign * numer / denom;
            numer *= x[idx] * x[idx];
            denom *= (2*j+2) * (2*j + 3);
            sign *= -1;
        }

        result[idx] = value;
    }
}

int main (int argc, char **argv) {
    // initializations

    ispc_sinx(N, terms, x, result);
    return 0;
}
```

* ISPC execution
    - Call to ISPC function spawns "gang" of ISPC "program instances"
    - All instances run ISPC code concurrently
    - Each instance has its own copy of local variables
    - Upon return, all instances have completed
* What ISPC does
    - Turns instructions into SIMD at compilation time
        - Number of instances in a gang: SIMD width of the hardware (or small multiple thereof)
    - Does not spawn new threads!