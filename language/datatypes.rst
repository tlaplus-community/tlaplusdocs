.. _datatypes:

++++++++++++++++++
Datatypes
++++++++++++++++++

Primitive Datatypes
===================

Booleans
--------

Use the value keywords ``TRUE`` and ``FALSE`` for Boolean literal values.
The keyword ``BOOLEAN`` evaluates to the set ``{TRUE, FALSE}``.
For operations on boolean values see Logic Operators.

Numbers
--------

Number literals are defined as you might expect, for example ``12345`` or ``-42`` or ``3.14159`` or ``0``.
The infinite sets of Naturals, Integers, and Reals are defined as ``Nat``, ``Int``, and ``Real`` in the ``Naturals``, ``Integers``, and ``Reals`` standard modules respectively.
Those definitions must have their values explicitly overridden by a finite set during modelchecking.
Operations on numbers are also defined in those standard modules.

Strings
-------

String literal values are defined using quotes, as in ``"foobar"``.
The keyword ``STRING`` evaluates to the set of all strings.
Although ``STRING`` is an infinite set that cannot be enumerated during modelchecking, expressions such as ``"foobar" \in STRING`` can be evaluated.

.. _sets:

Sets
====

Sets are unordered collections of elements.
Set literals are defined using curly braces, for example ``{1, 2, 3}``.
Defining sets of contiguous Natural numbers is so common that it has its own syntax: ``1 .. 3`` to define the set ``{1, 2, 3}``.
Sets can contain elements of any type, including other sets; ``{"a", "b", {1, {2}, 3}}`` is a valid set.
The empty set is denoted ``{}``.
TLA+ has a number of built-in operators defined for sets:

 * ``S = T`` for checking set equality
 * ``S /= T`` or ``S # T`` for checking set inequality
 * ``e \in S`` for checking whether an element ``e`` is a member of set ``S``
 * ``e \notin S`` for checking whether an element ``e`` is *not* a member of set ``S``
 * ``S \cup T`` evaluates to the union of ``S`` and ``T``
 * ``S \cap T`` evaluates to the conjunction of ``S`` and ``T``
 * ``S \subseteq T`` checks whether ``S`` is a subset of ``T``
 * ``S \ T`` evaluates to the set of all elements in ``S`` that are not in ``T``
 * ``SUBSET S`` is the set of all subsets of ``S``, also called the power set
 * ``UNION S`` "flattens" ``S`` by evaluating to the union of all of its elements

Two very useful operators are used to map or filter over sets:

 * ``{f : e \in S}`` is the set with function ``f`` applied to each element ``e`` in ``S``
 * ``{e \in S : p}`` is the set of elements in ``S`` such that ``p`` is true, where ``p`` is any Boolean expression

These can be combined by nesting; for example, we can take the set of all even numbers and double them as follows:

``{2*n : n \in {x \in Nat : x % 2 = 0}}``

A number of additional set operators are defined in the ``FiniteSets`` standard module.

.. _functions:

Functions
=========

Functions are data structures mapping elements of their domain to elements of their range.
Their action must be defined for every element in their domain.
They bear some resemblance in usage to macros, but important differences exist.
Functions are usually defined as follows:

``f[e \in S] = q``

Where ``f`` is the name of the function, and ``q`` is some expression transforming ``e``.
The action of ``f`` on some element ``e`` of ``S`` is given by the expression ``f[e]``.
Functions can also be defined anonymously:

``[e \in S |-> q]``

For example, we can write the function ``mod2`` which maps the Natural numbers to their value modulo 2; there are two equivalent ways to write it although in either case ``mod2[2] = 0``, ``mod2[3] = 1``, etc.:

``mod2[n \in Nat] == n % 2``

``mod2 == [n \in Nat |-> n % 2]``

The domain set of a function is given by the expression:

``DOMAIN f``

In the above examples, ``DOMAIN mod2 = Nat``.

The set of all functions from a domain ``S`` to a range ``T`` is given by:

``[S -> T]``

