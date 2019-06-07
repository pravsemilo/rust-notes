# Hello World
```rust
fn main() {
	println!("Hello World!");
}
```
* `println!` is a macro that prints next to the console.
* A binary can generated using Rust compiler `rustc`. It will produce a `hello` binary that can be executed.
```bash
$ rustc hello.rs
$ ./hello
```
## Comments
* *Regular Comments*
```rust
// Single line comments
/* Block comments */
```
* *Doc Comments*
```rust
/// Generate library docs for the following item.
//! Generate library docs for the enclosing item.
```
## Formatted print
* Printing is handled by a series of `macros` defined in `std::fmt` :
	* `format!` : Write formatted text to `String`.
	* `print!` : Same as `format!` but text is printed to the console (io::stdout).
	* `println!` : Same as `print!` but a new line is appended.
	* `eprint!` : Same as `format!` but text is printed to the standard error (io::stderr).
	* `eprintln!` : Same as `eprint!` but a new line is appended.
* A plus is that the formatting correctness will be checked at compile time.
```rust
fn main() {
    // In general, the `{}` will be automatically replaced with any
    // arguments. These will be stringified.
    println!("{} days", 31);

    // Without a suffix, 31 becomes an i32. You can change what type 31 is,
    // with a suffix.

    // There are various optional patterns this works with. Positional
    // arguments can be used.
    println!("{0}, this is {1}. {1}, this is {0}", "Alice", "Bob");

    // As can named arguments.
    println!("{subject} {verb} {object}",
             object="the lazy dog",
             subject="the quick brown fox",
             verb="jumps over");

    // Special formatting can be specified after a `:`.
    println!("{} of {:b} people know binary, the other half doesn't", 1, 2);

    // You can right-align text with a specified width. This will output
    // "     1". 5 white spaces and a "1".
    println!("{number:>width$}", number=1, width=6);

    // You can pad numbers with extra zeroes. This will output "000001".
    println!("{number:>0width$}", number=1, width=6);

    // It will even check to make sure the correct number of arguments are
    // used.
    println!("My name is {0}, {1} {0}", "Bond", "James");
    // FIXME ^ Add the missing argument: "James"

    // Create a structure which contains an `i32`. Name it `Structure`.
    #[allow(dead_code)]
    struct Structure(i32);

    // However, custom types such as this structure require more complicated
    // handling. This will not work.
    //println!("This struct `{}` won't print...", Structure(3));
    // FIXME ^ Comment out this line.
}
```
* `std::fmt` contains many `traits` which govern the display of text. For example :
	* `fmt::Debug` : Uses the `{:?}` marker. Formats text for debugging purposes.
	* `fmt::Display` : Uses the `{}` marker. Formats text in a more elegant, user friendly fashion.
* Implementing the `fmt::Display` trait automagically implements the `ToString` trait which allows us to convert the type to `String`.
### Debug
* All types which want to use `std::fmt` formatting `traits` require an implementation to be printable.
* Automatic implementations are only provided for types such as in the `std` library.
* `fmt::Debug` makes this very easy.
	* All types can `derive` (automatically create) the `fmt::Debug` implementation.
	* This is not true for `fmt:Display` which must be manually implemented.
```rust
// This structure cannot be printed either with `fmt::Display` or
// with `fmt::Debug`
struct UnPrintable(i32);

// The `derive` attribute automatically creates the implementation
// required to make this `struct` printable with `fmt::Debug`.
#[derive(Debug)]
struct DebugPrintable(i32);
```
* All `std` library types automatically are printable with `{:?}` too :
```rust
// Derive the `fmt::Debug` implementation for `Structure`. `Structure`
// is a structure which contains a single `i32`.
#[derive(Debug)]
struct Structure(i32);

// Put a `Structure` inside of the structure `Deep`. Make it printable
// also.
#[derive(Debug)]
struct Deep(Structure);

fn main() {
    // Printing with `{:?}` is similar to with `{}`.
    println!("{:?} months in a year.", 12);
    println!("{1:?} {0:?} is the {actor:?} name.",
             "Slater",
             "Christian",
             actor="actor's");

    // `Structure` is printable!
    println!("Now {:?} will print!", Structure(3));

    // The problem with `derive` is there is no control over how
    // the results look. What if I want this to just show a `7`?
    println!("Now {:?} will print!", Deep(Structure(7)));
}
```
* `fmt::Debug` makes things printable but at the cost of elegance.
	* Rust also provides pretty printing with `{:?}`.
```rust
#[derive(Debug)]
struct Person<'a> {
    name: &'a str,
    age: u8
}

fn main() {
    let name = "Peter";
    let age = 27;
    let peter = Person { name, age };

    // Pretty print
    println!("{:#?}", peter);
}
```
* One can manually implement `fmt::Display` to control the display.
### Display
* `fmt::Debug` is not compact and clean.
* To customize the appearance, we need to manually implement `fmt::Display`, which uses the `{}` print marker.
```rust

#![allow(unused_variables)]
fn main() {
// Import (via `use`) the `fmt` module to make it available.
use std::fmt;

// Define a structure which `fmt::Display` will be implemented for. This is simply
// a tuple struct containing an `i32` bound to the name `Structure`.
struct Structure(i32);

// In order to use the `{}` marker, the trait `fmt::Display` must be implemented
// manually for the type.
impl fmt::Display for Structure {
    // This trait requires `fmt` with this exact signature.
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Write strictly the first element into the supplied output
        // stream: `f`. Returns `fmt::Result` which indicates whether the
        // operation succeeded or failed. Note that `write!` uses syntax which
        // is very similar to `println!`.
        write!(f, "{}", self.0)
    }
}
```
* Because there is no ideal style for all types and the `std` library doesn't presume to dictate one, `fmt::Display` is not implemented for `Vec<T>` or for any other generic containers.
	* `fmt::Debug` should be used for these generic cases.
	* For non generic new container types, `fmt::Display` can be implemented.
```rust
use std::fmt; // Import `fmt`

// A structure holding two numbers. `Debug` will be derived so the results can
// be contrasted with `Display`.
#[derive(Debug)]
struct MinMax(i64, i64);

// Implement `Display` for `MinMax`.
impl fmt::Display for MinMax {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Use `self.number` to refer to each positional data point.
        write!(f, "({}, {})", self.0, self.1)
    }
}

// Define a structure where the fields are nameable for comparison.
#[derive(Debug)]
struct Point2D {
    x: f64,
    y: f64,
}

// Similarly, implement for Point2D
impl fmt::Display for Point2D {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Customize so only `x` and `y` are denoted.
        write!(f, "x: {}, y: {}", self.x, self.y)
    }
}

fn main() {
    let minmax = MinMax(0, 14);

    println!("Compare structures:");
    println!("Display: {}", minmax);
    println!("Debug: {:?}", minmax);

    let big_range =   MinMax(-300, 300);
    let small_range = MinMax(-3, 3);

    println!("The big range is {big} and the small is {small}",
             small = small_range,
             big = big_range);

    let point = Point2D { x: 3.3, y: 7.2 };

    println!("Compare points:");
    println!("Display: {}", point);
    println!("Debug: {:?}", point);

    // Error. Both `Debug` and `Display` were implemented but `{:b}`
    // requires `fmt::Binary` to be implemented. This will not work.
    // println!("What does Point2D look like in binary: {:b}?", point);
}
```
# References
* https://doc.rust-lang.org/stable/rust-by-example/hello.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/comment.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/print.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/print/print_debug.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/print/print_display.html
