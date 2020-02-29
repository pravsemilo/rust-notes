# Generics
* _Generics_ are used to generalize types and functionalities to broader cases.
* Helps in reducing code duplication.
* Syntax is rather complex.
* Requires great care to specify over which types a generic type is considered valid.
* Type Parameters
	* Simplest and common use of generics.
	* Specified by angle brackets and upper camel case.
```rust
// A concrete type `A`.
struct A;

// In defining the type `Single`, the first use of `A` is not preceded by `<A>`.
// Therefore, `Single` is a concrete type, and `A` is defined as above.
struct Single(A);
//            ^ Here is `Single`s first use of the type `A`.
// Here, `<T>` precedes the first use of `T`, so `SingleGen` is a generic type.
// Because the type parameter `T` is generic, it could be anything, including
// the concrete type `A` defined at the top.
struct SingleGen<T>(T);

fn main() {
	// `Single` is concrete and explicitly takes `A`.
	let _s = Single(A);

	// Create a variable `_char` of type `SingleGen<char>`
	// and give it the value `SingleGen('a')`.
	// Here, `SingleGen` has a type parameter explicitly specified.
	let _char: SingleGen<char> = SingleGen('a');

	// `SingleGen` can also have a type parameter implicitly specified:
	let _t = SingleGen(A); // Uses `A` defined at the top.
	let _i32 = SingleGen(6); // Uses `i32`.
	let _char = SingleGen('a'); // Uses `char`.
}
```
## Functions
```rust
struct A;		// Concrete type `A`.
struct S(A);		// Concrete type `S`.
struct SGen<T>(T);	// Generic type `SGen`.

// The following functions all take ownership of the variable passed into
// them and immediately go out of scope, freeing the variable.

// Define a function `reg_fn` that takes an argument `_s` of type `S`.
// This has no `<T>` so this is not a generic function.
fn reg_fn(_s: S) {}

// Define a function `gen_spec_t` that takes an argument `_s` of type `SGen<T>`.
// It has been explicitly given the type parameter `A`, but because `A` has not
// been specified as a generic type parameter for `gen_spec_t`, it is not generic.
fn gen_spec_t(_s: SGen<A>) {}

// Define a function `gen_spec_i32` that takes an argument `_s` of type `SGen<i32>`.
// It has been explicitly given the type parameter `i32`, which is a specific type.
// Because `i32` is not a generic type, this function is also not generic.
fn gen_spec_i32(_s: SGen<i32>) {}

// Define a function `generic` that takes an argument `_s` of type `SGen<T>`.
// Because `SGen<T>` is preceded by `<T>`, this function is generic over `T`.
fn generic<T>(_s: SGen<T>) {}

fn main() {
	// Using the non-generic functions
	reg_fn(S(A));		// Concrete type.
	gen_spec_t(SGen(A));	// Implicitly specified type parameter `A`.
	gen_spec_i32(SGen(6));	// Implicitly specified type parameter `i32`.

	// Explicitly specified type parameter `char` to `generic()`.
	generic::<char>(SGen('a'));

	// Implicitly specified type parameter `char` to `generic()`.
	generic(SGen('c'));
}
```
## Implementation
```rust
struct S; // Concrete type `S`
struct GenericVal<T>(T); // Generic type `GenericVal`

// impl of GenericVal where we explicitly specify type parameters:
impl GenericVal<f32> {} // Specify `f32`
impl GenericVal<S> {} // Specify `S` as defined above

// `<T>` Must precede the type to remain generic
impl<T> GenericVal<T> {}
}
```
```rust
struct Val {
	val: f64,
}

struct GenVal<T> {
	gen_val: T,
}

// impl of Val
impl Val {
	fn value(&self) -> &f64 {
		&self.val
	}
}

// impl of GenVal for a generic type `T`
impl<T> GenVal<T> {
	fn value(&self) -> &T {
		&self.gen_val
	}
}

fn main() {
	let x = Val { val: 3.0 };
	let y = GenVal { gen_val: 3i32 };

	println!("{}, {}", x.value(), y.value());
}
```
## Traits
```rust
// Non-copyable types.
struct Empty;
struct Null;

// A trait generic over `T`.
trait DoubleDrop<T> {
	// Define a method on the caller type which takes an
	// additional single parameter `T` and does nothing with it.
	fn double_drop(self, _: T);
}

// Implement `DoubleDrop<T>` for any generic parameter `T` and
// caller `U`.
impl<T, U> DoubleDrop<T> for U {
	// This method takes ownership of both passed arguments,
	// deallocating both.
	fn double_drop(self, _: T) {}
}

fn main() {
	let empty = Empty;
	let null = Null;

	// Deallocate `empty` and `null`.
	empty.double_drop(null);

	//empty;
	//null;
	// ^ TODO: Try uncommenting these lines.
}
```
## Bounds
* Type parameters use traits as _bounds_ to stipulate what functionality a type implements.
```rust
// Define a function `printer` that takes a generic type `T` which
// must implement trait `Display`.
fn printer<T: Display>(t: T) {
	println!("{}", t);
}
```
* Bounding restricts the generic to types that conform to the bounds.
```rust
struct S<T: Display>(T);

// Error! `Vec<T>` does not implement `Display`. This
// specialization will fail.
let s = S(vec![1]);
```
* Generic instances are allowed to access the methods of traits specified in the bounds.
```rust
// A trait which implements the print marker: `{:?}`.
use std::fmt::Debug;

trait HasArea {
	fn area(&self) -> f64;
}

impl HasArea for Rectangle {
	fn area(&self) -> f64 { self.length * self.height }
}

#[derive(Debug)]
struct Rectangle { length: f64, height: f64 }
#[allow(dead_code)]
struct Triangle { length: f64, height: f64 }

// The generic `T` must implement `Debug`. Regardless
// of the type, this will work properly.
fn print_debug<T: Debug>(t: &T) {
	println!("{:?}", t);
}

// `T` must implement `HasArea`. Any type which meets
// the bound can access `HasArea`'s function `area`.
fn area<T: HasArea>(t: &T) -> f64 { t.area() }

fn main() {
	let rectangle = Rectangle { length: 3.0, height: 4.0 };
	let _triangle = Triangle { length: 3.0, height: 4.0 };

	print_debug(&rectangle);
	println!("Area: {}", area(&rectangle));

	//print_debug(&_triangle);
	//println!("Area: {}", area(&_triangle));
	// ^ TODO: Try uncommenting these.
	// | Error: Does not implement either `Debug` or `HasArea`.
}
```
* [where](https://github.com/pravsemilo/rust-notes/blob/master/doc.rust-lang.org/Rust_By_Example/Generics.md#where-clauses) can also be used to apply expressive bounds.
### Testcase : empty bounds
* Even if a `trait` doesn't include any functionality, you can still use it as a bound.
* `Eq` and `Ord` are examples of such `traits` from the `std` library.
```rust
struct Cardinal;
struct BlueJay;
struct Turkey;

trait Red {}
trait Blue {}

impl Red for Cardinal {}
impl Blue for BlueJay {}

// These functions are only valid for types which implement these
// traits. The fact that the traits are empty is irrelevant.
fn red<T: Red>(_: &T) -> &'static str { "red" }
fn blue<T: Blue>(_: &T) -> &'static str { "blue" }

fn main() {
	let cardinal = Cardinal;
	let blue_jay = BlueJay;
	let _turkey = Turkey;

	// `red()` won't work on a blue jay nor vice versa
	// because of the bounds.
	println!("A cardinal is {}", red(&cardinal));
	println!("A blue jay is {}", blue(&blue_jay));
	//println!("A turkey is {}", red(&_turkey));
	// ^ TODO: Try uncommenting this line.
}
```
## Multiple bounds
* Multiple bounds can be applied with a `+`.
```rust
use std::fmt::{Debug, Display};

fn compare_prints<T: Debug + Display>(t: &T) {
	println!("Debug: `{:?}`", t);
	println!("Display: `{}`", t);
}

fn compare_types<T: Debug, U: Debug>(t: &T, u: &U) {
	println!("t: `{:?}`", t);
	println!("u: `{:?}`", u);
}

fn main() {
	let string = "words";
	let array = [1, 2, 3];
	let vec = vec![1, 2, 3];

	compare_prints(&string);
	//compare_prints(&array);
	// TODO ^ Try uncommenting this.

	compare_types(&array, &vec);
}
```
## Where clauses
* A bound can also be expressed using a `where` clause immedidately before the opening `{`, rather at the type's first mention.
* `where` clauses can apply bounds to arbitrary types, rather than just to type parameters.
* Examples
	* When specifying generics types and bounds separately is clearer.
	```rust
	impl <A: TraitB + TraitC, D: TraitE + TraitF> MyTrait<A, D> for YourType {}

	// Expressing bounds with a `where` clause
	impl <A, D> MyTrait<A, D> for YourType where
		A: TraitB + TraitC,
		D: TraitE + TraitF {}
	```
	* When using a `where` clause is more expressive than the normal syntax.
	```rust
	use std::fmt::Debug;

	trait PrintInOption {
		fn print_in_option(self);
	}

	// Because we would otherwise have to express this as `T: Debug` or
	// use another method of indirect approach, this requires a `where` clause:
	impl<T> PrintInOption for T where
		Option<T>: Debug {
			// We want `Option<T>: Debug` as our bound because that is what's
			// being printed. Doing otherwise would be using the wrong bound.
			fn print_in_option(self) {
				println!("{:?}", Some(self));
			}
	}

	fn main() {
		let vec = vec![1, 2, 3];

		vec.print_in_option();
	}
	```
## New Type Idiom
* `newType` idiom gives compile time guarantees that the right type of value is supplied to a program.
```rust
struct Years(i64);

struct Days(i64);

impl Years {
	pub fn to_days(&self) -> Days {
		Days(self.0 * 365)
	}
}


impl Days {
	/// truncates partial years
	pub fn to_years(&self) -> Years {
		Years(self.0 / 365)
	}
}

fn old_enough(age: &Years) -> bool {
	age.0 >= 18
}

fn main() {
	let age = Years(5);
	let age_days = age.to_days();
	println!("Old enough {}", old_enough(&age));
	println!("Old enough {}", old_enough(&age_days.to_years()));
	//println!("Old enough {}", old_enough(&age_days));
}
```
```rust
struct Years(i64);

fn main() {
	let years = Years(42);
	let years_as_primitive: i64 = years.0;
}
```
## Associated items
* "Associated items" refers to a set of rules pertaining to [item](https://doc.rust-lang.org/reference/items.html)s of various types.
* It is an extension to `trait` generics and allows `traits` to internally define new items.
* One such item is called an _associated type_, providing simpler usage patterns when the `trait` is generic over its container type.
### The Problem
* A `trait` that is generic over its container type has type specification requirements. Users of the `trait` _must_ specify all of its generic types.
```rust
struct Container(i32, i32);

// A trait which checks if 2 items are stored inside of container.
// Also retrieves first or last value.
trait Contains<A, B> {
	fn contains(&self, _: &A, _: &B) -> bool; // Explicitly requires `A` and `B`.
	fn first(&self) -> i32; // Doesn't explicitly require `A` or `B`.
	fn last(&self) -> i32;	// Doesn't explicitly require `A` or `B`.
}

impl Contains<i32, i32> for Container {
	// True if the numbers stored are equal.
	fn contains(&self, number_1: &i32, number_2: &i32) -> bool {
		(&self.0 == number_1) && (&self.1 == number_2)
	}

	// Grab the first number.
	fn first(&self) -> i32 { self.0 }

	// Grab the last number.
	fn last(&self) -> i32 { self.1 }
}

// `C` contains `A` and `B`. In light of that, having to express `A` and
// `B` again is a nuisance.
fn difference<A, B, C>(container: &C) -> i32 where
	C: Contains<A, B> {
	container.last() - container.first()
}

fn main() {
	let number_1 = 3;
	let number_2 = 10;

	let container = Container(number_1, number_2);

	println!("Does container contain {} and {}: {}",
		&number_1, &number_2,
		container.contains(&number_1, &number_2));
	println!("First number: {}", container.first());
	println!("Last number: {}", container.last());

	println!("The difference is: {}", difference(&container));
}
```
### Associated types
* Improves code readability by moving inner types locally into a trait as _output_ types.
```rust
// `A` and `B` are defined in the trait via the `type` keyword.
// (Note: `type` in this context is different from `type` when used for
// aliases).
trait Contains {
	type A;
	type B;

	// Updated syntax to refer to these new types generically.
	fn contains(&self, &Self::A, &Self::B) -> bool;
}
```
* Functions that use the `trait` are no longer required to pass the type parameters.
```rust
// Without using associated types
fn difference<A, B, C>(container: &C) -> i32 where
	C: Contains<A, B> { ... }

// Using associated types
fn difference<C: Contains>(container: &C) -> i32 { ... }
```
```rust
struct Container(i32, i32);

// A trait which checks if 2 items are stored inside of container.
// Also retrieves first or last value.
trait Contains {
	// Define generic types here which methods will be able to utilize.
	type A;
	type B;

	fn contains(&self, _: &Self::A, _: &Self::B) -> bool;
	fn first(&self) -> i32;
	fn last(&self) -> i32;
}

impl Contains for Container {
	// Specify what types `A` and `B` are. If the `input` type
	// is `Container(i32, i32)`, the `output` types are determined
	// as `i32` and `i32`.
	type A = i32;
	type B = i32;

	// `&Self::A` and `&Self::B` are also valid here.
	fn contains(&self, number_1: &i32, number_2: &i32) -> bool {
		(&self.0 == number_1) && (&self.1 == number_2)
	}
	// Grab the first number.
	fn first(&self) -> i32 { self.0 }

	// Grab the last number.
	fn last(&self) -> i32 { self.1 }
}

fn difference<C: Contains>(container: &C) -> i32 {
	container.last() - container.first()
}

fn main() {
	let number_1 = 3;
	let number_2 = 10;

	let container = Container(number_1, number_2);

	println!("Does container contain {} and {}: {}",
		&number_1, &number_2,
		container.contains(&number_1, &number_2));
	println!("First number: {}", container.first());
	println!("Last number: {}", container.last());

	println!("The difference is: {}", difference(&container));
}
```
## Phantom type parameters
* A phantom type parameter is one that doesn't show up at runtime, but it checked statically and only at compile time.
* Data types can use extra generic type parameters to act as markers or to perform type checking at compile time.
* These extra parameters hold no storage values and have no runtime behavior.
```rust
use std::marker::PhantomData;

// A phantom tuple struct which is generic over `A` with hidden parameter `B`.
#[derive(PartialEq)] // Allow equality test for this type.
struct PhantomTuple<A, B>(A,PhantomData<B>);

// A phantom type struct which is generic over `A` with hidden parameter `B`.
#[derive(PartialEq)] // Allow equality test for this type.
struct PhantomStruct<A, B> { first: A, phantom: PhantomData<B> }

// Note: Storage is allocated for generic type `A`, but not for `B`.
// Therefore, `B` cannot be used in computations.

fn main() {
	// Here, `f32` and `f64` are the hidden parameters.
	// PhantomTuple type specified as `<char, f32>`.
	let _tuple1: PhantomTuple<char, f32> = PhantomTuple('Q', PhantomData);
	// PhantomTuple type specified as `<char, f64>`.
	let _tuple2: PhantomTuple<char, f64> = PhantomTuple('Q', PhantomData);

	// Type specified as `<char, f32>`.
	let _struct1: PhantomStruct<char, f32> = PhantomStruct {
		first: 'Q',
		phantom: PhantomData,
	};
	// Type specified as `<char, f64>`.
	let _struct2: PhantomStruct<char, f64> = PhantomStruct {
		first: 'Q',
		phantom: PhantomData,
	};

	// Compile-time Error! Type mismatch so these cannot be compared:
	//println!("_tuple1 == _tuple2 yields: {}",
	//	_tuple1 == _tuple2);

	// Compile-time Error! Type mismatch so these cannot be compared:
	//println!("_struct1 == _struct2 yields: {}",
	//	_struct1 == _struct2);
}
```
# References
* https://doc.rust-lang.org/stable/rust-by-example/generics.html
* https://doc.rust-lang.org/stable/rust-by-example/generics/gen_fn.html
* https://doc.rust-lang.org/stable/rust-by-example/generics/impl.html
* https://doc.rust-lang.org/stable/rust-by-example/generics/gen_trait.html
* https://doc.rust-lang.org/stable/rust-by-example/generics/bounds.html
* https://doc.rust-lang.org/stable/rust-by-example/generics/bounds/testcase_empty.html
* https://doc.rust-lang.org/stable/rust-by-example/generics/multi_bounds.html
* https://doc.rust-lang.org/stable/rust-by-example/generics/where.html
* https://doc.rust-lang.org/stable/rust-by-example/generics/new_types.html
* https://doc.rust-lang.org/stable/rust-by-example/generics/assoc_items.html
* https://doc.rust-lang.org/stable/rust-by-example/generics/assoc_items/the_problem.html
* https://doc.rust-lang.org/stable/rust-by-example/generics/assoc_items/types.html
* https://doc.rust-lang.org/stable/rust-by-example/generics/phantom.html
