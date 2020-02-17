# Expressions
* A Rust program is made up of a series of statements.
```rust
fn main() {
	// statement
	// statement
	// statement
}
```
* There are a few kinds of statements in Rust. The most common two are declaring a variable binding and using a `;` with an expression.
```rust
fn main() {
	// variable binding
	let x = 5;

	// expression;
	x;
	x + 1;
	15;
}
```
* Blocks are expressions too. This mean that they can be used as values in assignments.
	* The last expression in the block will be assigned to the place expression such as a local variable.
	* If the last expression of the block ends with a `;`, the return value will be `()`.
```rust
fn main() {
	let x = 5u32;

	let y = {
		let x_squared = x * x;
		let x_cube = x_squared * x;

		// This expression will be assigned to `y`
		x_cube + x_squared + x
	};

	let z = {
		// The semicolon suppresses this expression and `()` is assigned to `z`
		2 * x;
	};

	println!("x is {:?}", x);
	println!("y is {:?}", y);
	println!("z is {:?}", z);
}
```
# References
* https://doc.rust-lang.org/stable/rust-by-example/expression.html
