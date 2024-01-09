- Feature Name: where-eq-supertrait
- Start Date: (fill me in with today's date, 2024-01-09)
- RFC PR: [rust-lang/rfcs#0000](https://github.com/rust-lang/rfcs/pull/0000)
- Rust Issue: [rust-lang/rust#0000](https://github.com/rust-lang/rust/issues/0000)

# Summary
[summary]: #summary

Trait bounds expressed in `where` bounds should be treated in the same way as
supertrait bounds.

Currently, they are the both the same and different.  They
are the same in the sense that a type only implements the trait if it meets both
the supertrait bounds and the `where` bounds.  But they are different in that
uses of the trait will assume that supertrait bounds are satisfied, but not
`where` bounds.

# Motivation
[motivation]: #motivation

Rust traits provide two ways that the trait definition can constrain
implementations.  A trait can specify supertraits, which a type must implement
in order to implement the trait in question.  A trait definition can also
contain a `where` clause that expresses constraints that a type must meet in
order to imiplement the trait in question.

Some constraints cannot be expressed as supertraits.  This introduces some odd
asymmetries.  For example, a trait can require addition to `T` on the right to
work (`t + x` for an implementor `x`) with a supertrait `Add<T>`, but a requirement for
addition on the left to work can only be expressed with a `where` clause, `T:
Add<Self>`.

This is probalematic when one wants to be generic over implementations of the
trait.  The compiler will infer that a generic parameter bound to implement a
trait also implements that trait's supertraits.  But the compiler will *not*
infer that `where` clauses have been satisfied -- even though it is impossible
for the type to implement the trait unless they are satisfied.  Thus, the
`where` clauses propagate up the call stack until they are fulfilled by a
concrete instantiation.

The following small example illustrates the problem:

https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=6a34be4c2371177a139dc665978765e1

In this small example, the cascading `where` clause is not that large, but in
more complicated cases, with generic parameters each bringing multiple `where`
constraints, it can get result in large blobs of `where` gunk that have to be
replicated over and over.


# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

Explain the proposal as if it was already included in the language and you were teaching it to another Rust programmer. That generally means:

- Introducing new named concepts.
- Explaining the feature largely in terms of examples.
- Explaining how Rust programmers should *think* about the feature, and how it should impact the way they use Rust. It should explain the impact as concretely as possible.
- If applicable, provide sample error messages, deprecation warnings, or migration guidance.
- If applicable, describe the differences between teaching this to existing Rust programmers and new Rust programmers.
- Discuss how this impacts the ability to read, understand, and maintain Rust code. Code is read and modified far more often than written; will the proposed feature make code easier to maintain?

For implementation-oriented RFCs (e.g. for compiler internals), this section should focus on how compiler contributors should think about the change, and give examples of its concrete impact. For policy RFCs, this section should provide an example-driven introduction to the policy, and explain its impact in concrete terms.

# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

This is the technical portion of the RFC. Explain the design in sufficient detail that:

- Its interaction with other features is clear.
- It is reasonably clear how the feature would be implemented.
- Corner cases are dissected by example.

The section should return to the examples given in the previous section, and explain more fully how the detailed proposal makes those examples work.

# Drawbacks
[drawbacks]: #drawbacks

Why should we *not* do this?

# Rationale and alternatives
[rationale-and-alternatives]: #rationale-and-alternatives

- Why is this design the best in the space of possible designs?
- What other designs have been considered and what is the rationale for not choosing them?
- What is the impact of not doing this?
- If this is a language proposal, could this be done in a library or macro instead? Does the proposed change make Rust code easier or harder to read, understand, and maintain?

# Prior art
[prior-art]: #prior-art

Discuss prior art, both the good and the bad, in relation to this proposal.
A few examples of what this can include are:

- For language, library, cargo, tools, and compiler proposals: Does this feature exist in other programming languages and what experience have their community had?
- For community proposals: Is this done by some other community and what were their experiences with it?
- For other teams: What lessons can we learn from what other communities have done here?
- Papers: Are there any published papers or great posts that discuss this? If you have some relevant papers to refer to, this can serve as a more detailed theoretical background.

This section is intended to encourage you as an author to think about the lessons from other languages, provide readers of your RFC with a fuller picture.
If there is no prior art, that is fine - your ideas are interesting to us whether they are brand new or if it is an adaptation from other languages.

Note that while precedent set by other languages is some motivation, it does not on its own motivate an RFC.
Please also take into consideration that rust sometimes intentionally diverges from common language features.

# Unresolved questions
[unresolved-questions]: #unresolved-questions

- What parts of the design do you expect to resolve through the RFC process before this gets merged?
- What parts of the design do you expect to resolve through the implementation of this feature before stabilization?
- What related issues do you consider out of scope for this RFC that could be addressed in the future independently of the solution that comes out of this RFC?

# Future possibilities
[future-possibilities]: #future-possibilities

Think about what the natural extension and evolution of your proposal would
be and how it would affect the language and project as a whole in a holistic
way. Try to use this section as a tool to more fully consider all possible
interactions with the project and language in your proposal.
Also consider how this all fits into the roadmap for the project
and of the relevant sub-team.

This is also a good place to "dump ideas", if they are out of scope for the
RFC you are writing but otherwise related.

If you have tried and cannot think of any future possibilities,
you may simply state that you cannot think of anything.

Note that having something written down in the future-possibilities section
is not a reason to accept the current or a future RFC; such notes should be
in the section on motivation or rationale in this or subsequent RFCs.
The section merely provides additional information.
