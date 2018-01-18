# Princeton COS326: Functional Programming
### A TL;DR by Perry Cate '19

Disclaimer: This is being created as my own study tool. I will make aggressive,
opinionated choices about what to include. (But suggestions, issues, and pull
requests are always welcome!)


## Things not Included
 * History of Functional Languages
 * Names of People
 * Anything I think won't be on the final exam.
 * Some syntax stuff (|>, etc.)
 * Inference Rule Notation (will figure out how to format later)


## Terms
**Expressions**: Computations. _2+3_
**Values**: Results of computations. Not every computation has one. (Errors,
etc.) _5_
**Tuple**: Fixed, finite, ordered collection of values.
**Unit**: Tuple with zero fields. _()_
**List**: Like a tuple but everything is the same type
**Inductive Data Type**: Has collection of base cases, bunch of inductive cases
that build new values from pre-existing (smaller) values
**Combinator Libraries**: Simple functions, composable for complex tasks. _map,
fold, reduce, etc._
**Binding Occurrence**: Place where a variable is defined
**Free Variable**: Variable with a binding occurrence not in scope
  * Theorem: well-typed programs have no free variables.
**Valuable Expression**: Always terminates, no side effects, produces a value
**Total Function**: Terminates on all arguments of input, produces a value
  * Opposite of **Partial Function**


## Ocaml
 * Ocaml is functional
    * Functions are values and can be bound to variables as such
    * Ocaml produces new, unchanging\* data, Java modifies data
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
 * e1: T1 and e2: T1 => e1 + e2 : T1
 * f: T1 -> T2 and e: T1 => f e : T2
 * e1: T1 and e2: T2 => (e1, e2): T1 * T2
 * T1 -> T2 -> T3 == T1 -> (T2 -> T3) (currying)
    * f: A -> B -> C
       * Returns C if given two arguments
       * Returns g: B -> C if given one argument (partially applied)
 * t option -> is either None or Some t
    * match statements must handle both
 * :: (cons) takes T :: T list
 * (fun x -> e2) e1 == let x = e1 in e2

### Data types
 * Type abbreviation: type point = float * float
    * Just an alias - can substitute definition for type wherever.
 * Data type: type mytype = Something | Others
    * Usage: let b : mytype = Something
    * Value with type mytype is either "Something" or "Others"
    * Things after = between | are constructors
    * As many constructors as you want.
    * Can use in match statements
    * Can hold values: type shape = Circle of point * float | Square of point *
      float
    * Examples:
       * type tree = Leaf | Node of key * value * tree * tree
       * type nat = Zero | Succ of nat
 * Parameterized type: type ('key, 'val) tree = Leaf | Node of 'key * 'val *
   ('key, 'val) tree * ('key, 'val) tree
    * Basically a function that takes a type as arg, produces a type
    * Usage of example:
      1. type 'a stree = (string, 'a) tree
      2. type sitree = int stree

### Important functions
 * **map** (f:'a -> 'b) (xs:'a list) : 'b list
   * applies f to every item in xs
 * **reduce** (f:'a -> 'b -> 'b) (u:'b) (xs:'a list) : 'b
   * applies f to each item in xs, using u as an accumulator
   * can implement basically everything with this, including map


## Proof Principles

### Equality Properties
 * **Reflexivity**: e == e
 * **Symmetry**: if e1 == e2 then e2 == e1
 * **Tranistivity**: if e1 == e2 and e2 == e3 then e1 == e3
 * **Evaluation**: if e1 --> e2 then e1 == e2

### More Things that are equal
 * let f x = body == let f = (fun x -> body)
    * AKA function de-sugaring
 * (fun x -> ... x ...) arg == ... arg ...
    * if arg is a value or always produces one
    * AKA substitution
 * let f = def == let f x = (def) x
    * AKA eta-expansion
 * 2 expressions are equal iff:
    * both evaluate to same value, or
    * both raise the same exception, or
    * both infinite loop
 * if expressions e1 == e2, FOO(e1) == FOO(e2)

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
 3. Prove case k+1, assuming proof is true for some k
    * must cover all cases one way or another

### Important notes
 * Ocaml evaluates function arguments to a value _before_ calling the function


## Space
 * Important things are **Live Space** (Space currently used) and **Rate of
   Allocation**
 * Data representations:
    * Tuples - Consecutive bytes of memory (like Java arrays)
    * Lists - Linked List
    * Parameterized types: Name of type, pointed to address of tuple of data
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
       * Defining a function sum: rather than recurse on hd + sum tail, recurse
         on sum tail (fun s -> k (hd + s)) (k is the new argument)
       * We're just passing the rest of the non-tail-recursive function as an
         argument
    * Rather than storing info on the stack, it becomes a bunch of functions
      pointing to each other on the heap 
       * Heap has way more space than the stack, so this is ok.
    * Once you go low level enough, everything is CPS, regardless of it's on
      the stack or the heap
 * **A-Normal Form**: Itermediate computations are given names, serves as a
   nice middle step between direct (non-tail-recursive) style and CPS-converted
    1. **Direct Style**: let g input = f3 (f2 (f1 input))
    2. **A-Normal Form**: let g input = let x1 = f1 input in let x2 = f2 x1 in
       f3 x2
    3. **CPS-Converted**: let g input k = f1 input (fun x1 -> f2 x1 (fnu x2 ->
       f3 x2 k))

