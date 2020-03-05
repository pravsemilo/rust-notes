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
* Data can be immutably borrowed any number of times.
* While immutably borrowed, the original data can't be mutably borrowed.
* Only _one_ mutable borrow is allowed at a time.
* The original data can be borrowed again only _after_ the mutable reference has been used for the last time.
```rust
struct Point { x: i32, y: i32, z: i32 }

fn main() {
	let mut point = Point { x: 0, y: 0, z: 0 };

	let borrowed_point = &point;
	let another_borrow = &point;

	// Data can be accessed via the references and the original owner
	println!("Point has coordinates: ({}, {}, {})",
		borrowed_point.x, another_borrow.y, point.z);

	// Error! Can't borrow `point` as mutable because it's currently
	// borrowed as immutable.
	// let mutable_borrow = &mut point;
	// TODO ^ Try uncommenting this line

	// The borrowed values are used again here
	println!("Point has coordinates: ({}, {}, {})",
		borrowed_point.x, another_borrow.y, point.z);

	// The immutable references are no longer used for the rest of the code so
	// it is possible to reborrow with a mutable reference.
	let mutable_borrow = &mut point;

	// Change data via mutable reference
	mutable_borrow.x = 5;
	mutable_borrow.y = 2;
	mutable_borrow.z = 1;

	// Error! Can't borrow `point` as immutable because it's currently
	// borrowed as mutable.
	// let y = &point.y;
	// TODO ^ Try uncommenting this line

	// Error! Can't print because `println!` takes an immutable reference.
	// println!("Point Z coordinate is {}", point.z);
	// TODO ^ Try uncommenting this line

	// Ok! Mutable references can be passed as immutable to `println!`
	println!("Point has coordinates: ({}, {}, {})",
		mutable_borrow.x, mutable_borrow.y, mutable_borrow.z);

	// The mutable reference is no longer used for the rest of the code so it
	// is possible to reborrow
	let new_borrowed_point = &point;
	println!("Point now has coordinates: ({}, {}, {})",
		new_borrowed_point.x, new_borrowed_point.y, new_borrowed_point.z);
}
```
### The ref pattern
* When doing pattern matching or destructuring via the `let` binding, the `ref` keyword can be used to take references to the fields of a struct / tuple.
```rust
#[derive(Clone, Copy)]
struct Point { x: i32, y: i32 }

fn main() {
	let c = 'Q';

	// A `ref` borrow on the left side of an assignment is equivalent to
	// an `&` borrow on the right side.
	let ref ref_c1 = c;
	let ref_c2 = &c;

	println!("ref_c1 equals ref_c2: {}", *ref_c1 == *ref_c2);

	let point = Point { x: 0, y: 0 };

	// `ref` is also valid when destructuring a struct.
	let _copy_of_x = {
		// `ref_to_x` is a reference to the `x` field of `point`.
		let Point { x: ref ref_to_x, y: _ } = point;

		// Return a copy of the `x` field of `point`.
		*ref_to_x
	};

	// A mutable copy of `point`
	let mut mutable_point = point;

	{
		// `ref` can be paired with `mut` to take mutable references.
		let Point { x: _, y: ref mut mut_ref_to_y } = mutable_point;

		// Mutate the `y` field of `mutable_point` via a mutable reference.
		*mut_ref_to_y = 1;
	}

	println!("point is ({}, {})", point.x, point.y);
	println!("mutable_point is ({}, {})", mutable_point.x, mutable_point.y);

	// A mutable tuple that includes a pointer
	let mut mutable_tuple = (Box::new(5u32), 3u32);

	{
		// Destructure `mutable_tuple` to change the value of `last`.
		let (_, ref mut last) = mutable_tuple;
		*last = 2u32;
	}

	println!("tuple is {:?}", mutable_tuple);
}
```
## Lifetimes
* A _lifetime_ is a contruct the compiler (or more specifically, its _borrow checker_) uses to ensure all borrows are valid.
* A variables lifetime begins when it is created and end when it is destroyed.
* Lifetime and scopes are often referred to together, they are not the same.
```rust
// Lifetimes are annotated below with lines denoting the creation
// and destruction of each variable.
// `i` has the longest lifetime because its scope entirely encloses
// both `borrow1` and `borrow2`. The duration of `borrow1` compared
// to `borrow2` is irrelevant since they are disjoint.
fn main() {
	let i = 3; // Lifetime for `i` starts. ─────────────────────────────────┐
	//									│
	{ //									│
		let borrow1 = &i; // `borrow1` lifetime starts. ────────┐	│
		//							│	│
		println!("borrow1: {}", borrow1); //			│	│
	} // `borrow1 ends. ────────────────────────────────────────────┘	│
	//									│
	//									│
	{ //									│
		let borrow2 = &i; // `borrow2` lifetime starts. ────────┐	│
		//							│	│
		println!("borrow2: {}", borrow2); //			│	│
	} // `borrow2` ends. ───────────────────────────────────────────┘	│
	//									│
}	// Lifetime ends. ──────────────────────────────────────────────────────┘
```
### Explicit annotation
* In cases, where lifetimes are not [elided](https://github.com/pravsemilo/rust-notes/blob/master/doc.rust-lang.org/Rust_By_Example/Scoping_rules.md#elision), Rust requires explicit annotations to determine the lifetime of a reference.
	* Elision implicitly annotates lifetimes and so is different.
	* The syntax uses an apostrophe.
```rust
foo<'a>
// `foo` has a lifetime parameter `'a`
```
* Similar to [closures](https://github.com/pravsemilo/rust-notes/blob/master/doc.rust-lang.org/Rust_By_Example/Functions.md#type-anonymity), using lifetime requires generics.
	* Explicit annotation of a type has the form `&'a T` where `'a` has already been introduced.
* In case of multiple lifetimes, the lifetimes cannot exceed that of either lifetimes.
```rust
foo<'a, 'b>
// `foo` has lifetime parameters `'a` and `'b`
```
```rust
// `print_refs` takes two references to `i32` which have different
// lifetimes `'a` and `'b`. These two lifetimes must both be at
// least as long as the function `print_refs`.
fn print_refs<'a, 'b>(x: &'a i32, y: &'b i32) {
	println!("x is {} and y is {}", x, y);
}

