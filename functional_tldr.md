# Princeton COS326: Functional Programming
### A TL;DR by Perry Cate '19

Disclaimer: This is being created as my own study tool. I will make aggressive,
opinionated choices about what to include. YMMV.


## Things not Included
 * History of Functional Languages
 * Names of People
 * Anything I think won't be on the final exam.


## Terms
**Expressions**: Computations. _2+3_
**Values**: Results of computations. Not every computation has one. (Errors,
etc.) _5_
**Tuple**: Fixed, finite, ordered collection of values.
**Unit**: Tuple with zero fields. _()_
**List**: Like a tuple but everything is the same type
**Inductive Data Type**: Has collection of base cases, bunch of inductive cases
that build new values from pre-existing (smaller) values


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

### Things that are equal (use these for proofs)
 * let f x = body == let f = (fun x -> body)
    * AKA function de-sugaring
 * (fun x -> ... x ...) arg == ... arg ...
    * if arg is a value or always produces one
    * AKA substitution
 * let f = def == let f x = (def) x
    * AKA eta-expansion

### Important functions
 * **map** (f:'a -> 'b) (xs:'a list) : 'b list
   * applies f to every item in xs
 * **reduce** (f:'a -> 'b -> 'b) (u:'b) (xs:'a list) : 'b
   * applies f to each item in xs, using u as an accumulator
   * can implement basically everything with this, including map
