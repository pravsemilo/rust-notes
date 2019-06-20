# Primitives
* __Scalar Types__
	* Signed Integers : `i8`, `i16`, `i32`, `i64`, `i128` and `isize` (pointer size).
	* Unsigned Integers : `u8`, `u16`, `u32`, `u64`, `u128` and `usize` (pointer size).
	* Floating Point :  `f32`, `f64`.
	* `char`
		* Unicode scalar values.
		* 4 bytes each.
	* `bool`
	* Unit Type `()`
		* Only possible value is an empty tuple `()`.
		* Despite the value of a unity type being a tuple, it is not considered a compound type because it does not contain multiple values.
* __Compound Types__
	* Arrays like `[1, 2, 3]`.
	* Tuples like `(1, true)`.
* Variables can always be type annotated.
* Numbers may additionaly be annotatd via a _suffix_ or _by default_.
* Integers default to `i32`.
* Floats default to `f64`.
* Rust can also infer types from context.
```rust
fn main() {
    // Variables can be type annotated.
    let logical: bool = true;

    let a_float: f64 = 1.0;  // Regular annotation
    let an_integer   = 5i32; // Suffix annotation

    // Or a default will be used.
    let default_float   = 3.0; // `f64`
    let default_integer = 7;   // `i32`

    // A type can also be inferred from context
    let mut inferred_type = 12; // Type i64 is inferred from another line
    inferred_type = 4294967296i64;

    // A mutable variable's value can be changed.
    let mut mutable = 12; // Mutable `i32`
    mutable = 21;

    // Error! The type of a variable can't be changed.
    mutable = true;

    // Variables can be overwritten with shadowing.
    let mutable = true;
}
```
# References
* https://doc.rust-lang.org/stable/rust-by-example/primitives.html
