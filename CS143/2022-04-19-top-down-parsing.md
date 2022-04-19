# Top-down parsing

## Predictive parsers

* Like recursive-descent, but parser can "predict" which production to use by looking at next few tokens (no backtracking)
* Predictive parsers accept LL(k) grammars
    - First L: "left-to-right" input scan
    - Second L: "leftmost derivation"
    - k: "predict based on k tokens of lookahead"
    - In practice: LL(1)

### LL(1) vs Recursive Descent

* Recursive Descent: at each step, many choices of production to use
    - Backtracking used to undo bad choices
* LL(1): at each step, only one choice of production
    - When non-terminal `A` is leftmost in a derivation and next input `t`, either:
        - Unique production `A -> α` to use
        - No production to use (error state)
    - Like a recursive descent variant but without backtracking

### Predictive parsing and left factoring

* Example: consider grammar
    ```
    E -> T + E | T
    T -> int | int * T | ( E )
    ```
    - This is hard to predict because
        - For `T` two productions start with `int`
        - For `E` unclear how to predict
    - Requires left-factoring the grammar
        - Factor out common prefixes of productions:
            ```
            E -> T X
            X -> + E | ε
            T -> int Y | ( E )
            Y -> * T | ε
            ```