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
	* The `name` field under `[package]` determines the name of the project.
		* This is used by `crates.io` when published.
		* It is also the name of the output binary when you compile.
	* The `version` field is the crate version number.
	* The `authors` field is a list of authors used when publishing the crate.
	* The `[dependencies]` section lets you add dependencies for your project.
```toml
[package]
name = "foo"
version = "0.1.0"
authors = ["mark"]

[dependencies]
```
* To add a dependency to your program, you can add it under `[dependencies]` section in `Cargo.toml` and in `main.rs` you can import it as `extern crate`.
```toml
[package]
name = "foo"
version = "0.1.0"
authors = ["mark"]

[dependencies]
clap = "2.27.1" # from crates.io
rand = { git = "https://github.com/rust-lang-nursery/rand" } # from online repo
bar = { path = "../bar" } # from a path in the local filesystem
```
* To build our project, you can execute `cargo build` anywhere in the project directory including subdirectories.
* You can do `cargo run` to build and run.
* These commands will resolve all dependencies, download crates and build everything including your crate.
# References
* https://doc.rust-lang.org/stable/rust-by-example/cargo.html
* https://doc.rust-lang.org/stable/rust-by-example/cargo/deps.html
