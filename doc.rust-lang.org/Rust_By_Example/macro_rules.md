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
# References
* https://doc.rust-lang.org/stable/rust-by-example/macros.html
* https://doc.rust-lang.org/stable/rust-by-example/macros/syntax.html
