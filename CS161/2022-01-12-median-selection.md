# The k-SELECT problem

* Let *A* be an array of integers and let *k* be an integer
* Goal: Return the *k*'th smallest element of *A*
* e.g.
    - A = [7, 4, 3, 8, 1, 5, 9, 14]
    - SELECT(A, 1) = 1
    - SELECT(A, 2) = 3
    - SELECT(A, 3) = 4
    - SELECT(A, 8) = 14
    - SELECT(A, 1) = MIN(A)
    - SELECT(A, n/2) = MEDIAN(A)

## O(n log n) algorithm
* Very simple! Sort the array, return A[k-1]

## What we want: an O(n) algorithm

Ex. MIN(A) = SELECT(A, 1)

* ret = MAX_INT
* for i in 0..n-1:
    - ret = min(ret, A[i])
* return ret

### Divide and conquer

Algorithm SELECT(A, k)

* First, pick a "pivot"
* Next, partition the array into "bigger than pivot" (L) and "less than pivot" (R)
* if k = len(L) + 1:
    - return A[pivot]
* if k < len(L) + 1:
    - return SELECT(L, k)
* if k > len(L) + 1:
    - return SELECT(R, k - len(L) - 1)

### Running time

* T(n) =
    - T(len(L)) + O(n) if len(L) > k-1
    - T(len(R)) + O(n) if len(L) < k-1
    - O(n) if len(L) = k-1
* Ideal pivot would split the input in half; however, finding such a pivot is itself an application of SELECT
    - Yields O(n) algorithm
* "Good enough" pivot approximates median: median of medians of groups of size 5, which takes O(n) time to find
* Alternatively, random pivot performs fine *on average*