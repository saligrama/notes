# Sorting lower bounds

## Defining a valid sorting algorithm

Comparison-based model of computation (requires Ω(n log n) steps):

* Input: array
* Output: sorted array
* Operations allowed: comparisons

Another model:

* CountingSort and RadixSort
* Run in time O(n)

## Ω(n log n) bound for comparison sorting

Theorem:

* Any deterministic comparison-based sorting algorithm must take Ω(n log n) steps
* Any randomized comparison-based sorting algorithm must take Ω(n log n) steps in expectation

Argument:
* All comparison-based sorting algorithms give rise to a decision tree
    - Internal nodes correspond to yes/no (>= / <) decisions
    - Leaf nodes correspond to outputs (in this case, all possible orderings of the items)
* Running an algorithm on a particular input corresponds to a particular path through the tree
* In all such trees, the longest path is at least log(n!) ~= log((n/e)<sup>n</sup>) = n ((log n) - 1) = O(n log n)
* Thus traversing a path on the decision tree is worst case O(n log n)
    - MergeSort is optimal!

# O(n) sorting\*

## CountingSort

* Suppose we have an array of numbers (e.g. [9, 6, 3, 5, 2, 1, 2])
* Set up buckets for values 1..9
* Foreach number in the array, put the number in its respective buckets
* Concatenate the buckets

Assumptions:

* Need to be able to know what bucket to put something in
    - Need to evaluate items directly, not just by comparison
* Need to know what values might show up ahead of time
* Need to assume there are not too many such values

## RadixSort

* For sorting integers up to size M, or more generally for lexicographically sorting strings
* Can use less space than CountingSort
* Idea: CountingSort on least significant digit, then next significant, etc
    - Treat each bucket as FIFO, then concatenate
* Running time: O((log<sub>r</sub>M + 1)(n+r))
    - Choose n = r: O(n(log<sub>n</sub>M + 1))
    - If M <= n<sup>c</sup> for some constant c, then this is O(n)
    - If M >= n<sup>c</sup> for some constant c, then this is O(n<sup>2</sup>/log n)