// A function which takes no arguments, but has a lifetime parameter `'a`.
fn failed_borrow<'a>() {
	let _x = 12;

	// ERROR: `_x` does not live long enough
	//let y: &'a i32 = &_x;
	// Attempting to use the lifetime `'a` as an explicit type annotation
	// inside the function will fail because the lifetime of `&_x` is shorter
	// than that of `y`. A short lifetime cannot be coerced into a longer one.
}

fn main() {
	// Create variables to be borrowed below.
	let (four, nine) = (4, 9);

	// Borrows (`&`) of both variables are passed into the function.
	print_refs(&four, &nine);
	// Any input which is borrowed must outlive the borrower.
	// In other words, the lifetime of `four` and `nine` must
	// be longer than that of `print_refs`.

	failed_borrow();
	// `failed_borrow` contains no references to force `'a` to be
	// longer than the lifetime of the function, but `'a` is longer.
	// Because the lifetime is never constrained, it defaults to `'static`.
}
```
### Functions
* Ignoring [elision](https://github.com/pravsemilo/rust-notes/blob/master/doc.rust-lang.org/Rust_By_Example/Scoping_rules.md#elision), function signatures with lifetimes have a few constraints.
	* Any reference _must_ have an annotated lifetime.
	* Any reference being returned _must_ have the same lifetime as an input or be `static`.
* Also note that returning references without input is banned if it would result in returning references to invalid data.
```rust
// One input reference with lifetime `'a` which must live
// at least as long as the function.
fn print_one<'a>(x: &'a i32) {
	println!("`print_one`: x is {}", x);
}

