Rust is not an object-oriented language. Notions such as inheritance are part of 
the language. Instead, Rust's main vehicle for implementing abstractions are _traits_. 

## What are Traits?
Traits are essentially another name for "interface", if you're familiar with the term. 
You can think of them as a  contract that some type needs to fulfill. More specifically, 
a particular trait declares functions that a type needs to implement in order to fulfill
the trait's contract. 

The standard library exports many useful traits. Some examples include the `Debug`, 
`Clone`, and `Display` traits. A type that implements the `Debug` trait means that the
type implements the requisite functionality to be debuggable, as in the type can be 
formatted into a programmer-facing debugging context. A type that implements the 
`Clone` trait means that the type implements the required functionality to be cloneable. 

Many traits in the standard library can automatically be derived using a `#[derive]`
annotation. For example:

```rust
#[derive(Debug, Clone)]
struct Coordinate<T> {
    x: f32,
    y: f32,
}
```

This means that our `Foo` type is now both debuggable and cloneable, and we didn't have
to implement that logic ourself!

However, not every trait can be automatically derived in this way. The `Display` trait,
which communicates the intent that the given type can be printed in a user-facing context,
is one such trait that cannot be automatically derived; it needs to be manually implemented.
The `Display` trait looks like this:

```rust
use std::fmt;

trait Display {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result;
}
```

We can implement the trait then for our `Coordinate` type like so:
```rust
impl fmt::Display for Coordinate {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "({}, {})", self.x, self.y)
    }
}
```

Our implementation specifies that we want our `Coordinate` type to be displayed by writing
its two component fields formatted with the format specifier `"({}, {})"`. 

Note that this is just one posssible way for we could have specified that we wanted our type
to be displayed. So long as we implement the `fmt` function in a way that adhere's to the 
prescribed type signature, the inner logic of the `fmt` function can act however we'd like!
This is why the `Display` trait in particular cannot be automatically derived: there's no 
sensible default to fall back to for arbitrary types. 

## Using Traits
We've talked about how to implement traits, but why do we even need to implement traits
for our types in the first place?  

Another way to think about traits is as encapsulating _functionality_, compared to the 
object-oriented paradigm, where _objects_ are encapsulated. Traits encapsulate patterns
of functionality.

For example, if we want to implement a generic sorting algorithm that takes as input a 
slice of some type `T`, our algorithm wouldn't know what to do with types that don't have
some notion of order attached to them. This is where _trait bounds_ come in. They allow us
to specify particular traits that we require of input types to some function. 

The standard library exports the `Ord` trait for encapsulating the notion of ordering.
We can use it to constrain the kinds of types that our implementation will accept:
```rust
use std::cmp;

fn sort<T: cmp::Ord>(items: &mut [T]) 
```

We can even place multiple trait bounds on generic types!
