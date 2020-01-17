# Crates
* A crate is a compilation unit in Rust.
* Whenever `rustc some_file.rs` is called, `some_file.rs` is treated as the _crate file_.
	* If `some_file.rs` has `mod` declarations in it, then the contents of the module files would be inserted in places where the `mod` declarations in the crate file are found, _before_ running the compiler over it.
	* Modules do _not_ get compiled individually, only crates get compiled.
* Crate can be compiled into a binary or into a library.
	* By default, `rustc` will produce a binary from a crate.
	* This can be overridden by passing the `--crate-type` flag to `rustc`.
# References
* https://doc.rust-lang.org/stable/rust-by-example/crates.html
