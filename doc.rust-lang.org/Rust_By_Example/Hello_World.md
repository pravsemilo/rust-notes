# Hello World
```rust
fn main() {
	println!("Hello World!");
}
```
* `println!` is a macro that prints text to the console.
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
#### Testcase : List
* Implementing `fmt::Display` for a structure where the elements must each be handled sequentially is tricky.
	* The problem is that each `write!` generates a `fmt::Result`.
	* This requires to deal with all the results.
	* Rust provides the `?` operator for this purpose.
	```rust
	// Try `write!` to see if it errors. If it errors, return
	// the error. Otherwise continue.
	write!(f, "{}", value)?;
	```
	* Alternatively you can use the `try!` macro.
		* It is bit more verbose and no longer recommended.
		```rust
		try!(write!(f, "{}", value));
		```
	* With `?` available, implementing `fmt::Display` for a `Vec` is straightforward.
	```rust
	use std::fmt; // Import the `fmt` module.

	// Define a structure named `List` containing a `Vec`.
	struct List(Vec<i32>);

	impl fmt::Display for List {
	    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
		// Extract the value using tuple indexing
		// and create a reference to `vec`.
		let vec = &self.0;

		write!(f, "[")?;

		// Iterate over `v` in `vec` while enumerating the iteration
		// count in `count`.
		for (count, v) in vec.iter().enumerate() {
		    // For every element except the first, add a comma.
		    // Use the ? operator, or try!, to return on errors.
		    if count != 0 { write!(f, ", ")?; }
		    write!(f, "{}", v)?;
		}

		// Close the opened bracket and return a fmt::Result value
		write!(f, "]")
	    }
	}

	fn main() {
	    let v = List(vec![1, 2, 3]);
	    println!("{}", v);
	}
	```
### Formatting
* Formatting is specified via a *format string*.
```rust
format!("{}", foo) // "3735928559"
format!("0x{:X}", foo) // "0xDEADBEEF"
format!("0o{:o}", foo) // "0o33653337357"
```
* Formatting functionality is implemented via traits.
	* There is one trait for each argument type.
	* Most common formatting trait is `Display`, which handles the cases where the argument type is left unspecified `{}`.
	```rust
	use std::fmt::{self, Formatter, Display};

	struct City {
	    name: &'static str,
	    // Latitude
	    lat: f32,
	    // Longitude
	    lon: f32,
	}

	impl Display for City {
	    // `f` is a buffer, this method must write the formatted string into it
	    fn fmt(&self, f: &mut Formatter) -> fmt::Result {
		let lat_c = if self.lat >= 0.0 { 'N' } else { 'S' };
		let lon_c = if self.lon >= 0.0 { 'E' } else { 'W' };

		// `write!` is like `format!`, but it will write the formatted string
		// into a buffer (the first argument)
		write!(f, "{}: {:.3}°{} {:.3}°{}",
		       self.name, self.lat.abs(), lat_c, self.lon.abs(), lon_c)
	    }
	}

	#[derive(Debug)]
	struct Color {
	    red: u8,
	    green: u8,
	    blue: u8,
	}

	fn main() {
	    for city in [
		City { name: "Dublin", lat: 53.347778, lon: -6.259722 },
		City { name: "Oslo", lat: 59.95, lon: 10.75 },
		City { name: "Vancouver", lat: 49.25, lon: -123.1 },
	    ].iter() {
		println!("{}", *city);
	    }
	    for color in [
		Color { red: 128, green: 255, blue: 90 },
		Color { red: 0, green: 3, blue: 254 },
		Color { red: 0, green: 0, blue: 0 },
	    ].iter() {
		// Switch this to use {} once you've added an implementation
		// for fmt::Display
		println!("{:?}", *color);
	    }
	}
	```
* You can view a [full list of formatting traits](https://doc.rust-lang.org/std/fmt/#formatting-traits) and their argument types in the `std::fmt` documentation.
# References
* https://doc.rust-lang.org/stable/rust-by-example/hello.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/comment.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/print.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/print/print_debug.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/print/print_display.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/print/print_display/testcase_list.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/print/fmt.html