// Mutable references are possible with lifetimes as well.
fn add_one<'a>(x: &'a mut i32) {
	*x += 1;
}

// Multiple elements with different lifetimes. In this case, it
// would be fine for both to have the same lifetime `'a`, but
// in more complex cases, different lifetimes may be required.
fn print_multi<'a, 'b>(x: &'a i32, y: &'b i32) {
	println!("`print_multi`: x is {}, y is {}", x, y);
}

// Returning references that have been passed in is acceptable.
// However, the correct lifetime must be returned.
fn pass_x<'a, 'b>(x: &'a i32, _: &'b i32) -> &'a i32 { x }

//fn invalid_output<'a>() -> &'a String { &String::from("foo") }
// The above is invalid: `'a` must live longer than the function.
// Here, `&String::from("foo")` would create a `String`, followed by a
// reference. Then the data is dropped upon exiting the scope, leaving
// a reference to invalid data to be returned.

fn main() {
	let x = 7;
	let y = 9;

	print_one(&x);
	print_multi(&x, &y);

	let z = pass_x(&x, &y);
	print_one(z);

	let mut t = 3;
	add_one(&mut t);
	print_one(&t);
}
```
### Methods
* Methods are annotated similarly to functions.
```rust
struct Owner(i32);

impl Owner {
	// Annotate lifetimes as in a standalone function.
	fn add_one<'a>(&'a mut self) { self.0 += 1; }
	fn print<'a>(&'a self) {
		println!("`print`: {}", self.0);
	}
}

fn main() {
	let mut owner = Owner(18);

	owner.add_one();
	owner.print();
}
```
### Structs
* Structs are annotated similarly to functions.
```rust
// A type `Borrowed` which houses a reference to an
// `i32`. The reference to `i32` must outlive `Borrowed`.
#[derive(Debug)]
struct Borrowed<'a>(&'a i32);

// Similarly, both references here must outlive this structure.
#[derive(Debug)]
struct NamedBorrowed<'a> {
	x: &'a i32,
	y: &'a i32,
}

// An enum which is either an `i32` or a reference to one.
#[derive(Debug)]
enum Either<'a> {
	Num(i32),
	Ref(&'a i32),
}

fn main() {
	let x = 18;
	let y = 15;

	let single = Borrowed(&x);
	let double = NamedBorrowed { x: &x, y: &y };
	let reference = Either::Ref(&x);
	let number = Either::Num(y);

	println!("x is borrowed in {:?}", single);
	println!("x and y are borrowed in {:?}", double);
	println!("x is borrowed in {:?}", reference);
	println!("y is *not* borrowed in {:?}", number);
}
```
### Traits
* Annotation of lifetimes in trait methods are similar to functions.
* Note that `impl` may have annotation of lifetimes too.
```rust
// A struct with annotation of lifetimes.
#[derive(Debug)]
 struct Borrowed<'a> {
	x: &'a i32,
 }

// Annotate lifetimes to impl.
impl<'a> Default for Borrowed<'a> {
	fn default() -> Self {
		Self {
			x: &10,
		}
	}
}

fn main() {
	let b: Borrowed = Default::default();
	println!("b is {:?}", b);
}
```
### Bounds
* Just like generic types can be bounded, lifetimes (themselves generic) use bounds as well.
* The `:` character has a different meaning here, but `+` is the same.
	* `T: 'a` : _All_ references in `T` must outlive lifetime `'a`.
	* `T: Trait + 'a` : Type `T` must implement trait `Trait` and _all_ references in `T` must outlive `'a`.
```rust
use std::fmt::Debug; // Trait to bound with.

