# Princeton COS326: Functional Programming
### A TL;DR by Perry Cate '19

Disclaimer: I created this as my own study tool. I have made aggressive,
opinionated choices about what to include.

(That said, suggestions, issues, and pull requests are always welcome!)


## Things not Included
 * History of Functional Languages
 * Names of People
 * Some syntax stuff (|>, module sig/struct syntax, etc.)
 * Inference Rule Notation (will add later)
 * Type Inference (might be added later)
 * F# stuff
 * Functional Networking
 * Anything I think isn't directly relevant to the final exam.


## General Terms
 * **Expressions**: Computations. _`2+3`_
 * **Values**: Results of computations. Not every computation has one. (Errors,
etc.) _`5`_
 * **Tuple**: Fixed, finite, ordered collection of values. _`(2, 3)`_
 * **Record**: Basically a named tuple. _`type point2d = { x: float; y: float }`_
 * **Unit**: Tuple with zero fields. _`()`_
 * **List**: Like a tuple but everything is the same type _`[2;3]`_
 * **Inductive Data Type**: Has collection of base cases, bunch of inductive
 cases that build new values from pre-existing (smaller) values
 * **Combinator Libraries**: Simple functions, composable for complex tasks.
 _map, fold, reduce, etc._
 * **Binding Occurrence**: Place where a variable is defined
 * **Free Variable**: Variable with a binding occurrence not in scope
    * Theorem: well-typed programs have no free variables.
 * **Valuable Expression**: Always terminates, no side effects, produces a
 value
 * **Total Function**: Terminates on all arguments of input, produces a value
    * Opposite of **Partial Function**


## Ocaml
 * Ocaml is functional
    * Functions are values and can be bound to variables as such
    * Ocaml produces new, unchanging\* (immutable) data, Java modifies data
 * Ocaml is typed
    * Type of expression predicts kind of value it will produce
    * Type system is sound => language is safe.

### Type Checking is nice because it
 * Make us think about how to construct programs
 * Finds stupid errors
 * Makes maintainability easier
 * We can enforce invariants (unchanging facts) about our data structures.
 * Makes it easy to prove things

### Type Checking rules(!)
 * `e1: T1` and `e2: T1` => `e1 + e2 : T1`
 * `f: T1 -> T2` and `e: T1` => `f e : T2`
 * `e1: T1` and `e2: T2` => `(e1, e2): T1 * T2`
 * `T1 -> T2 -> T3` == `T1 -> (T2 -> T3)` (currying)
    * `f: A -> B -> C`
       * Returns `C` if given two arguments
       * Returns `g: B -> C` if given one argument (partially applied)
 * Values of type `t option` are either `None` or `Some t`
    * Match statements must handle both
 * `::` (cons) takes `T :: T list`
 * `(fun x -> e2) e1` == `let x = e1 in e2`

### Data types
 * Type abbreviation: `type point = float * float`
    * Just an alias - can substitute definition for type wherever.
 * Data type: `type mytype = Something | Others`
    * Usage: `let b : mytype = Something`
    * Value with type mytype is either `Something` or `Others`
    * Things after `=` between `|` are constructors
    * As many constructors as you want.
    * Can use in match statements
    * Can hold values: `type shape = Circle of point * float | Square of float *
      float`
    * Examples:
       * `type tree = Leaf | Node of key * value * tree * tree`
       * `type nat = Zero | Succ of nat`
 * Parameterized type: `type ('key, 'val) tree = Leaf | Node of 'key * 'val *
   ('key, 'val) tree * ('key, 'val) tree`
    * Basically a function that takes a type as arg, produces a type
    * Usage example:
      1. `type 'a stree = (string, 'a) tree`
      2. `type sitree = int stree`

### Important functions
 * **`map`** `(f:'a -> 'b) (xs:'a list) : 'b list`
   * Applies `f` to every item in `xs`
 * **`fold\_right`** `(f:'a -> 'b -> 'b) (xs:'a list) (u:'b) : 'b`
   * Applies `f` to each item in `xs`, using `u` as an accumulator
   * Can implement basically everything with this, including map and filter
   * Right associative (works right to left): `f(a, f(b, f(c, z)))`
 * **`fold_left`** `(f:'a -> 'b -> 'a) (u:'a) (xs:'b list) : 'a`
   * Same as `fold_right` but is left associative: `f(f(f(z, a), b), c)`
   * Unlike `fold_right`, is tail recursive (thus more efficient)
 * **`reduce`** `(f:'a -> 'b -> 'b) (u:'b) (xs:'a list) : 'b`
   * Literally just `fold_right` but with arguments in a different order
 * **`filter`** `(f: 'a -> bool) -> (l: 'a list) -> 'a list`
   * Returns list formed of all elements `x` in `l` such that `f x = true`

