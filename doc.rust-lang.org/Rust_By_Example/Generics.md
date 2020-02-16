# Generics
* _Generics_ are used to generalize types and functionalities to broader cases.
* Helps in reducing code duplication.
* Syntax is rather complex.
* Requires great care to specify over which types a generic types is considered valid.
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
# References
* https://doc.rust-lang.org/stable/rust-by-example/generics.html
