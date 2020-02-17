# Conversion
* Rust addresses conversion between types by use of [traits](https://github.com/pravsemilo/rust-notes/blob/master/doc.rust-lang.org/Rust_By_Example/Traits.md).
* Generic conversions will use the [From](https://doc.rust-lang.org/std/convert/trait.From.html) and [Into](https://doc.rust-lang.org/std/convert/trait.Into.html) traits.
* However there are more specific ones for the more common cases, in particular when converting to and from `String`.
## `From` and `Into`
* [From](https://doc.rust-lang.org/std/convert/trait.From.html) and [Into](https://doc.rust-lang.org/std/convert/trait.Into.html) traits are inherently linked.
* If you are able to convert type A from type B, then it should be possible to convert type B to type A.
* __[From](https://doc.rust-lang.org/std/convert/trait.From.html)__
	* Allows for a type to define how to create itself from another type.
	* Provides a mechanism for converting between several types.
	* There are numerous implementations of this trait within the standard library for conversion of primitive and common types.
	* Example : We can easily convert a `str` into a `String`.
	```rust
	let my_str = "hello";
	let my_string = String::from(my_str);
	```
	* We can also define a conversion for our own type.
	```rust
	use std::convert::From;

	#[derive(Debug)]
	struct Number {
		value: i32,
	}

	impl From<i32> for Number {
		fn from(item: i32) -> Self {
			Number { value: item }
		}
	}

	fn main() {
		let num = Number::from(30);
		println!("My number is {:?}", num);
	}
	```
* __[Into](https://doc.rust-lang.org/std/convert/trait.Into.html)__
	* Reciprocal of the `From` trait.
	* If you have implemented the `From` trait for your type, you get the `Into` implementation for free.
	* Using the `Into` trait will require specification of the type to convert into as the compiler is unable to determine this most of the time.
	```rust
	use std::convert::From;

	#[derive(Debug)]
	struct Number {
		value: i32,
	}

	impl From<i32> for Number {
		fn from(item: i32) -> Self {
			Number { value: item }
		}
	}

	fn main() {
		let int = 5;
		// Try removing the type declaration
		let num: Number = int.into();
		println!("My number is {:?}", num);
	}
	```
## TryFrom and TryInto
* Similar to [`From and Into`](https://github.com/pravsemilo/rust-notes/blob/master/doc.rust-lang.org/Rust_By_Example/Conversion.md#from-and-into), [`TryFrom`](https://doc.rust-lang.org/std/convert/trait.TryFrom.html) and [`TryInto`](https://doc.rust-lang.org/std/convert/trait.TryInto.html) are generic traits for converting between types.
* Unlike `From` / `Into`, the `TryFrom` / `TryInto` traits are used for fallible conversions and return [`Result`](https://doc.rust-lang.org/std/result/enum.Result.html)s.
```rust
use std::convert::TryFrom;
use std::convert::TryInto;

#[derive(Debug, PartialEq)]
struct EvenNumber(i32);

impl TryFrom<i32> for EvenNumber {
	type Error = ();

	fn try_from(value: i32) -> Result<Self, Self::Error> {
		if value % 2 == 0 {
			Ok(EvenNumber(value))
		} else {
			Err(())
		}
	}
}

fn main() {
	// TryFrom

	assert_eq!(EvenNumber::try_from(8), Ok(EvenNumber(8)));
	assert_eq!(EvenNumber::try_from(5), Err(()));

	// TryInto

	let result: Result<EvenNumber, ()> = 8i32.try_into();
	assert_eq!(result, Ok(EvenNumber(8)));
	let result: Result<EvenNumber, ()> = 5i32.try_into();
	assert_eq!(result, Err(()));
}
```
## To and from Strings
* __Converting to String__
	* Implement the [ToString](https://doc.rust-lang.org/std/string/trait.ToString.html) trait to convert a given type to a `String`.
	* Instead of doing directly, you should implement the [fmt::Display](https://doc.rust-lang.org/std/fmt/trait.Display.html) trait.
		* Automagically provides the [ToString](https://doc.rust-lang.org/std/string/trait.ToString.html).
		* Allows printing the type.
	```rust
	use std::fmt;

	struct Circle {
		radius: i32
	}

	impl fmt::Display for Circle {
		fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
			write!(f, "Circle of radius {}", self.radius)
		}
	}

	fn main() {
		let circle = Circle { radius: 6 };
		println!("{}", circle.to_string());
	}
	```
* __Parsing a String__
	* The idiomatic approach is to use the [parse](https://doc.rust-lang.org/std/primitive.str.html#method.parse) function and provide the type to the function.
	* Type can be passed in following manner :
		* Without type inference.
		* Using the turbofish syntax.
	* This will convert the string into the type specified as long as the [FromStr](https://doc.rust-lang.org/std/str/trait.FromStr.html) trait is implemented for that type.
		* This is implemented for the types within the standard library.
		* For user defined types implement the [FromStr](https://doc.rust-lang.org/std/str/trait.FromStr.html) trait for that type.
	```rust
	fn main() {
		let parsed: i32 = "5".parse().unwrap();
		let turbo_parsed = "10".parse::<i32>().unwrap();

		let sum = parsed + turbo_parsed;
		println!("Sum: {:?}", sum);
	}
	```
# References
* https://doc.rust-lang.org/stable/rust-by-example/conversion.html
* https://doc.rust-lang.org/stable/rust-by-example/conversion/from_into.html
* https://doc.rust-lang.org/stable/rust-by-example/conversion/try_from_try_into.html
* https://doc.rust-lang.org/stable/rust-by-example/conversion/string.html
