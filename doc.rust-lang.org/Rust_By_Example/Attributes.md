# Attributes
* Attribute is metadata applied to some module, crate or item.
* This metadata can be used to / for :
	* [conditional compilation of some comde](https://github.com/pravsemilo/rust-notes/blob/master/doc.rust-lang.org/Rust_By_Example/Attributes.md#cfg)
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
## Crates
## `cfg`
### Custom
# References
* https://doc.rust-lang.org/stable/rust-by-example/attribute.html
* https://doc.rust-lang.org/stable/rust-by-example/attribute/unused.html
* https://doc.rust-lang.org/stable/rust-by-example/attribute/crate.html
* https://doc.rust-lang.org/stable/rust-by-example/attribute/cfg.html
* https://doc.rust-lang.org/stable/rust-by-example/attribute/cfg/custom.html
