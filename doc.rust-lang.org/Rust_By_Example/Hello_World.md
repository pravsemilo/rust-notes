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
# References
* https://doc.rust-lang.org/stable/rust-by-example/hello.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/comment.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/print.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/print/print_debug.html
