# Worst-case and asymptotic analysis

## Worst-case analysis

* An algorithm must be correct on all possible inputs
* The running time of the algorithm is the worst possible running time over all inputs
    - That is, if we design a purposefully adversarial input, the algorithm should still work on that input
* Pro and con: very strong guarantee

## Asymptotic analysis: Big-O notation

* Meaningful way to talk about the runtime of an algorithm, independent of programming language or computing platform, without having to count all of the operations
* Focuses on how the runtime scales with input size *n*
* Highest-degree term in the sum is the only one that counts

e.g. 

(n<sup>2</sup>/10) + 100 -> O(n<sup>2</sup>)

0.063n<sup>2</sup> - 0.5n + 12.7 -> O(n<sup>2</sup>)

100n<sup>1.5</sup> - 10<sup>10000</sup>sqrt(n) -> O(n<sup>1.5</sup>)

11 n log(n) + 1 -> O(n log(n))

Pros:
* Abstracts away hardware- and language- specific issues
* Makes algorithm analysis more tractable
* Allows us to meaningfully compare how algorithms will perform on large inputs

Cons:
* Only makes sense if *n* is large compared to the constant factors

### Definition for O(...)
* Let T(n), g(n) be functions of positive integers
    - Consider T(n) as a runtime: positive and increasing in *n*
* We say "T(n) is O(g(n))" if for large enough *n*, T(n) is at most some constant multiple of g(n)
* Formally, T(n) = O(g(n)) if there exists a *c*, *n<sub>0</sub>* > 0 such that for each *n* > *n<sub>0</sub>*, T(n) <= c g(n)

### Definition for Ω(...)
* We say "T(n) is Ω(g(n))" if for large enough *n*, T(n) is at least as big as a constant multiple of g(n)
* Formally, T(n) = Ω(g(n)) if there exists a *c*, *n<sub>0</sub>* > 0 such that for each *n* > *n<sub>0</sub>*, T(n) >= c g(n)

### Definition for Θ(...)
* We say "T(n) is Θ(g(n))" iff both
    - T(n) = O(g(n))
    - T(n) = Ω(g(n))

### Proofs
* To prove T(n) = O(g(n)), need to come up with *c* and *n<sub>0</sub>* such that the definition is satisfied
* To prove T(n) is not O(g(n)), use proof by contradiction:
    - Come up with *c* and *n<sub>0</sub>* such that the definition is satisfied

# Examples

## Sorting

### Basics
* Idea: take array of unsorted items (i.e., [6, 4, 3, 8, 1, 5, 2, 7]) and sort it in nondecreasing order (i.e., [1, 2, 3, 4, 5, 6, 7, 8])
* For convenience, assume all items are distinct and length of list is *n*

### Insertion Sort

* At each iteration i starting with i=1:
    - Move A[i] towards the beginning of the list until we can't find anything smaller or can't go further

#### Worst-case analysis on Insertion Sort
Claim: Insertion Sort is correct.

Proof: Induction.

1. Base case: empty array (A[:1]) is sorted.
2. Inductive hypothesis: At the *i*'th iteration of the outer loop, A[:i+1] is sorted, for 0 <= i < k
3. Inductive step: Consider the *k*'th iteration. Insertion sort will move A[k] to its appropriate position (i.e., it will move it to a position *p* where A[p-1] < A[p] < A[p+1]). Thus A[:k+1] is sorted at the end of the *k*'th iteration, as required. Thus insertion sort is correct for 0 <= k < n.

#### Asymptotic analysis
* O(n<sup>2</sup>)

### Merge sort

* Algorithm MergeSort(A):
    - if len(A) <= 1, return A
    - L = MergeSort(A[:len(A)/2])
    - R = MergeSort(A[len(A)/2:])
    - return Merge(L, R)

#### Worst-case analysis on Merge Sort

Claim: Merge Sort is correct

Proof: Induction.

1. Base case (i=1): One-element array is always sorted.
2. Inductive hypothesis: In every recursive call on an array of length at most *i*, MergeSort returns a sorted array
3. Inductive step: Assume inductive hypothesis holds for k < i. Then it holds for k=i because if left and right halves are sorted, then Merge(L, R) is sorted. Therefore, this works and in the top recursive call, MergeSort returns a sorted array.

#### Asymptotic analysis on MergeSort

* Runs in O(n log(n))
* Why?
    - At each step, we divide A into L and R arrays, where |L| = |R| = |A|/2
    - There are log(n) of these divisions = log(n) recursive calls
    - Each Merge(L, R) takes O(n) time to finish.
    - Thus, O(n log(n))