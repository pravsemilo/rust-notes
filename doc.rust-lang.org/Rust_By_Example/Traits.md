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
## Returning Traits with dyn
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
