# Attributes
* Attribute is metadata applied to some module, crate or item.
* This metadata can be used to / for :
	* [conditional compilation of some code](https://github.com/pravsemilo/rust-notes/blob/master/doc.rust-lang.org/Rust_By_Example/Attributes.md#cfg)
	* [set crate name, version and type (binary or library)](https://github.com/pravsemilo/rust-notes/blob/master/doc.rust-lang.org/Rust_By_Example/Attributes.md#crates)
	* disable lints (warnings)
	* enable compiler features (macros, glob imports etc.)
	* link to a foreign library
	* mark functions as unit tests
	* mark functions that will be part of a benchmark
* Attributes applied to a whole crate : `#![crate_attribute]`.
* Attributes applied to a module or an item : `#[item_attribute]`.
* Syntax for attribute arguments :
	* `#[attribute = "value"]`
	* `#[attribute(key = "value")]`
	* `#[attribute(value)]`
* Attributes can have multiple values and can be separated over multiple lines.
```rust
#[attribute(value, value2)]


#[attribute(value, value2, value3,
	value4, value5)]
```
## `dead_code`
* The compiler provides a `dead_code` _lint_ that will warn about unused function.
* An _attribute_ can be used to disable the lint.
```rust
fn used_function() {}

// `#[allow(dead_code)]` is an attribute that disables the `dead_code` lint
#[allow(dead_code)]
fn unused_function() {}

fn noisy_unused_function() {}
// FIXME ^ Add an attribute to suppress the warning

fn main() {
	used_function();
}
```
## Crates
* The `crate_type` attribute can be used to tell the compiler whether a crate is a binary or a library (and even which type of library).
* The `crate_name` attribute can be used to set the name of the crate.
* It is important to note that both these attributes have __no__ effect when using Cargo.
```rust
// This crate is a library
#![crate_type = "lib"]
// The library is named "rary"
#![crate_name = "rary"]

pub fn public_function() {
	println!("called rary's `public_function()`");
}

fn private_function() {
	println!("called rary's `private_function()`");
}

pub fn indirect_access() {
	print!("called rary's `indirect_access()`, that\n> ");

	private_function();
}
```
* When `crate_type` attribute is used, we no longer need to pass the `--crate-type` flag to `rustc`.
```bash
$ rustc lib.rs
$ ls lib*
library.rlib
```
## `cfg`
* Conditional compilation is possible through two different operators :
	* `cfg` attribute : `#[cfg(...)]` in attribute position.
	* `cfg!` macro : `cfg!(...)` in boolean expressions.
```rust
// This function only gets compiled if the target OS is linux
#[cfg(target_os = "linux")]
fn are_you_on_linux() {
	println!("You are running linux!");
}

// And this function only gets compiled if the target OS is *not* linux
#[cfg(not(target_os = "linux"))]
fn are_you_on_linux() {
	println!("You are *not* running linux!");
}

fn main() {
	are_you_on_linux();

	println!("Are you sure?");
	if cfg!(target_os = "linux") {
		println!("Yes. It's definitely linux!");
	} else {
		println!("Yes. It's definitely *not* linux!");
	}
}
```
### Custom
* Some conditionals like `target_os` are implicitly provided by `rustc`.
* Custom conditionals must be passed to `rustc` using the `--cfg` flag.
```rust
#[cfg(some_condition)]
fn conditional_function() {
	println!("condition met!");
}

fn main() {
	conditional_function();
}
```
```bash
$ rustc --cfg some_condition custom.rs && ./custom
condition met!
```
# References
* https://doc.rust-lang.org/stable/rust-by-example/attribute.html
* https://doc.rust-lang.org/stable/rust-by-example/attribute/unused.html
* https://doc.rust-lang.org/stable/rust-by-example/attribute/crate.html
* https://doc.rust-lang.org/stable/rust-by-example/attribute/cfg.html
* https://doc.rust-lang.org/stable/rust-by-example/attribute/cfg/custom.html
