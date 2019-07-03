# Conversion
* Rust addresses conversion between types by use of `traits`.
* Generic conversions will use the `From` and `Into` traits.
* However there are more specific ones for the more common cases, in particular when converting to and from `String`.
## `From` and `Into`
* `From` and `Into` traits are inherently linked.
* If you are able to convert type A from type B, then it should be possible to convert type B to type A.
### `From`
* Allows for a type to define how to create itself from another type.
* Provides a mechanism for converting between several types.
* Example : We can easily convert a `str` into a `String`.
```rust
let my_str = "hello";
let my_string = String::from(my_str);
```
```rust
use std::convert::From;

#[derive(Debug)]
struct Number {
    value: i32,
}

impl From<i32> for Number {
    fn from(item: i32) -> Self {
        Number { value: item }
    }
}

fn main() {
    let num = Number::from(30);
    println!("My number is {:?}", num);
}
```
### `Into`
* Reciprocal of the `From` trait.
* If you have implemented the `From` trait for your type, you get the `Into` implementation for free.
* Using the `Into` trait will require specification of the type to convert into as the compiler is unable to determine this most of the time.
```rust
use std::convert::From;

#[derive(Debug)]
struct Number {
    value: i32,
}

impl From<i32> for Number {
    fn from(item: i32) -> Self {
        Number { value: item }
    }
}

fn main() {
    let int = 5;
    // Try removing the type declaration
    let num: Number = int.into();
    println!("My number is {:?}", num);
}
```
# References
* https://doc.rust-lang.org/stable/rust-by-example/conversion.html
* https://doc.rust-lang.org/stable/rust-by-example/conversion/from_into.html
