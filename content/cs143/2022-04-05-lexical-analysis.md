# Lexical analysis

Goal: partition input strings into substrings; i.e.

```c
if (i == j)
    z = 0;
else
    z = 1;
```

converts to

```c
"if(i==j)\n\tz=0;\n\telse\n\tz=1"
```

which we want to select the relevant *tokens* from.

## Tokens

Tokens are a syntactic category

* In English: noun, verb, adjective, etc.
* In programming languages: identifier, integer, keyword, whitespace, etc.

A token class corresponds to a set of strings; e.g.

* Identifier: strings of letters or digits, starting with a letter
* Integer: a non-empty string of digits
* Keyword: `if`, `else`, `begin`, etc.
* Whitespace: nonempty sequence of blanks, newlines, and tabs

Tokens are used for classifying program substrings depending on their role

Lexical analysis produces a set of tokens, which is then used as input to the parser

* Parser relies on token distinctions: e.g. an identifier is treated differently than a keyword

## Designing a lexical analyzer (lexer)

### Step 1: define a finite set of tokens

* Tokens define all items of interest
    - Identifiers, integers, keywords, etc.
* Choice of tokens depends on language and design of parser

e.g.

```c
"if(i==j)\n\tz=0;\n\telse\n\tz=1"
```
* Useful tokens include
    - Integer, keyword, relation, identifier, whitespace, `(`, `)`, `=`, `;`

### Step 2: describe which strings belong to each token

* Identifier: strings of letters or digits, starting with a letter
* Integer: a non-empty string of digits
* Keyword: `if`, `else`, `begin`, etc.
* Whitespace: nonempty sequence of blanks, newlines, and tabs

### Implementation

1. Classify each substring as a token
2. Return the value or lexeme of a token
    - Lexeme is the actual substring
    - From the set of substrings that make up the token

Note: lexer returns token-lexeme pairs, plus line numbers, file names, etc. to improve later error messages

e.g.

```c
"if(i==j)\n\tz=0;\n\telse\n\tz=1"
```

yields token-lexeme pairs

```json
{
    'whitespace': [
        '\t',
        ' ',
        '\n',
    ],
    'keyword': [
        'if',
        'else'
    ],
    'identifier': [
        'i',
        'j',
        'z'
    ],
    'integer': [
        '0',
        '1'
    ],
    '(': '(',
    ')': ')',
    ...
}
```

Lexer usually discards "uninteresting" tokens that don't contribute to parsing: e.g. whitespace, comments

### What not to do: "crimes" of lexical analysis

* e.g. FORTRAN: whitespace is insignificant (motivated by inaccuracy of punch card operators)
    - `VAR1` is the same as `VA R1`
* Lookahead: may be required to decide where one token ends and the next token begins
    - Even our simple example has lookahead issues: `i` vs `if`, `=` vs `==`
* PL/I: keywords are not reserved
    - `IF ELSE THEN THEN = ELSE; ELSE ELSE = THEN` is valid!
    - `DECLARE(ARG1, ..., ARGN)`: cannot tell whether `DECLARE` is a keyword or array reference until after the `)` - requires arbitrary lookahead
* C++: template syntax (until C++11)
    - Template: `Foo<Bar>`
    - Stream: `cin >> var`
    - Nested templates: `Foo<Bar<Baz>>`: unclear what the `>>` is, so before `C++11` we needed to write this as `Foo<Bar<Baz> >`

## Regular languages

* Many formalisms for specifiying tokens; regular languages are the most popular
    - Simple and useful theory
    - Easy to understand
    - Efficient implementations
* Def. Let alphabet Σ be set of characters; a language L over Σ is a set of strings of characters drawn from Σ
    - Alphabet = English characters; Language = English sentences
    - Alphabet = ASCII; language = C programs
    - Observe that not every string of English characters is an English sentence
* Notation:
    - Languages are sets of strings
    - Need some notation for specifying which sets we want
    - The standard notation for regular languages is regular expressions

### Regular expressions

Atomic regular expressions: basic rules

* Single character
    - `'c' = {'c'}`
* Epsilon
    - `ε = {''}`

Compound regular expressions

* Union
    - `A + B = {s | s ∈ A or s ∈ B}`
* Concatenation
    - `AB = {ab | a ∈ A and b ∈ B}`
* Iteration (Kleene star)
    - `A* = U_{i >= 0} A^i where A^i = A repeated i times` 

Def. regular expressions: the regular expressions over Σ are the smallest set of expressions including the above operators

### Describing tokens as regular expressions

* Keywords: `else`, `if`, `begin`, etc.
    - `keyword = "else" + "if" + "begin" + ...`
* Integers: non-empty sets of digits
    - `digit = '0' + '1' + '2' + '3' + '4' + '5' + '6' + '7' + '8' + '9'`
    - `integer = digit digit* = digit+`
* Identifiers: strings of letters or digits, starting with a letter
    - `letter = 'A' + ... + 'Z' + 'a' + ... + 'z'`
    - `identifier = letter (letter + digit)*`
* Whitespace: non-empty sequence of blanks, newlines, and tabs
    - `('' + '\n' + '\t')+`
