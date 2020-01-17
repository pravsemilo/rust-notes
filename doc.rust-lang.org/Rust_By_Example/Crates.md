# Crates
* A crate is a compilation unit in Rust.
* Whenever `rustc some_file.rs` is called, `some_file.rs` is treated as the _crate file_.
	* If `some_file.rs` has `mod` declarations in it, then the contents of the module files would be inserted in places where the `mod` declarations in the crate file are found, _before_ running the compiler over it.
	* Modules do _not_ get compiled individually, only crates get compiled.
* Crate can be compiled into a binary or into a library.
	* By default, `rustc` will produce a binary from a crate.
	* This can be overridden by passing the `--crate-type=lib` flag to `rustc`.
## Library
* Libraries get prefixed with `lib` and by default they get named after their crate file.
* This name can be overridden using the [`crate_name` attribute](https://github.com/pravsemilo/rust-notes/blob/master/doc.rust-lang.org/Rust_By_Example/Attributes.md#crates).
```rust
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
```bash
$ rustc --crate-type=lib rary.rs
$ ls lib*
library.rlib
```
# References
* https://doc.rust-lang.org/stable/rust-by-example/crates.html
* https://doc.rust-lang.org/stable/rust-by-example/crates/lib.html
