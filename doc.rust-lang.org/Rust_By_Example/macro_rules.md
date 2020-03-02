# macro_rules!
* Rust provides a powerful macro system that allows metaprogramming.
* Macros look like functions, except that their name ends with a`!`.
* Instead of generating a function call, they are expanded into the source code that gets compiled with the rest of the program.
* Unlike other languages, Rust macros are expanded into abstract syntax trees rather than string preprocessing.
* Macros are created using the `marco_rules!` macro.
```rust
// This is a simple macro named `say_hello`.
macro_rules! say_hello {
	// `()` indicates that the macro takes no argument.
	() => {
		// The macro will expand into the contents of this block.
		println!("Hello!");
	};
}

fn main() {
	// This call will expand into `println!("Hello");`
	say_hello!()
}
```
## Syntax
### Designators
* Arguments of a macro are prefixed with a `$` sign and type annotated with a _designator_.
```rust
macro_rules! create_function {
	// This macro takes an argument of designator `ident` and
	// creates a function named `$func_name`.
	// The `ident` designator is used for variable/function names.
	($func_name:ident) => {
		fn $func_name() {
			// The `stringify!` macro converts an `ident` into a string.
			println!("You called {:?}()",
				stringify!($func_name));
		}
	};
}

// Create functions named `foo` and `bar` with the above macro.
create_function!(foo);
create_function!(bar);

macro_rules! print_result {
	// This macro takes an expression of type `expr` and prints
	// it as a string along with its result.
	// The `expr` designator is used for expressions.
	($expression:expr) => {
		// `stringify!` will convert the expression *as it is* into a string.
		println!("{:?} = {:?}",
			 stringify!($expression),
			 $expression);
	};
}

fn main() {
	foo();
	bar();

	print_result!(1u32 + 1);

	// Recall that blocks are expressions too!
	print_result!({
		let x = 1u32;

		x * x + 2 * x - 1
	});
}
```
* Some of the available designators are :
	* `block`
	* `expr` is used for expressions.
	* `ident` is used for variable / function names.
	* `item`
	* `literal` is used for literal constants.
	* `pat` (Pattern)
	* `path`
	* `stmt` (Statement)
	* `tt` (Token Tree)
	* `ty` (Type)
	* `vis` (Visibility Qualifier)
* For a complete list of designators, see [here](https://doc.rust-lang.org/reference/macros-by-example.html).
### Overload
* Macros can be overloaded to accept different combinations of arguments.
```rust
// `test!` will compare `$left` and `$right`
// in different ways depending on how you invoke it:
macro_rules! test {
	// Arguments don't need to be separated by a comma.
	// Any template can be used!
	($left:expr; and $right:expr) => {
		println!("{:?} and {:?} is {:?}",
			stringify!($left),
			stringify!($right),
			$left && $right)
	};
	// ^ each arm must end with a semicolon.
	($left:expr; or $right:expr) => {
		println!("{:?} or {:?} is {:?}",
			stringify!($left),
			stringify!($right),
			$left || $right)
	};
}

fn main() {
	test!(1i32 + 1 == 2i32; and 2i32 * 2 == 4i32);
	test!(true; or false);
}
```
### Repeat
* `+` : May repeat at least once.
* `*` : May repeat zero or more times.
```rust
// `min!` will calculate the minimum of any number of arguments.
macro_rules! find_min {
	// Base case:
	($x:expr) => ($x);
	// `$x` followed by at least one `$y,`
	($x:expr, $($y:expr),+) => (
		// Call `find_min!` on the tail `$y`
		std::cmp::min($x, find_min!($($y),+))
	)
}

fn main() {
	println!("{}", find_min!(1u32));
	println!("{}", find_min!(1u32 + 2, 2u32));
	println!("{}", find_min!(5u32, 2u32 * 3, 4u32));
}
```
# References
* https://doc.rust-lang.org/stable/rust-by-example/macros.html
* https://doc.rust-lang.org/stable/rust-by-example/macros/syntax.html
* https://doc.rust-lang.org/stable/rust-by-example/macros/designators.html
* https://doc.rust-lang.org/stable/rust-by-example/macros/overload.html
* https://doc.rust-lang.org/stable/rust-by-example/macros/repeat.html