Note the difference in usage between ``|->`` and ``->``, commonly confused.
In the above examples, ``mod2`` is in the set ``[Nat -> {0, 1}]``; in fact, we can write ``mod2 \in [Nat -> {0, 1}]`` which evaluates to ``TRUE``.

New functions can be derived from existing functions using the ``EXCEPT`` keyword to modify the original function's action on a specific element of the domain:

``[f EXCEPT ![e] = q]``

For example, we can write ``[mod2 EXCEPT ![3] = 1 - @]`` to derive a new function from ``mod2`` giving the wrong answer on input ``3``, where the symbol ``@`` evaluates to the original value of ``mod2[3]``.

Multiple function values can be redefined at once, separated by commas:

``[f EXCEPT ![e1] = q1, ![e2] = q2, ![e3] = q3]``

This is shorthand for nested ``EXCEPT`` statements:

``[[[f EXCEPT ![e1] = q1] EXCEPT ![e2] = q2] EXCEPT ![e3] = q3]``

Finally, importing the standard module ``TLC`` enables explicit definition of a function for each element of the domain: 

``e1 :> q1 @@ e2 :> q2 @@ e3 :> q3``

You can also use ``@@`` to combine two separate functions into a single function; the function on the left is given precedence if elements of the domain overlap:

``f1 @@ f2``

.. _records:

Records
=======

Records are a special subset of functions where the domain is restricted to strings, functioning similarly to rows in a database.
The set of all records of a given type is defined as follows, where ``S``, ``T``, and ``U`` are sets:

``[name1 : S, name2 : T, name3 : U]``

A specific element of this set of records is written:

``r == [name1 |-> s, name2 |-> t, name3 |-> u]``

Where ``s``, ``t``, and ``u`` are elements of the sets ``S``, ``T``, and ``U`` respectively.
There are two equivalent methods of accessing the fields of this record (although the former is preferred):

``r.name1``

``r["name1"]``

As an example, we might have a set of records describing people:

``People == [name : STRING, age : Nat]``

A specific element of this set could be:

``Person == [name |-> "Leslie Lamport", age |-> 79]``

Where ``Person \in People``.
Same as functions, new records can be derived from existing records using ``EXCEPT``:

``[Person EXCEPT !.age = 80]``

Or equivalently:

``[Person EXCEPT !["age"] = 80]``

.. _tuples:

Tuples
======

Tuples are a subset of functions where the domain is restricted to ``1..N`` for some ``N`` (the number of elements in the tuple).
The set of all 3-tuples where the first element is in the set ``S``, the second is in the set ``T``, and the third is in the set ``U`` is written as follows, where ``\X`` is the cross-product:

``S \X T \X U``

A specific tuple in this set is written as follows, where ``s``, ``t``, and ``u`` are elements of the sets ``S``, ``T``, and ``U`` respectively:

``tuple == <<s, t, u>>``

The specific elements of this tuple are accessed with ``tuple[1]``, ``tuple[2]``, and ``tuple[3]`` for ``s``, ``t``, and ``u`` respectively.
Note the tuple is indexed from one, not zero.
The above tuple is equivalent to this function:

``1 :> s @@ 2 :> t @@ 3 :> u``

Since tuples are just functions, they can be manipulated in all the usual ways - ``DOMAIN``, ``EXCEPT``, etc.

.. _sequences:

Sequences
=========

Sequences behave like arrays.
They are completely identical to tuples, except that a spec will generally treat sequences as variable-length while tuples are of fixed length; also, usually every element in a sequence is a member of the same set.
The empty sequence is defined as follows:

``<<>>``

Operators on sequences are defined in the ``Sequences`` standard module; they straddle the line between built-in language primitive and module-defined data structure, but are so commonly used they should be mentioned here.
The set of all sequences whose elements are in set ``S`` is defined as:

``Seq(S)``

So for example:

``<<0, 1, 0, 1, 0, 1, 0, 1>> \in Seq({0, 1})``

See the ``Sequences`` standard module documentation for more operators (append, etc.)
