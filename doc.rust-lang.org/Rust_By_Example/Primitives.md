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
## Tuples
* A tuple is a collection of values of different types.
* Constructed using parentheses `()`.
* Each tuple itself is a value with type signature `(T1, T2, ...)`, where `T1`, `T2` are the types of its members.
* Functions can use tuples to return multiple values.
```rust
// Tuples can be used as function arguments and as return values
fn reverse(pair: (i32, bool)) -> (bool, i32) {
    // `let` can be used to bind the members of a tuple to variables
    let (integer, boolean) = pair;

    (boolean, integer)
}

// The following struct is for the activity.
#[derive(Debug)]
struct Matrix(f32, f32, f32, f32);

fn main() {
    // A tuple with a bunch of different types
    let long_tuple = (1u8, 2u16, 3u32, 4u64,
                      -1i8, -2i16, -3i32, -4i64,
                      0.1f32, 0.2f64,
                      'a', true);

    // Values can be extracted from the tuple using tuple indexing
    println!("long tuple first value: {}", long_tuple.0);
    println!("long tuple second value: {}", long_tuple.1);

    // Tuples can be tuple members
    let tuple_of_tuples = ((1u8, 2u16, 2u32), (4u64, -1i8), -2i16);

    // Tuples are printable
    println!("tuple of tuples: {:?}", tuple_of_tuples);

    // But long Tuples cannot be printed
    // let too_long_tuple = (1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13);
    // println!("too long tuple: {:?}", too_long_tuple);
    // TODO ^ Uncomment the above 2 lines to see the compiler error

    let pair = (1, true);
    println!("pair is {:?}", pair);

    println!("the reversed pair is {:?}", reverse(pair));

    // To create one element tuples, the comma is required to tell them apart
    // from a literal surrounded by parentheses
    println!("one element tuple: {:?}", (5u32,));
    println!("just an integer: {:?}", (5u32));

    //tuples can be destructured to create bindings
    let tuple = (1, "hello", 4.5, true);

    let (a, b, c, d) = tuple;
    println!("{:?}, {:?}, {:?}, {:?}", a, b, c, d);

    let matrix = Matrix(1.1, 1.2, 2.1, 2.2);
    println!("{:?}", matrix);

}
```
## Arrays and Slices
* Array is a collection of objects of the same type `T`.
	* Stored in contiguous memory.
	* Created using `[]`.
	* Size is known at compile time.
	* Type signature : `[T; size]`.
* Slices
	* Similar to arrays but size is not known at compile time.
	* Slice is a two word object.
		* The first word is a pointer to the data.
		* The second word is the length of the slice.
	* Word size is same as usize, determined by the processor architecture.
	* Slices can be used to borrow a section of an array.
	* Type signature : `&[T]`.
```rust
use std::mem;

// This function borrows a slice
fn analyze_slice(slice: &[i32]) {
    println!("first element of the slice: {}", slice[0]);
    println!("the slice has {} elements", slice.len());
}

fn main() {
    // Fixed-size array (type signature is superfluous)
    let xs: [i32; 5] = [1, 2, 3, 4, 5];

    // All elements can be initialized to the same value
    let ys: [i32; 500] = [0; 500];

    // Indexing starts at 0
    println!("first element of the array: {}", xs[0]);
    println!("second element of the array: {}", xs[1]);

    // `len` returns the size of the array
    println!("array size: {}", xs.len());

    // Arrays are stack allocated
    println!("array occupies {} bytes", mem::size_of_val(&xs));

    // Arrays can be automatically borrowed as slices
    println!("borrow the whole array as a slice");
    analyze_slice(&xs);

    // Slices can point to a section of an array
    println!("borrow a section of the array as a slice");
    analyze_slice(&ys[1 .. 4]);

 	   // Out of bound indexing causes compile error
    println!("{}", xs[5]);
}
```
# References
* https://doc.rust-lang.org/stable/rust-by-example/primitives.html
* https://doc.rust-lang.org/stable/rust-by-example/primitives/literals.html
* https://doc.rust-lang.org/stable/rust-by-example/primitives/tuples.html
* https://doc.rust-lang.org/stable/rust-by-example/primitives/array.html
