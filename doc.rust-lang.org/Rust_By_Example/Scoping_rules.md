# Scoping rules
* Scopes play an important part in ownership, borrowing and lifetimes.
* They indicate to compiler when borrows are valid, when resources can be freed and when variables are created or destroyed.
## RAII
* Variables in Rust not only hold data in stack, they also _own_ resources.
	* Example : `Box<T>` owns memory in the heap.
* Rust enforces RAII (Resource Acquisition Is Initialization).
	* Whenever object goes out of scope, its destructor is called and its owned resources are freed.
	* Shields against _resource leak_ bugs.
```rust
// raii.rs
fn create_box() {
	// Allocate an integer on the heap
	let _box1 = Box::new(3i32);

	// `_box1` is destroyed here, and memory gets freed
}

fn main() {
	// Allocate an integer on the heap
	let _box2 = Box::new(5i32);

	// A nested scope:
	{
		// Allocate an integer on the heap
		let _box3 = Box::new(4i32);

		// `_box3` is destroyed here, and memory gets freed
	}

	// Creating lots of boxes just for fun
	// There's no need to manually free memory!
	for _ in 0u32..1_000 {
		create_box();
	}

	// `_box2` is destroyed here, and memory gets freed
}
```
* __Destructor__
* Provided through the `[Drop](https://doc.rust-lang.org/std/ops/trait.Drop.html)` trait.
* Destructor is called when the resource goes out of scope.
* Required to be implemented only if you require its own destructor logic.
```rust
struct ToDrop;

impl Drop for ToDrop {
	fn drop(&mut self) {
		println!("ToDrop is being dropped");
	}
}

fn main() {
	let x = ToDrop;
	println!("Made a ToDrop!");
}
```
## Ownership and moves
### Mutability
## Borrowing
### Mutability
### Freezing
### Aliasing
### The ref pattern
## Lifetimes
### Explicit annotation
### Functions
### Methods
### Structs
### Traits
### Bounds
### Coercion
### Static
### Elision
# References
* https://doc.rust-lang.org/stable/rust-by-example/scope.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/raii.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/move.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/move/mut.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/borrow.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/borrow/mut.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/borrow/freeze.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/borrow/alias.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/borrow/ref.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/lifetime.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/lifetime/explicit.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/lifetime/fn.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/lifetime/methods.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/lifetime/struct.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/lifetime/trait.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/lifetime/lifetime_bounds.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/lifetime/lifetime_coercion.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/lifetime/static_lifetime.html
* https://doc.rust-lang.org/stable/rust-by-example/scope/lifetime/elision.html
