- Feature Name: `generalized_type_ascription`
- Start Date: 2018-08-10
- RFC PR: _
- Rust Issue: _

# Summary
[summary]: #summary

[RFC 803]: https://github.com/rust-lang/rfcs/blob/master/text/0803-type-ascription.md

This RFC supersedes and subsumes [RFC 803].
We finalize a general notion of type ascription uniformly in patterns,
expressions, `let` bindings. You may now for example write:

```rust
let x = (0..10).collect() : Vec<_>;

let alpha: u8 = expr;
    ^^^^^^^^^

let [x: u8, y, z] = stuff();
    ^^^^^^^^^^^^^

if let Some(beta: u8) = expr { .. }
            ^^^^^^^^

for x: i8 in 0..100 { .. }
    ^^^^^
```

Here, the underlined bits are patterns.

Finally, when a user writes `Foo { $field: $pat : $type }`, and when
`$pat` and `$type` are syntactically α-equivalent, the compiler emits a
warn-by-default lint suggesting: `Foo { $field: ($pat : $type) }`.

# Motivation
[motivation]: #motivation

## Type ascription is useful

[RFC_803_motivation]: https://github.com/rust-lang/rfcs/blob/master/text/0803-type-ascription.md#motivation

[pointfree persuasion]: https://en.wikipedia.org/wiki/Tacit_programming

[TwoHardThings]: https://martinfowler.com/bliki/TwoHardThings.html

Type ascription is useful. A motivation for the feature is noted in the merged,
but thus far not stabilized, [RFC 803][RFC_803_motivation] which introduces
type ascription in expression contexts as `expr : T`. We reinforce that RFC
with more motivation:

1. With type ascription, you can annotate smaller bits and subsets of what you
   previously needed to. This especially holds in pattern contexts.
   This will be made clear later on in this RFC.

...

# Drawbacks
[drawbacks]: #drawbacks

## Language Complexity

We believe that we've demonstrated that this RFC simplifies the language by
applying rules uniformly, and thus has a negative complexity cost on balance.
However, this view may not be shared by everybody. It is a legitimate position
to take to view this as an increase in language complexity.

## Potential conflict with named arguments

Consider the following function definition:

```rust
fn foo(alpha: u8, beta: bool) { ... }
```

Some have proposed that we introduce named function arguments into Rust.
One of the syntaxes that have been proposed are:

```rust
foo(alpha: 1, beta: true)
```

However, this syntax conflicts with type ascription in expression contexts.
For those who value named arguments over type ascription, they may want to retain
the syntax `argument: expr` because it is reminiscent of the struct literal
syntax `Struct { field: expr }`. However, we argue that it is only weakly so.
In particular, note that functions are not called with braces but that they are
called with parenthesis. Therefore, they are more syntactically kindred with
tuple structs which have positional field names. Thus, a more consistent
function call syntax would be `foo { alpha: 1, beta: true }`.

Furthermore, it is still possible to come up with other syntaxes for named arguments.
For example, you could hypothetically write `foo(alpha = 1, beta = 2)`.


# Rationale and alternatives
[alternatives]: #rationale-and-alternatives

## Do nothing

We could opt to not do anything and leave type ascription in a half-baked
and inconsistent state. In that case, we would only have [RFC 803] which
gives us type ascription in expression contexts and in a mandatory way on
function parameters as well as optionally on `let` bindings.
It is also possible to unaccept [RFC 803] and have no type ascription but for
function definitions and let bindings.


# Unresolved questions
[unresolved]: #unresolved-questions

[ ] Question 1
[ ] Question 2

# Possible future work
[possible future work]: #possible-future-work

In previous versions of this RFCs some features were proposed including:

- Block ascription syntax; e.g. `async: Type { ... }` or `try: Type { ... }`.
- Making the syntax of function parameters into `fn name(pat0, pat1, ..)`
  rather than `fn name(pat0: type0, pat1: type1, ..)`.

These have since been removed from this particular RFC and will be proposed
separately instead.
