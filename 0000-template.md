- Feature Name: Allow overlapping impls for marker traits
- Start Date: 2015-09-01
- RFC PR: (leave this empty)
- Rust Issue: (leave this empty)

# Summary

Preventing overlapping implementations of a trait makes complete sense in the context of determining method dispatch. There must not be ambiguity in what code will actually be run for a given type. However, for marker traits, there are no associated methods for which to indicate ambiguity. There is no harm in a type being marked as `Sync` for multiple reasons.

# Motivation

This is purely to improve the ergonomics of adding/implementing marker traits. While specialization will certainly make all cases not covered today possible, removing the restricition entirely will improve the ergonomics in several edge cases.

# Detailed design

For the purpose of this RFC, the definition of a marker trait is a trait with no associated functions, which does not inherit from any other trait. The design here is quite straightforward. The following code fails to compile today:

```rust
trait Marker<A> {}

struct GenericThing<A, B> {
    a: A,
    b: B,
}

impl<A, B> Marker<GenericThing<A, B>> for A {}
impl<A, B> Marker<GenericThing<A, B>> for B {}
```

The two impls are considered overlapping, as there is no way to prove currently that `A` and `B` are not the same type. However, in the case of marker traits, there is no actual reason that they couldn't be overlapping, as no code could actually change based on the `impl`.

# Drawbacks

This causes marker traits to become a special case, and the rules for overlapping impls become inconsistent when looking at all traits. Overall, it causes a minor increase in total complexity of the language.

# Alternatives

Once specialization lands, there does not appear to be a case that is impossible to write, albeit with some additional boilerplate.

# Unresolved questions

None at this time.
