# Applications of Dynamic Programming

## Longest Common Subsequence

### Definition

* **Subsequence**: chosen sequential subset of a sequence
    - e.g.: *BDFH* is a subsequence of A*B*C*D*E*F*G*H*
* If X, Y are sequences, a **common sequence** that is a subsequence of both
    - e.g. *BDFH* is a subsequence of A*B*C*D*E*F*G*H* and of A*BDF*G*H*I
* **Longest common subsequence (LCS)** is a common subsequence that is the longest
    - e.g. *ABDFGH* is the LCS of of *AB*C*D*E*FGH* and of *ABDFGH*I

### Dynamic programming steps

1. Optimal substructure
    - Subproblems will be finding LCS's of prefixes to X and Y
    - Let `C[i, j] = length_of_LCS(X[i], Y[j])`
    - Case 1: `X[i] = Y[j]`
        - Then `C[i, j] = 1 + C[i-1,j-1]` (because we can continue the previous subsequences by adding the new common character)
    - Case 2: `X[i] != Y[j]`
        - Then `C[i, j] = max(C[i-1, j], C[i, j-1])` (try possibilities for prefix considering previous considered characters)
2. Recursive formulation of the optimal solution
    - Case 0: `i = 0 || j = 0` -> `C[i, j] = 0`
    - Case 1: `X[i] = Y[j] && i,j > 0` -> `C[i, j] = C[i-1, j-1] + 1`
    - Case 2: `X[i] != Y[j] && i,j > 0` -> `C[i, j] = max(C[i-1, j] + C[i, j-1])`
3. Bottom-up dynamic programming formulaton
    ```python
    def lcs(X, Y):
        C = [ [0] * len(X) ] * len(Y)
        for i in range(1, len(X)):
            for j in range(1, len(Y)):
                if X[i] = Y[j]:
                    C[i][j] = C[i-1][j-1] + 1
                else:
                    C[i][j] = max(C[i-1, j], C[i, j-1])
        return C[len(X)-1][len(Y)-1]
    ```

Notes:
- Can cut down on space complexity from `O(mn)` to `O(n)` by only keeping two rows
    - However, if we want to recover the actual LCS (instead of its length) we need all rows

## Knapsack Problem

### Definition

* Have a number of items, each with some weight and some value
* Have a knapsack with maximum weight capacity
* Goal: find maximum value (sum) that can be fit into the knapsack given maximum weight constraint
* **Unbounded knapsack**: have infinite copies of all items
* **0/1 knapsack**: can only use an item once
* Notation: `w[i], v[i]` weight and value for item `i`, `W` knapsack capacity

### Dynamic programming steps (unbounded)

1. Optimal substructure
    - Subproblem: unbounded knapsack with a smaller knapsack
    - `K[x]`: value that can be fit into a knapsack of capacity `x`
    - Suppose we have optimal solution for capacity `x` that contains at least one copy of item `i`
        - Then the optimal solution to `x - w[i]` removes that item `i`, since if there was a different optimal solution, we could do better for `x`
2. Recursive relationship
    - Let `K[x]` be the optimal value for capacity `x`
        - Then `K[x] = max`<sub>i</sub>`(K[x-w[i]] + v[i])`
        - (`K[x] = 0` if there are no `i` such that `w[i] < x`)
3. Bottom-up dynamic programming formulation
    ```python
    def unboundedKnapsack(W, w, v):
        K = [0] * (W+1)
        for x in range(1, W+1):
            for i in range(len(w)):
                if w[i] < x:
                    K[x] = max(K[x], K[x-w[i]] + v[i])
        return K[w]
    ```

Notes:
- Runtime: O(nW), whereas input size is n log W -> this is not a polynomial time algorithm, however there is not known to be anything faster
- Space complexity O(W)
- Can add item accounting to return the actual items that go in the knapsack (O(nW) space)

### Dynamic programming steps (0/1)

1. Optimal substructure
    - Subproblem: 0/1 knapsack using up to the first `j` items and with smaller knapsack of capacity `x`
    - `K[x, j]`: optimal solution for a knapsack of capacity `x` using only the first `j` items
    - Case 1: Optimal solution for `j` items does not use item `j`
        - Then optimal solution for `j` items is the same as for `j-1` items
        - `K[x, j] = K[x, j-1]`
    - Case 2: Optimal solution for `j` items uses item `j`
        - `K[x, j] = K[x-w[j], j-1]`
2. Recursive relationship
    - Let `K[x, j]` be the optimal value for capacity `x` with `j` items
    - Then `K[x, j] = max(K[x, j-1], K[x-w[j], j-1] + v[j])`
3. Bottom-up dynamic programming formulation
    ```python
    def zeroOneKnapsack(W, w, v):
        K = [ [0] * (W+1) ] * len(w)
        for x in range(1, len(W)+1):
            for j in range(1, len(w)):
                K[x][j] = K[x][j-1]
            if w[j] <= x:
                K[x][j] = max(K[x][j], K[x-w[j]][j-1] + v[j])
        return K[W][len(w)-1]
    ```