#[derive(Debug)]
struct Ref<'a, T: 'a>(&'a T);
// `Ref` contains a reference to a generic type `T` that has
// an unknown lifetime `'a`. `T` is bounded such that any
// *references* in `T` must outlive `'a`. Additionally, the lifetime
// of `Ref` may not exceed `'a`.

// A generic function which prints using the `Debug` trait.
fn print<T>(t: T) where
	T: Debug {
	println!("`print`: t is {:?}", t);
}

// Here a reference to `T` is taken where `T` implements
// `Debug` and all *references* in `T` outlive `'a`. In
// addition, `'a` must outlive the function.
fn print_ref<'a, T>(t: &'a T) where
	T: Debug + 'a {
	println!("`print_ref`: t is {:?}", t);
}

fn main() {
	let x = 7;
	let ref_x = Ref(&x);

	print_ref(&ref_x);
	print(ref_x);
}
```
### Coercion
* A longer lifetime can be coerced into a shorter one so that it works inside a scope it normally wouldn't work in.
* This comes in the form of inferred coercion by the Rust compiler and also in the form of declaring a lifetime difference.
```rust
// Here, Rust infers a lifetime that is as short as possible.
// The two references are then coerced to that lifetime.
fn multiply<'a>(first: &'a i32, second: &'a i32) -> i32 {
	first * second
}

// `<'a: 'b, 'b>` reads as lifetime `'a` is at least as long as `'b`.
// Here, we take in an `&'a i32` and return a `&'b i32` as a result of coercion.
fn choose_first<'a: 'b, 'b>(first: &'a i32, _: &'b i32) -> &'b i32 {
	first
}

fn main() {
	let first = 2; // Longer lifetime

	{
		let second = 3; // Shorter lifetime

		println!("The product is {}", multiply(&first, &second));
		println!("{} is the first", choose_first(&first, &second));
	};
}
```
### Static
* A `'static` lifetime is the longest possible lifetime.
* It lasts for the lifetime of the running program.
* It can also be coerced to a shorter lifetime.
* There are two ways to make a variable with `'static` lifetime and both are stored in the read-only memory of the binary.
	* Make a constant with the `static` declaration.
	* Make a `string` literal which has type : `&'static str`.
```rust
// Make a constant with `'static` lifetime.
static NUM: i32 = 18;

// Returns a reference to `NUM` where its `'static`
// lifetime is coerced to that of the input argument.
fn coerce_static<'a>(_: &'a i32) -> &'a i32 {
	&NUM
}

fn main() {
	{
		// Make a `string` literal and print it:
		let static_string = "I'm in read-only memory";
		println!("static_string: {}", static_string);

		// When `static_string` goes out of scope, the reference
		// can no longer be used, but the data remains in the binary.
	}

	{
		// Make an integer to use for `coerce_static`:
		let lifetime_num = 9;

		// Coerce `NUM` to lifetime of `lifetime_num`:
		let coerced_static = coerce_static(&lifetime_num);

		println!("coerced_static: {}", coerced_static);
	}

	println!("NUM: {} stays accessible!", NUM);
}
```
### Elision
* Elision exists in Rust to save typing and improve readability.
* Some lifetime patterns are overwhelmingly common and hence the borrow checker will allow you to omit them.
* A comprehensive description of elision is given in [here](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html#lifetime-elision).
```rust
// `elided_input` and `annotated_input` essentially have identical signatures
// because the lifetime of `elided_input` is inferred by the compiler:
fn elided_input(x: &i32) {
	println!("`elided_input`: {}", x);
}

fn annotated_input<'a>(x: &'a i32) {
	println!("`annotated_input`: {}", x);
}

// Similarly, `elided_pass` and `annotated_pass` have identical signatures
// because the lifetime is added implicitly to `elided_pass`:
fn elided_pass(x: &i32) -> &i32 { x }

fn annotated_pass<'a>(x: &'a i32) -> &'a i32 { x }

fn main() {
	let x = 3;

	elided_input(&x);
	annotated_input(&x);

	println!("`elided_pass`: {}", elided_pass(&x));
	println!("`annotated_pass`: {}", annotated_pass(&x));
}
```
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
