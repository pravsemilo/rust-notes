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
## Literals and operators
* Integers, floats, characters, strings, booleans and the unit type can be expressed using literals.
	* Examples :
		* `1`
		* `1.2`
		* `'a'`
		* `"abc"`
		* `true`
		* `()`
* Alternatively, integers can be expressed using hexadecimal, octal or binary notation using these prefixes respectively : `0x`, `0o` or `0b`.
* Underscores can be inserted in numeric literals to improve readability.
	* Examples :
		* `1_000` is same as `1000`.
		* `0.000_001` is same as `0.000001`.
```rust
fn main() {
    // Integer addition
    println!("1 + 2 = {}", 1u32 + 2);

    // Integer subtraction
    println!("1 - 2 = {}", 1i32 - 2);
    // TODO ^ Try changing `1i32` to `1u32` to see why the type is important

    // Short-circuiting boolean logic
    println!("true AND false is {}", true && false);
    println!("true OR false is {}", true || false);
    println!("NOT true is {}", !true);

    // Bitwise operations
    println!("0011 AND 0101 is {:04b}", 0b0011u32 & 0b0101);
    println!("0011 OR 0101 is {:04b}", 0b0011u32 | 0b0101);
    println!("0011 XOR 0101 is {:04b}", 0b0011u32 ^ 0b0101);
    println!("1 << 5 is {}", 1u32 << 5);
    println!("0x80 >> 2 is 0x{:x}", 0x80u32 >> 2);

    // Use underscores to improve readability!
    println!("One million is written as {}", 1_000_000u32);
}
```
# References
* https://doc.rust-lang.org/stable/rust-by-example/primitives.html
* https://doc.rust-lang.org/stable/rust-by-example/primitives/literals.html
