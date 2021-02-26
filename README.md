# Homework 4: Error handling and the heap

In this homework, you'll implement lists using pairs and arrays. You'll get
practice handling runtime errors and dealing with data on the heap.

## Lists

As we saw in class, lists can be implemented using pairs. In fact, this is how
lists are generally implemented in Scheme and other Lisp-like languages. A list
is either:

- The empty list, for which we used `false` in class
- A pair where the second element is a list

Instead of using false to signal the empty list, Scheme includes a special
value `()` (pronounced "nil").

Add support for `()` to both the interpreter and the compiler. In the compiler,
use `0b11111111` as the runtime representation of `()`.

Now we can build lists using `pair` and `()`. Here are a few such lists:

- `()`
- `(pair 1 ())`
- `(pair 1 (pair 2 ()))`
- `(pair (pair 1 2) ())`

Add a primitive `(list? e)`. It should evaluate to `true` when its argument is a
list and `false` otherwise.

Hint: implement `list?` in the interpreter with a recursive helper function.

Hint: implement `list?` in the compiler with a loop: you'll need to define a
label and repeatedly jump back to that label.

## Vectors

We can also implement lists using arrays. In Scheme, array-backed lists are
called vectors. You'll implement the following vector operations:

- `(vector n e)` creates a vector of length `n` where all of the elements are
  `e`. `n` must evaluate to a positive integer.
- `(vector? e)` returns `true` if `e` is a vector and `false` otherwise.
- `(vector-length v)` returns the length of the vector `v`.
- `(vector-get v n)` returns the element of the vector `v` at (0-based) index
  `n`. `n` must evaluate to a non-negative integer less than the length of `v`.
- `(vector-set v n e)` sets the element at index `n` of the vector `v` to `e`
  and evaluates to the vector. `n` must evaluate to a non-negative integer less
  than the length of `v`.

Vectors should be represented as space-delimited lists of their values enclosed
in square brackets. For instance, the following represent some simple vectors of
numbers:

- `[]`
- `[1]`
- `[1 2 3]`

In the interpreter, you can implement vectors using OCaml's built-in `array`
type and the functions in the `Array` module.

In the compiler, you should implement vectors on the heap. A vector of length
`n` should occupy `n+1` 8-byte cells; the first cell should be used to store its
length. Use the three-bit tag `0b101` for vector values.

Hint: you may need to make use of an additional register in order to implement
some vector operations. If you do, you can use `R9` in addition to the usual
`Rax` and `R8`.

## Error handling

For this assignment, unlike previous assignments, you are expected to implement
error-handling in the compiler. Invalid expressions, such as `vector-length` on
a non-vector or `vector-get` with an index greater than the vector's length,
should always cause runtime errors. Take a look at `ensure_num` and `ensure_pair`
for some examples of the error handling we've implemented so far.

## The runtime

You'll need to change the runtime to handle displaying `nil` and vector values.
In order to display vectors, it will likely be helpful to cast a vector's pointer
to the heap into a C pointer to a `uint64_t` and then use
[pointer arithmetic](https://www.tutorialspoint.com/cprogramming/c_pointer_arithmetic.htm)
to access each of the values in the vector.

## Testing

In addition to the usual testing scheme based on `*.lisp`, `*.out` and `*.err`
files, you can now include a CSV file of anonymous tests in `examples/examples.csv`.
The format of each line is either `<PROGRAM>,<EXPECTED>` or `<PROGRAM>`, depending
on whether or not you want to specify an expected output. The CSV does not support
tests that are expected to error--for those, and for more complicated tests for
programs that span multiple lines, use file-based tests. Leading and trailing
whitespace is stripped from each of the CSV's cells.

## Grammar
The grammar your new interpreter and compiler should support is as follows:
```diff
<expr> ::= <num>
         | <id>
         | true
         | false
         | ()
         | (<un_prim> <expr>)
         | (<bin_prim> <expr> <expr>)
         | (<tri_prim> <expr> <expr> <expr>)
         | (if <expr> <expr> <expr>)
         | (let ((<id> <expr>)) <expr>)
         | (do <expr> <expr> ...)
             

<un_prim> ::= add1
            | sub1
            | zero?
            | num?
            | not
            | pair?
            | left
            | right
+           | list?
+           | vector?
+           | vector-length

<bin_prim> ::= +
             | -
             | =
             | <
             | pair
+            | vector
+            | vector-get

<tri_prim> ::=
+            vector-set
```
