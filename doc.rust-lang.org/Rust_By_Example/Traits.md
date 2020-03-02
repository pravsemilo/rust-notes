# Traits
* A `trait` is a collection of methods defined for an unknown type : `Self`.
* They can access methods declared in the same trait.
* Traits can be implemented for any data type.
```rust
struct Sheep { naked: bool, name: &'static str }

trait Animal {
	// Static method signature; `Self` refers to the implementor type.
	fn new(name: &'static str) -> Self;

	// Instance method signatures; these will return a string.
	fn name(&self) -> &'static str;
	fn noise(&self) -> &'static str;

	// Traits can provide default method definitions.
	fn talk(&self) {
		println!("{} says {}", self.name(), self.noise());
	}
}

impl Sheep {
	fn is_naked(&self) -> bool {
		self.naked
	}

	fn shear(&mut self) {
		if self.is_naked() {
			// Implementor methods can use the implementor's trait methods.
			println!("{} is already naked...", self.name());
		} else {
			println!("{} gets a haircut!", self.name);

			self.naked = true;
		}
	}
}

// Implement the `Animal` trait for `Sheep`.
impl Animal for Sheep {
	// `Self` is the implementor type: `Sheep`.
	fn new(name: &'static str) -> Sheep {
		Sheep { name: name, naked: false }
	}

	fn name(&self) -> &'static str {
		self.name
	}

	fn noise(&self) -> &'static str {
		if self.is_naked() {
			"baaaaah?"
		} else {
			"baaaaah!"
		}
	}

	// Default trait methods can be overridden.
	fn talk(&self) {
		// For example, we can add some quiet contemplation.
		println!("{} pauses briefly... {}", self.name, self.noise());
		}
}

fn main() {
	// Type annotation is necessary in this case.
	let mut dolly: Sheep = Animal::new("Dolly");
	// TODO ^ Try removing the type annotations.

	dolly.talk();
	dolly.shear();
	dolly.talk();
}
```
## Derive
* The compiler is capable of providing basic implementations for some traits via the `#[derive]` [attribute](https://github.com/pravsemilo/rust-notes/blob/master/doc.rust-lang.org/Rust_By_Example/Attributes.md).
* These traits can be manually implemented if a more complex behavior is required.
* List of derivable traits :
	* Comparison traits : [`Eq`](https://doc.rust-lang.org/std/cmp/trait.Eq.html), [`PartialEq`](https://doc.rust-lang.org/std/cmp/trait.PartialEq.html), [`Ord`](https://doc.rust-lang.org/std/cmp/trait.Ord.html), [`PartialOrd`](https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html).
	* [`Clone`](https://doc.rust-lang.org/std/clone/trait.Clone.html), to create `T` from `&T` via a copy.
	* [`Copy`](https://doc.rust-lang.org/core/marker/trait.Copy.html), to give a type 'copy semantics' instead of 'move semantics'.
	* [`Hash`](https://doc.rust-lang.org/std/hash/trait.Hash.html), to compute a hash from `&T`.
	* [`Default`](https://doc.rust-lang.org/std/default/trait.Default.html), to create an empty instance of a data type.
	* [`Debug`](https://doc.rust-lang.org/std/fmt/trait.Debug.html), to format a value using the `{:?}` formatter.
```rust
// `Centimeters`, a tuple struct that can be compared
#[derive(PartialEq, PartialOrd)]
struct Centimeters(f64);

// `Inches`, a tuple struct that can be printed
#[derive(Debug)]
struct Inches(i32);

impl Inches {
	fn to_centimeters(&self) -> Centimeters {
		let &Inches(inches) = self;

		Centimeters(inches as f64 * 2.54)
	}
}

// `Seconds`, a tuple struct with no additional attributes
struct Seconds(i32);

fn main() {
	let _one_second = Seconds(1);

	// Error: `Seconds` can't be printed; it doesn't implement the `Debug` trait
	//println!("One second looks like: {:?}", _one_second);
	// TODO ^ Try uncommenting this line

	// Error: `Seconds` can't be compared; it doesn't implement the `PartialEq` trait
	//let _this_is_true = (_one_second == _one_second);
	// TODO ^ Try uncommenting this line

	let foot = Inches(12);

	println!("One foot equals {:?}", foot);

	let meter = Centimeters(100.0);

	let cmp =
		if foot.to_centimeters() < meter {
			"smaller"
		} else {
			"bigger"
		};

	println!("One foot is {} than one meter.", cmp);
}
```
## Returning Traits with `dyn`
* Rust compiler needs to know how much space every function's return type requires.
	* This implies all functions have to return a concrete type.
	* This is an issue for functions returning `traits` since their size may vary.
	* So instead of returning a `trait`, functions can return a `Box` which contains the `trait`.
* A `Box` is just a reference to some memory in the heap.
	* Reference has a statically known size.
	* Compiler can guarantee it points to a `trait` reference allocated in heap. 
* Rust tries to be as explicit as possible whenever it allocates memory on heap.
	* For a function returning a pointer-to-trait-on-heap, you need to write the return type with `dyn`.
		* Example : `Box <dyn Trait_Type>`.
```rust
struct Sheep {}
struct Cow {}

trait Animal {
	// Instance method signature
	fn noise(&self) -> &'static str;
}

// Implement the `Animal` trait for `Sheep`.
impl Animal for Sheep {
	fn noise(&self) -> &'static str {
		"baaaaah!"
	}
}

// Implement the `Animal` trait for `Cow`.
impl Animal for Cow {
	fn noise(&self) -> &'static str {
		"moooooo!"
	}
}

// Returns some struct that implements Animal, but we don't know which one at compile time.
fn random_animal(random_number: f64) -> Box<dyn Animal> {
	if random_number < 0.5 {
		Box::new(Sheep {})
	} else {
		Box::new(Cow {})
	}
}

fn main() {
	let random_number = 0.234;
	let animal = random_animal(random_number);
	println!("You've randomly chosen an animal, and it says {}", animal.noise());
}
```
## Operator Overloading
* In Rust, many of the operators can be overloaded via traits.
	* Some operators can be used to accomplish different tasks based on their input arguments.
	* This is possible because operators are syntactic sugar for method calls.
	* A list of traits that overload operators can be found in [core::ops](https://doc.rust-lang.org/core/ops/).
```rust
use std::ops;

struct Foo;
struct Bar;

#[derive(Debug)]
struct FooBar;

#[derive(Debug)]
struct BarFoo;

// The `std::ops::Add` trait is used to specify the functionality of `+`.
// Here, we make `Add<Bar>` - the trait for addition with a RHS of type `Bar`.
// The following block implements the operation: Foo + Bar = FooBar
impl ops::Add<Bar> for Foo {
	type Output = FooBar;

	fn add(self, _rhs: Bar) -> FooBar {
		println!("> Foo.add(Bar) was called");

		FooBar
	}
}

// By reversing the types, we end up implementing non-commutative addition.
// Here, we make `Add<Foo>` - the trait for addition with a RHS of type `Foo`.
// This block implements the operation: Bar + Foo = BarFoo
impl ops::Add<Foo> for Bar {
	type Output = BarFoo;

	fn add(self, _rhs: Foo) -> BarFoo {
		println!("> Bar.add(Foo) was called");

		BarFoo
	}
}

fn main() {
	println!("Foo + Bar = {:?}", Foo + Bar);
	println!("Bar + Foo = {:?}", Bar + Foo);
}
```
## Drop
* The [`Drop`](https://doc.rust-lang.org/std/ops/trait.Drop.html) has only one method : `drop`.
	* This method is automatically called when an object goes out of scope.
	* Used to free resources.
	* Example : `Box`, `Vec`, `String`, `File`, `Process`.
```rust
struct Droppable {
	name: &'static str,
}

// This trivial implementation of `drop` adds a print to console.
impl Drop for Droppable {
	fn drop(&mut self) {
		println!("> Dropping {}", self.name);
	}
}

fn main() {
	let _a = Droppable { name: "a" };

	// block A
	{
		let _b = Droppable { name: "b" };

		// block B
		{
			let _c = Droppable { name: "c" };
			let _d = Droppable { name: "d" };

			println!("Exiting block B");
		}
		println!("Just exited block B");

		println!("Exiting block A");
	}
	println!("Just exited block A");

	// Variable can be manually dropped using the `drop` function
	drop(_a);
	// TODO ^ Try commenting this line

	println!("end of the main function");

	// `_a` *won't* be `drop`ed again here, because it already has been
	// (manually) `drop`ed
}
```
## Iterators
* The [`Iterator`](https://doc.rust-lang.org/core/iter/trait.Iterator.html) trait is used to implement iterators over collections.
	* Requires only a method to be defined : `next`.
```rust
struct Fibonacci {
	curr: u32,
	next: u32,
}

// Implement `Iterator` for `Fibonacci`.
// The `Iterator` trait only requires a method to be defined for the `next` element.
impl Iterator for Fibonacci {
	type Item = u32;

	// Here, we define the sequence using `.curr` and `.next`.
	// The return type is `Option<T>`:
		// * When the `Iterator` is finished, `None` is returned.
		// * Otherwise, the next value is wrapped in `Some` and returned.
	fn next(&mut self) -> Option<u32> {
		let new_next = self.curr + self.next;

		self.curr = self.next;
		self.next = new_next;

		// Since there's no endpoint to a Fibonacci sequence, the `Iterator`
		// will never return `None`, and `Some` is always returned.
		Some(self.curr)
	}
}

// Returns a Fibonacci sequence generator
fn fibonacci() -> Fibonacci {
	Fibonacci { curr: 0, next: 1 }
}

fn main() {
	// `0..3` is an `Iterator` that generates: 0, 1, and 2.
	let mut sequence = 0..3;

	println!("Four consecutive `next` calls on 0..3");
	println!("> {:?}", sequence.next());
	println!("> {:?}", sequence.next());
	println!("> {:?}", sequence.next());
	println!("> {:?}", sequence.next());

	// `for` works through an `Iterator` until it returns `None`.
	// Each `Some` value is unwrapped and bound to a variable (here, `i`).
	println!("Iterate through 0..3 using `for`");
	for i in 0..3 {
		println!("> {}", i);
	}

	// The `take(n)` method reduces an `Iterator` to its first `n` terms.
	println!("The first four terms of the Fibonacci sequence are: ");
	for i in fibonacci().take(4) {
		println!("> {}", i);
	}

	// The `skip(n)` method shortens an `Iterator` by dropping its first `n` terms.
	println!("The next four terms of the Fibonacci sequence are: ");
	for i in fibonacci().skip(4).take(4) {
		println!("> {}", i);
	}

	let array = [1u32, 3, 3, 7];

	// The `iter` method produces an `Iterator` over an array/slice.
	println!("Iterate the following array {:?}", &array);
	for i in array.iter() {
		println!("> {}", i);
	}
}
```
## impl Trait
* If your function returns a type that implements a `trait`, you can write the return type as `-> impl MyTrait`.
```rust
use std::iter;
use std::vec::IntoIter;

// This function combines two `Vec<i32>` and returns an iterator over it.
// Look how complicated its return type is!
fn combine_vecs_explicit_return_type<'a>(
	v: Vec<i32>,
	u: Vec<i32>,
) -> iter::Cycle<iter::Chain<IntoIter<i32>, IntoIter<i32>>> {
	v.into_iter().chain(u.into_iter()).cycle()
}

// This is the exact same function, but its return type uses `impl Trait`.
// Look how much simpler it is!
fn combine_vecs<'a>(
	v: Vec<i32>,
	u: Vec<i32>,
) -> impl Iterator<Item=i32> {
	v.into_iter().chain(u.into_iter()).cycle()
}
```
* Some Rust types can't be written out.
	* Example : Closures has its own unnamed concrete type.
```rust
// Returns a function that adds `y` to its input
fn make_adder_function(y: i32) -> impl Fn(i32) -> i32 {
	let closure = move |x: i32| { x + y };
	closure
}

fn main() {
	let plus_one = make_adder_function(1);
	assert_eq!(plus_one(2), 3);
}
```
* You can also use `impl Trait` to return an iterator that uses `map` or `filter` closures.
```rust
fn double_positives<'a>(numbers: &'a Vec<i32>) -> impl Iterator<Item = i32> + 'a {
	numbers
		.iter()
		.filter(|x| x > &&0)
		.map(|x| x * 2)
}
```
## Clone
* When dealing with resources, the default behavior is to transfer them during assignments and function calls.
* Sometimes we need to make a copy of the resource.
* The [`Clone`](	https://doc.rust-lang.org/std/clone/trait.Clone.html) helps us to do this.
```rust
// A unit struct without resources
#[derive(Debug, Clone, Copy)]
struct Nil;

// A tuple struct with resources that implements the `Clone` trait
#[derive(Clone, Debug)]
struct Pair(Box<i32>, Box<i32>);

fn main() {
	// Instantiate `Nil`
	let nil = Nil;
	// Copy `Nil`, there are no resources to move
	let copied_nil = nil;

	// Both `Nil`s can be used independently
	println!("original: {:?}", nil);
	println!("copy: {:?}", copied_nil);

	// Instantiate `Pair`
	let pair = Pair(Box::new(1), Box::new(2));
	println!("original: {:?}", pair);

	// Copy `pair` into `moved_pair`, moves resources
	let moved_pair = pair;
	println!("copy: {:?}", moved_pair);

	// Error! `pair` has lost its resources
	//println!("original: {:?}", pair);
	// TODO ^ Try uncommenting this line

	// Clone `moved_pair` into `cloned_pair` (resources are included)
	let cloned_pair = moved_pair.clone();
	// Drop the original pair using std::mem::drop
	drop(moved_pair);

	// Error! `moved_pair` has been dropped
	//println!("copy: {:?}", moved_pair);
	// TODO ^ Try uncommenting this line

	// The result from .clone() can still be used!
	println!("clone: {:?}", cloned_pair);
}
```
## Supertraits
## Disambiguating overlapping traits
# References
* https://doc.rust-lang.org/stable/rust-by-example/trait.html
* https://doc.rust-lang.org/stable/rust-by-example/trait/derive.html
* https://doc.rust-lang.org/stable/rust-by-example/trait/dyn.html
* https://doc.rust-lang.org/stable/rust-by-example/trait/ops.html
* https://doc.rust-lang.org/stable/rust-by-example/trait/drop.html
* https://doc.rust-lang.org/stable/rust-by-example/trait/iter.html
* https://doc.rust-lang.org/stable/rust-by-example/trait/impl_trait.html
* https://doc.rust-lang.org/stable/rust-by-example/trait/clone.html
* https://doc.rust-lang.org/stable/rust-by-example/traits/supertraits.html
* https://doc.rust-lang.org/stable/rust-by-example/traits/disambiguating.html
