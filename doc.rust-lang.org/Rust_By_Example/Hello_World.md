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
# References
* https://doc.rust-lang.org/stable/rust-by-example/hello.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/comment.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/print.html
