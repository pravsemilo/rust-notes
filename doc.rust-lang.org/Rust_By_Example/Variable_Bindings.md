# Variable Bindings
* Rust provides type safety via static typing.
* Variable bindings can be type annotated when declared.
	* However compiler will be able to infer the type of the variable.
* Values (like literals) can be bound to variables, using the `let` binding.
```rust
fn main() {
	let an_integer = 1u32;
	let a_boolean = true;
	let unit = ();

	// copy `an_integer` into `copied_integer`
	let copied_integer = an_integer;

	println!("An integer: {:?}", copied_integer);
	println!("A boolean: {:?}", a_boolean);
	println!("Meet the unit value: {:?}", unit);

	// The compiler warns about unused variable bindings; these warnings can
	// be silenced by prefixing the variable name with an underscore
	let _unused_variable = 3u32;

	let noisy_unused_variable = 2u32;
	// FIXME ^ Prefix with an underscore to suppress the warning
}
```
## Mutability
* Variable bindings are immutable by default, but this can be overridden using the `mut` modifier.
* The compiler will throw a detailed diagnostic about mutability errors.
```rust
fn main() {
	let _immutable_binding = 1;
	let mut mutable_binding = 1;

	println!("Before mutation: {}", mutable_binding);

	// Ok
	mutable_binding += 1;

	println!("After mutation: {}", mutable_binding);

	// Error!
	_immutable_binding += 1;
	// FIXME ^ Comment out this line
}
```
## Scope and Shadowing
* Variable bindings have a scope and are constrained to live in a _block_.
	* A block is a collection of statements enclosed by braces `{}`.
* Variable shadowing is allowed.
```rust
fn main() {
	// This binding lives in the main function
	let long_lived_binding = 1;

	// This is a block, and has a smaller scope than the main function
	{
		// This binding only exists in this block
		let short_lived_binding = 2;

		println!("inner short: {}", short_lived_binding);

		// This binding *shadows* the outer one
		let long_lived_binding = 5_f32;

		println!("inner long: {}", long_lived_binding);
	}
	// End of the block

	// Error! `short_lived_binding` doesn't exist in this scope
	println!("outer short: {}", short_lived_binding);
	// FIXME ^ Comment out this line

	println!("outer long: {}", long_lived_binding);

	// This binding also *shadows* the previous binding
	let long_lived_binding = 'a';

	println!("outer long: {}", long_lived_binding);
}
```
## Declare First
* It's possible to declare variable bindings first and initialize them later.
* However this is not used much as it leads to the use of uninitialized variables.
```rust
fn main() {
	// Declare a variable binding
	let a_binding;

	{
		let x = 2;

		// Initialize the binding
		a_binding = x * x;
	}

	println!("a binding: {}", a_binding);

	let another_binding;

	// Error! Use of uninitialized binding
	println!("another binding: {}", another_binding);
	// FIXME ^ Comment out this line

	another_binding = 1;

	println!("another binding: {}", another_binding);
}
```
* Compiler forbids use of uninitialized variables, as this would lead to undefined behavior.
# References
* https://doc.rust-lang.org/stable/rust-by-example/variable_bindings.html
* https://doc.rust-lang.org/stable/rust-by-example/variable_bindings/mut.html
* https://doc.rust-lang.org/stable/rust-by-example/variable_bindings/scope.html
* https://doc.rust-lang.org/stable/rust-by-example/variable_bindings/declare.html
