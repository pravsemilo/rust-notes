# Cargo
* `cargo` is the official Rust package management tool.
* Features :
	* Dependency management and integration with [crates.io](https://crates.io/)
	* Awareness of unit tests
	* Awareness of benchmarks
## Dependencies
* To create a new Rust project :
```bash
# A binary
cargo new foo

# OR A library
cargo new --lib foo
```
* Above command results in the following directory structure :
```
foo
├── Cargo.toml
└── src
    └── main.rs
```
	* `main.rs` is the root source file for the project.
	* `Cargo.toml` is the `cargo` config file.
```rust
[package]
name = "foo"
version = "0.1.0"
authors = ["mark"]

[dependencies]
```
# References
* https://doc.rust-lang.org/stable/rust-by-example/cargo.html
* https://doc.rust-lang.org/stable/rust-by-example/cargo/deps.html
