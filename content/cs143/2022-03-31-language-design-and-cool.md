# Language design

* Languages are adopted to fill a void
    - Enable a previously difficult/impossible application
    - Can be orthogonal to language quality
* Programmer training is the dominant cost
    - Languages with many users are rarely replaced
    - Popular languages become ossified
    - But easy to start in a new niche

## Why so many languages?

* Application domains have distinctive and conflicting needs
    - Rust: systems programming w/focus on security
    - R: statistics w/focus on streams
    - Python: scripting
    - Julia: linear algebra
    - Javascript: able to run in browser
* No universally accepted metrics for design

## Abstraction

* Detatched from concrete details
* Information hiding: expose only the essential
* Modes of abstraction
    - Via languages/compilers: higher level code, few machine dependencies
    - Via functions/subroutines: abstract interface to behavior
    - Via modules: export interfaces, hide implementation
    - Via classes or abstract data types: bundle data with operations

## Types

* Originally, few types
    - FORTRAN: scalars, arrays
    - LISP: no static type distinctions
* Realization: types help
    - Allows expressing abstraction
    - Lets compiler report many frequent errors
    - Allows guaranteeing certain types of "safety"

## Reuse

* Exploit common patterns in software systems
    - Goal: mass-produced software components
* Two popular approaches
    - Type parametrization: `std::vector<int>`, `std::vector<double>`
    - Classes and inheritance: C++ derived classes
    - C++ and Java have both
* Inheritance allows
    - Specialization of existing abstractions
    - Extension, modification, hidden behavior

## Trends

* Language design
    - Many new special-purpose languages
    - Popular languages stick around (perhaps forever)
        - Fortran and Cobol
* Compilers
    - Ever more needed and more complex
    - Driven by increasing gap between new languages and architectures
    - Venerable and healthy area

# COOL overview

COOL: Classroom Object Oriented Language

* Designed to be implentable in a short time
* Compiler will compile to MIPS assembly
* Features:
    - Abstraction
    - Static typing
    - Reuse (inheritance)
    - Memory management
    - etc.

## Example

```
class Point {
    x: Int <- 0;
    y: Int <- 0;
}
```

* Programs are sets of class definition
    - Special class `Main` has special method `main`
    - All COOL code lives inside classes
* Class: collection of attributes and methods
* Instances of a class are objects

## Objects

```
class Point {
    x: Int <- 0;
    y: Int; (* use default value *)
}
```

* The expression `new Point` creates a new object of class `Point`
* An object can be thought of as a record with a slot for each attribute

## Methods

* A class can also define methods for manipulating attributes
* Methods can refer to the current object using `self`

```
class Point {
    x: Int <- 0;
    y: Int <- 0;

    movePoint(newx: Int, newy: Int): Point {
        {
            x <- newx;
            y <- newy;
            self;
        } -- close block expression
    }; -- close method
}; -- close class
```

* Each object knows how to access the code of a method
    - As if the object contains a slot pointing to the code
    - Actual implementation: first slot of each object contains a pointer to a virtual table (vtable) which contains pointers to the methods

## Information hiding

* Methods are global
* Attributes are local to a class
    - They can only be accessed by the class methods
    - Need getters/setters to access class attributes

```
class Point {
    ...
    x () : Int { x };
    setx (newx : Int) : Int { x <- newx };
};
```

## Inheritance

* Subclassing yields class hierarchy
* Implementation:
    - Add new subclass fields as slots at the *end* of the objects
    - Method table is copied and augmented for subclasses (no linking to method table of superclass)

```
class ColorPoint inherits Point {
    color : Int <- 0;
    movePoint(newx : Int, newy : Int) : Point {
        color <- 0;
        x <- newx;
        y <- newy;
        self;
    };
};
```

### Method invocation and inheritance

* Methods are invoked by dispatch
* Understanding dispatch in the presence of inheritance is a subtle aspect of OO languages

```
p : Point; -- p has static type Point
p <- new ColorPoint; -- p has dynamic type ColorPoint
p.movePoint(1, 2); -- p.movePoint must invoke the ColorPoint version
```

e.g. invoke one-argument method `m(x)`

```
e.m(e')
```

1. Evaluate expression `e`
2. Find class of `e`
    - Yields method table
3. Find code of `m`
    - Lookup in method table
4. Evaluate argument `e'`
5. Bind `self` and `x` for `m`
6. Run method

## Types

* Every class is a type
* Base classes:
    - `Int`: integers, 4-byte type
    - `Bool`: booleans - `true`, `false`
    - `String`
    - `Object`: root of class hierarchy
* All variables must be declared
    - Compiler infers type for inheritance

### Type checking

```
x : A;
x <- new B;
```

* Well-typed if `A` is an ancestor of `B` in the class hierarchy
* Anywhere an `A` is expected a `B` can be used
* Type safety: a well-typed program cannot result in runtime type errors

## Other expressions

* Every expression has a type and a value
    - Loops: `while E loop E pool`
    - Conditionals: `if E then E else E fi`
    - Case statement: `case E of x : Type => E; ... esac`
    - Arithmetic, logical operations
    - Assignments: `x <- E`
    - Primitive I/O: `out_string(s)`, `in_string(s)`
* Missing features:
    - Arrays
    - Floating point operations
    - Exceptions
    - etc.

## Memory management

* Memory is allocated every time `new` is invoked
* Memory is deallocated automatically when an object is no longe reachable
* Done by the garbage collector (GC)