### Ocaml Objects
 * They exist
 * Don't bother, nobody uses them.

## Proof Principles

### Equality Properties
 * **Reflexivity**: `e = e`
 * **Symmetry**: if `e1 = e2` then `e2 = e1`
 * **Tranistivity**: if `e1 = e2` and `e2 = e3` then `e1 = e3`
 * **Evaluation**: if `e1` evaluates to `e2` then `e1 = e2`

### More Things that are equal
 * `let f x = body` == `let f = (fun x -> body)`
    * AKA function de-sugaring
 * `(fun x -> ... x ...) arg` == `... arg ...`
    * If arg is a value or always produces one
    * AKA substitution
 * `let f = def` == `let f x = (def) x`
    * AKA eta-expansion
 * 2 expressions are equal iff:
    * Both evaluate to same value, or
    * both raise the same exception, or
    * both infinite loop
 * if `e1 = e2` then `foo e1 = foo e2`

### Proof Tips
 * Substitute things that are equal to each other
 * Types are clues for how to break down proofs/programs
 * Induction works great on recursive functions, cases mirror match statements
 * If the IH doesn't apply, may need to prove a more general theorem first

### General Proof by Induction Format:
 0. State Theorem
 1. State Proof Methodology
 2. Cover Base Case
 3. State Inductive Hypothesis (and which var it's a function of)
    * Remember to make sure IH is working on smaller values than your case!
 3. Prove case `k+1`, assuming proof is true for some `k`
    * Must cover all cases one way or another.

### Important notes
 * Ocaml evaluates function arguments to a value _before_ calling the function
 * In real ocaml, `==` is different from `=`
    * `=` : Equal, has the same value
    * `==` : Identical, is actually the same thing (don't use this one)
    * Logic stuff above ignores the difference, think of both as "equals"


## Space
 * Important things are **Live Space** (Space currently used) and **Rate of
   Allocation**
 * Data representations:
    * Tuples - Consecutive bytes of memory (like Java arrays)
    * Lists - Linked List
    * Parameterized types - Name of type, pointer to address of tuple of data
 * Space allocated when constructors are used or closures are created
 * Often must also estimate heap space used from closures
    * Cost of environment + (varname+value pairs)


## CPS
 * **Tail-Recursive Function**: Calling itself is the last computation it does
    * Compiler can optimize this so that new call replaces existing one on the
      stack frame
    * Without tail call optimization, n recursive calls add n calls to stack -
      bad memory growth!
    * With optimization, only uses 1 stack frame -> constant memory!
 * **Continuation-Passing Style**: Can change any recursive function to be tail
   recursive
    * Every function takes a _continuation_ (function) as arg telling what to
      do next, base case executes the function along with whatever's given
       * Defining a function sum: rather than recurse on `hd + sum tail`,
         recurse on `sum tail (fun s -> k (hd + s))` (`k` is the new argument)
       * We're just passing the rest of the non-tail-recursive function as an
         argument
    * Rather than storing info on the stack, it becomes a bunch of functions
      pointing to each other on the heap 
       * Heap has way more space than the stack, so this is ok.
    * Once you go low level enough, everything is CPS, regardless of it's on
      the stack or the heap
 * **A-Normal Form**: Itermediate computations are given names, serves as a
   nice middle step between direct (non-tail-recursive) style and CPS-converted
    1. **Direct Style**: `let g input = f3 (f2 (f1 input))`
    2. **A-Normal Form**: `let g input = let x1 = f1 input in let x2 = f2 x1 in
       f3 x2`
    3. **CPS-Converted**: `let g input k = f1 input (fun x1 -> f2 x1 (fnu x2 ->
       f3 x2 k))`


## Modules
 * They're nice because they:
    * Abstract away how functions are implemented
    * Make it easy to swap implementations later
 * **Signature**: Interface specifying abstract type and set of operations
   on it
 * **Structure**: Implementation defining types and values satisfying an
   interface
    * Can match any signature as long as it has _at least_ the definitions in
      the signature's interface
    * Functions not in the signature's interface are inaccessible to clients 
 * **Functor**: Parameterized module, basically a function that takes modules
   and produces new ones
    * Makes refactoring and reusing modules easy
 * **Anonymous Structures**
    * They're a thing
    * Can be passed into functors to give a module specific values to use

## Invariants
 * **Representation Invariant**: Property that's always true for an abstract
   type
 * If we prove the invariant holds for all module outputs, we can assume it for
   any module inputs
    * Invariant cares about the representation details, which are hidden from
      the client and thus can't be messed with outside the module.
    * Is independent of the client = very modular!
    * Helpful for debugging
 * Satisfies `inv()` means invariant is provably always true for some function
 * Proving stuff:
    * If a module defines abstract `type t`, must prove function satisfies
      `inv()` if it returns anything of `type t`
    * Assume `inv(x)` for any input `x`
    * We don't need to prove anything about unrelated types like int
    * Pair `v` of type `s1 * s2` is true if `fst v` is valid for `s1` and
      `snd v` is valid for `s2`
    * `v` is valid for type `s1 option` if `v` is `None`, or if `v` is `Some u`
      and `u` is valid `s1`

### Proving Implementations are Related
 * We want to show `M1` and `M2` both implement signature `S`
 * Define `is_related` function
    * Like a representation invariant but between 2 modules instead of 1
    * Returns true if you can convert a value with one module's underlying
      implementation to use the other module's implementation (while still
      being the same value)
    * `is_related(v1, v2)` takes `v1` from `M1` and `v2` from `M2`
    * If modules are equivalent, `is_related(v1, v2)` implies
      `is_related(M1.f v1, M2.f v2)`


## Refs
 * Reasoning about immutable state is easy
    * Any invariant you prove stays true
    * Side effects are bad
 * Mutable state is a necessary evil
    * The outside world is mutable (you gotta print stuff out)
    * Sometimes it's just faster/more efficient
 * `t ref` - basically a pointer, like in C
    * Contents of ref can be read/written, allows mutable state
       * Create new `ref` with contents `42`: `ref 42`
       * Read contents: `!r`
       * Write contents: `r := 5`
       * If I create a new variable `s = r`, `!s == !r`
    * Recommended: ref pointing to immutable structure (iff mutable state is
      needed)
    * Recommended: encapsulate behind an interface (keep clients from messing
      with it)
 * Possible use: memoizing functions
    * Basically caching result if something is called repeatedly (see laziness)

### Other mutable things
 * Mutable records: just write mutable before each field name
 * Arrays
    * Read the ith element: `A.(i)`
    * Write the ith element: `A.(i) <- 42`
    * Make an array of length `42` and all elements initialized to `'x'`:
      `Array.make 42 'x'`


## Laziness
 * Can be used to implement things like infinite streams
    * `type 'a str = Cons of 'a * ('a stream) and 'a stream = unit -> 'a str`
       * Each element in infinite stream is an element cons'd onto a function
         that produces the next item
       * We want to avoid executing that function until we're asked to do so
    * Won't be efficient because every time we access the nth element we
      recalculate 0..(n-1)
       * Solution: memoize (cache results)
       * (Assignment 6)
       * Ocaml has built in lazy keyword that forces a function to delay
         executing until the last moment


## Parallelism
 * **Parallelism**: Doing things at the same time instead of in sequence
    * More work done at once
      * Note: Sometimes threading overhead isn't worth it
    * Can utilize many cores/computers at once
    * Important because it's easier to just add multiple cores rather than
     making cores faster now.
 * **Concurrency**: Multi-party access to shared resources
    * Decreases response time
    * Switch to different thread while current one is waiting on resources
     (disk, network, etc.)
    * Can reduce throughput due to cost of thread switching
 * **Speedup**: Sequential execution time divided by parallel execution time
    * Perfect speedup (aka _linear speedp_) if speedup=# of cores
 * Working with multiple threads is hard
    * Can be nondeterministic
    * Can implicitly influence each other
    * Race conditions
    * Deadlocks
    * Busy waiting
    * Have to deal with synchronization to mitigate this stuff
    * Functional programming makes it easier
       * _If code is purely functional, the order that it's run in doesn't
         matter_ (imperative programming with side effects breaks everything)
      * **Futures**
        * Eliminate a lot of multithreading problems
        * Can fork a function off to run in another thread, and wait on the result
          when needed

### Scheduling
 * **Work**: Cost of executing a program with 1 processor
 * **Span**: Cost of executing a program with an infinite # of processors
 * Parallelism = work / span
    * If work = span, it's sequential
 * How you schedule jobs impacts performance
    * Some jobs may depend on each other
 * Greedy scheduling - give a task to a processor as soon as processor is free
    * It's decent, used in practice.

### Parallel Collections
 * Lists are bad
    * Splitting/merging = linear time, thus hard to divide and conquer, thus
      hard to parallelize things that use lists
 * Balanced Trees are ok
    * Splitting is constant time
    * Merging is poly-log
 * Parallel Sequences
    * Operations
       * Creation (aka tabulate): work=n but span=1
       * Indexing element, splitting sequence takes constant span
       * Map
       * Scan and fold are log n span!
 * Tips for operations over parallel collections:
    * Associativity allows parallelism
       * Reduce allows log n span for example
    * Some operations may require multiple passes
       * Example: parallel prefix-sum (ppt 20, slide 91)
    * Sequential cutoff good for performance, removes overhead for small inputs
      where threading is unecessary
