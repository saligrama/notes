# Dynamic Programming

## Definitions

* Dynamic programming: algorithmic design paradigm
* Usually for solving optimization problems (i.e., shortest path)
    - Also useful for combinatorial problems (i.e., how many ways are there to achieve *some task*?)

## Elements of dynamic programming

1. Optimal sub-structure (necessary for DP to be correct)
    - Big problems break up into subproblems
        - e.g. Fibonacci (below): F(i) for *i <= n*
        - e.g. Bellman-Ford (below): shortest paths with at most *i* edges for *i <= n*
    - The solution to a problem can be expressed in terms of solutions to smaller subproblems
        - e.g. Fibonacci: *F(i+1) = F(i) + F(i-1)*
        - e.g. Bellman-Ford: *d[v] = min(d[v], min<sub>u</sub> d[u] + w(u, v)*)
2. Overlapping sub-problems (necessary for DP to achieve speedup)
    - e.g. Fibonacci: Both *F[i+2]* and *F[i+1]* directly use *F[i]*; many *F[i+x]* indirectly use *F[i]*
    - e.g. Bellman-Ford: many different entries of *d* at step *i+1* will use *d[v]* at step *i*; lots of entries of *d* at step *i+x* will indirectly do so

## Example: Fibonacci numbers

### Recursive algorithm (i.e., Week 1):

```python
def fibonacci(n):
    if n == 0:
        return 0
    if n == 1:
        return 1
    return fibonacci(n-1) + fibonacci(n-2)
```

* Runtime: exponential.
* Note: this does a lot of repeated computation!
    - For `fibonacci(8)` we calculate `fibonacci(2)` 11 times (and `fibonacci(1)` and `fibonacci(0)` even more!)

### Instead: memoize the previous fibonacci calls

Memoization: keeping track of previous function calls, usually in an array or other data structure

```python
def fasterFibonacci(n):
    F = [None] * (n+1)
    F[0] = 0
    F[1] = 1
    for i in range(2, n+1):
        F[i] = F[i-1] + F[i-2]
    return F[n]
```

### Bottom-up DP

* Like what we see for Fibonacci and Bellman-Ford
    - Solve smaller problems first (F[0] or *d* at step [0], then F[1] or *d* at step 1, then...) then bigger problems, then the full problem (*F(n)* or *d* at step *n-1*)
* Preferable if we know recursion might get extremely deep (i.e., lots of variables to recurse over in algorithm/subproblem state) - that way, we don't overwhelm the function call stack

### Top-down DP

* Adding memoization to previous divide-and-conquer methods
* To solve big problems:
    - Recurse to solve smaller probelms
        - Then recurse to solve even smaller problems
            - etc...
* Keep track of what small problems you've already solved to prevent re-solving the same problem twice
* Preferable if we know that the recursion doesn't need to compute certain subproblems

#### Top-down example

```python
F = [None] * (n+1)
F[0] = 0
F[1] = 1

def Fibonacci(n):
    if F[n] != None:
        return F[n]
    return Fibonacci(n-1) + Fibonacci(n-2)
```

## Example: Bellman-Ford algorithm

### Motivation: Dijkstra drawbacks

* Needs non-negative edge weights
* If the weights change, need to re-run the whole algorithm

### Difference between Bellman-Ford and Dijkstra:

* Bellman-Ford is slower than Dijkstra
* Bellman-Ford can handle negative edge weights
    - Can detect negative cycles (i.e., path from a->b->c->a has less cost than zero)
    - Can be useful if we want to say some edges are actively helpful to take, rather than costly
    - Can be useful as a building block in other algorithms
* Allows some flexibility if weights change

Bellman-Ford intuition: Instead of picking the node *u* with the smallest *d[u]*, update all the node weights at once

### Pseudocode

```python
def bellmanFord(V, E, s):
    d = [INT_MAX] * len(V)
    d[s] = 0
    for i in range(len(V) - 1):
        for (u, v, w) in E: # u src, v dst, w weight
            d[v] = min(d[v], d[u] + w)

    return d
```

### Correctness

**Inductive hypothesis**: At each step *i*, *d[v]* is equal to the cost of the shortest path between *s* and *v* of length at most *i* edges

**Base case**: 0; vacuously true (0 edges -> infinite cost)

**Inductive step**: Suppose IH applies for *0 <= n <= k*; then at step *k* for all *v*, *d[v]* is the shortest path between *s* and *v* with at most *k* edges. Suppose we do a *k+1* iteration, then we can choose to take any *u* as an additional intermediate between *s* and *v* or not; thus the path will be the same or shorter than before and have at most *k+1* edges. 

**Conclusion**: At step *|V|-1*, for each *v*, *d[v]* is equal to the cost of the shortest path between *s* and *v* of length at most *|V|-1* edges (which is the absolute shortest path).

### Runtime

One loop for edges (*|E|*) inside a loop for vertices (*|V|*) -> O(|V||E|)

## Floyd-Warshall Algorithm

Algorithm for All-Pairs Shortest Paths

* Need to know shortest path from *u* to *v* for all pairs *(u, v)* of vertices in the graph (not just from a special single source *s*)
* Brute force: run Dijkstra from all nodes *s*: this takes time O(|V||E| log|V|)
    - If negative edge weights: need Bellman-Ford, this takes time O(|V|<sup>2</sup>|E|)
* Floyd-Warshall intuition: for each node *k*, check if the distance between each pair of nodes *i* and *j* can be reduced by using *k* as an intermediate
    - Just like Bellman-Ford: idea is to calculate shortest path between *i* and *j* for each *(i, j)* with at most *h* edges at each step *h*
    - Runtime: O(|V|<sup>3</sup>)
        - Better than running Bellman-Ford *n* times
        - However: if no negative edge weights and the graph is not extremely dense, better to run Dijkstra with a min-heap
    - Space complexity: O(|V|<sup>2</sup>)

### Psuedocode

```python
dist = [[INF] * len(V)] * len(V)
for i in range(len(V)):
    d[i][i] = 0

for k in range(len(V)):
    for i in range(len(V)):
        for j in range(len(V)):
            d[i][j] = min(d[i][j], d[i][k] + d[k][j])
```