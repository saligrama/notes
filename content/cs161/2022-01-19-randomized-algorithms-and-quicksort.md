# Randomized Algorithms

Idea: make random choices during the algorithm

* Hope the algorithm works
* Hope the algorithm is fast

Today: look at algorithms that always work and are probably fast

- e.g. `select` with random pivot

## Analysis of randomized algorithms

Scenario 1: publish algorithm, adversarially picked input, run algorithm (expected running time - as a random variable)

Scenario 2: publish algorithm, adversarially picked input *and randomness* (worst-case analysis)

### Bogosort

Algorithm `BogoSort(A)`:
* While true:
    - Randomly permute A (O(n) via Knuth shuffle)
    - Check if A is sorted
    - If A is sorted, return A

Analysis:

* Let x<sub>i</sub> be 1 if A is sorted after iteration i and 0 otherwise
    - E[x<sub>i</sub>] = 1/n! (n! permutations, and only one of them is sorted)
    - E[number of iterations until A is sorted] = n!
* Expected running time of Bogosort:
    - E[running time on a list of length n] = E[number of iterations * time per iteration] = (time per iteration)E[number of iterations] = O(n * n!)
* Worst case runtime: infinite!

### Quicksort

Algorithm `QuickSort(A)`:
* If len(A) <= 1:
    return
* Pick some x = A[i] at random; let x be the **pivot**
* Partition the rest of A into
    - L (less than x)
    - R (greater than x)
* Rearrange A into with [L, x, R]
* QuickSort(L)
* QuickSort(R)

Analysis:

* T(n) = T(|L|) + T(|R|) + O(n)
* Ideally, if the pivot splits the array exactly in half:
    - T(n) = 2T(n/2) + O(n)
    - T(n) = O(n log n)
* What happens in average/worst case?

**Theorem**: the expected running time of quicksort is O(n log n)

**Proof**: count the number of comparisons made by the algorithm (see lecture slides)