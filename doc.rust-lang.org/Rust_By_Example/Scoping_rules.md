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
* Mutability of data can be changed when ownership is transferred.
```rust
fn main() {
	let immutable_box = Box::new(5u32);

	println!("immutable_box contains {}", immutable_box);

	// Mutability error
	//*immutable_box = 4;

	// *Move* the box, changing the ownership (and mutability)
	let mut mutable_box = immutable_box;

	println!("mutable_box contains {}", mutable_box);

	// Modify the contents of the box
	*mutable_box = 4;

	println!("mutable_box now contains {}", mutable_box);
}
```
## Borrowing
* Used where we'd like to access data without taking ownership over it.
* Instead of passing objects by value (`T`), objects can be passed by reference (`&T`).
* The compiler statically guarantees (via its borrow checker) that references _always_ point to valiid objects.
* While reference to an object exist, the object cannot be destroyed.
```rust
// This function takes ownership of a box and destroys it
fn eat_box_i32(boxed_i32: Box<i32>) {
	println!("Destroying box that contains {}", boxed_i32);
}

// This function borrows an i32
fn borrow_i32(borrowed_i32: &i32) {
	println!("This int is: {}", borrowed_i32);
}

fn main() {
	// Create a boxed i32, and a stacked i32
	let boxed_i32 = Box::new(5_i32);
	let stacked_i32 = 6_i32;

	// Borrow the contents of the box. Ownership is not taken,
	// so the contents can be borrowed again.
	borrow_i32(&boxed_i32);
	borrow_i32(&stacked_i32);

	{
		// Take a reference to the data contained inside the box
		let _ref_to_i32: &i32 = &boxed_i32;

		// Error!
		// Can't destroy `boxed_i32` while the inner value is borrowed later in scope.
		eat_box_i32(boxed_i32);
		// FIXME ^ Comment out this line

		// Attempt to borrow `_ref_to_i32` after inner value is destroyed
		borrow_i32(_ref_to_i32);
		// `_ref_to_i32` goes out of scope and is no longer borrowed.
	}

	// `boxed_i32` can now give up ownership to `eat_box` and be destroyed
	eat_box_i32(boxed_i32);
}
```
### Mutability
* Mutable data can be mutably borrowed using `&mut T`.
	* This is called _mutable reference_.
	* Gives read / write access to the borrower.
* In contrast, `&T` borrows the data via an immutable reference.
	* The borrower can read the data but not modify it.
```rust
#[allow(dead_code)]
#[derive(Clone, Copy)]
struct Book {
	// `&'static str` is a reference to a string allocated in read only memory
	author: &'static str,
	title: &'static str,
	year: u32,
}

// This function takes a reference to a book
fn borrow_book(book: &Book) {
	println!("I immutably borrowed {} - {} edition", book.title, book.year);
}

// This function takes a reference to a mutable book and changes `year` to 2014
fn new_edition(book: &mut Book) {
	book.year = 2014;
	println!("I mutably borrowed {} - {} edition", book.title, book.year);
}

fn main() {
	// Create an immutable Book named `immutabook`
	let immutabook = Book {
		// string literals have type `&'static str`
		author: "Douglas Hofstadter",
		title: "Gödel, Escher, Bach",
		year: 1979,
	};

	// Create a mutable copy of `immutabook` and call it `mutabook`
	let mut mutabook = immutabook;

	// Immutably borrow an immutable object
	borrow_book(&immutabook);

	// Immutably borrow a mutable object
	borrow_book(&mutabook);

	// Borrow a mutable object as mutable
	new_edition(&mut mutabook);

	// Error! Cannot borrow an immutable object as mutable
	new_edition(&mut immutabook);
	// FIXME ^ Comment out this line
}
```
### Freezing
* When data is immutably borrowed, it also _freezes_.
* _Frozen_ data can't be modified via the original object until all references to it go out of scope.
```rust
fn main() {
	let mut _mutable_integer = 7i32;

	{
		// Borrow `_mutable_integer`
		let large_integer = &_mutable_integer;

		// Error! `_mutable_integer` is frozen in this scope
		_mutable_integer = 50;
		// FIXME ^ Comment out this line

		println!("Immutably borrowed {}", large_integer);

		// `large_integer` goes out of scope
	}

	// Ok! `_mutable_integer` is not frozen in this scope
	_mutable_integer = 3;
}
```
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
