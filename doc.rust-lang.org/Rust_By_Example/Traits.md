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
## Drop
## Iterators
## impl Trait
## Clone
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
