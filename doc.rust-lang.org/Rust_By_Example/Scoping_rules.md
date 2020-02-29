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
* Provided through the [`Drop`](https://doc.rust-lang.org/std/ops/trait.Drop.html) trait.
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
* Because variables are incharge of freeing their own resources, __resources can only have one owner__.
	* Prevents resources from being freed more than once.
	* Not all variables own resources. Example : [references](https://github.com/pravsemilo/rust-notes/blob/master/doc.rust-lang.org/Rust_By_Example/Flow_of_Control.md#pointers--ref)
* When doing assignments (`let x = y`) or passing function arguments by value (`foo(x)`), the _ownership_ of resources is transferred.
	* In Rust, this is known as a _move_.
	* After moving resources, the previous owner can no longer be used.
	* This avoids creating dangling pointers.
```rust
// This function takes ownership of the heap allocated memory
fn destroy_box(c: Box<i32>) {
	println!("Destroying a box that contains {}", c);

	// `c` is destroyed and the memory freed
}

fn main() {
	// _Stack_ allocated integer
	let x = 5u32;

	// *Copy* `x` into `y` - no resources are moved
	let y = x;

	// Both values can be independently used
	println!("x is {}, and y is {}", x, y);

	// `a` is a pointer to a _heap_ allocated integer
	let a = Box::new(5i32);

	println!("a contains: {}", a);

	// *Move* `a` into `b`
	let b = a;
	// The pointer address of `a` is copied (not the data) into `b`.
	// Both are now pointers to the same heap allocated data, but
	// `b` now owns it.

	// Error! `a` can no longer access the data, because it no longer owns the
	// heap memory
	//println!("a contains: {}", a);
	// TODO ^ Try uncommenting this line

	// This function takes ownership of the heap allocated memory from `b`
	destroy_box(b);

	// Since the heap memory has been freed at this point, this action would
	// result in dereferencing freed memory, but it's forbidden by the compiler
	// Error! Same reason as the previous Error
	//println!("b contains: {}", b);
	// TODO ^ Try uncommenting this line
}
```
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
