# Implementation of Lexical Analysis

Stages of code processing in lexical analysis:

1. Lexical Specification
2. Regular Expressions
3. NFA
4. DFA
5. Table-driven implementation of DFA

## Converting a lexical specification to a regular expression

### Notation

* Union: A + B = `A | B`
* Option: A + ε = `A?`
* Range: 'a' + 'b' + ... + 'z' = `[a-z]`
* Excluded range: complement of `[a-z]` = `^[a-z]`

### Steps

1. Write a regex for each token
    - Number = `digit +`
    - Keyword = `'if' + 'else' + ...`
    - Identifier = `letter(letter+digit)*`
    - OpenParenthesis = `'('`
    - ...
2. Construct R, matching all lexemes for all tokens
    - R = Keyword + Number + Identifier + Number + ...
    - This step is usually done automatically by tools like Flex
3. Check contiguous subsets of tokens are in L(R)
    - Let input be x<sub>1</sub>...x<sub>n</sub>
    - For 1 <= i <= n check that x<sub>1</sub>...x<sub>i</sub> is in L(R)
4. Removing tokens
    - If success then we know that x<sub>1</sub>...x<sub>i</sub> is in L(R<sub>j</sub>) for some j
    - Remove x<sub>1</sub>...x<sub>i</sub> from the input and goto step 3

### Ambiguities

1. How much input is being used?
    - What if x<sub>1</sub>...x<sub>i</sub> is in L(R) but so is x<sub>1</sub>...x<sub>k</sub>?
    - Rule: pick longest possible string in L(R); pick k if k > i
2. Which token is used?
    - What if x<sub>1</sub>...x<sub>i</sub> is in L(R<sub>j</sub>) and also in L(R<sub>k</sub>)?
    - Rule: use rule listed first; pick j if j < k
        - This treats `if` as a keyword, not as an identifier

### Error handling

* What if no rule matches a prefix of the input?
* Solution: write a rule matching all "bad" strings
    - Put it last (lowest priority)
    - Rule accepts any single character of the alphabet

## Finite automata

[See my notes from CS154.](../CS154/2021-09-28-finite-automata.pdf)

### DFA implementation as tables

* A DFA can be implemented by a 2D table T
    - One dimension is "states"
    - Another dimension is "input symbol"
    - For every transition S<sub>i</sub> -><sup>a</sup> S<sub>k</sub> define `T[i, a] = k`
* DFA execution
    - If in state S<sub>i</sub> and input a, read `T[i, a] = k` and skip to state S<sub>k</sub>
    - Very efficient