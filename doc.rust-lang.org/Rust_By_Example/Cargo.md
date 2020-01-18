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
## Conventions
* The default binary name in `main`.
* You can add addtional binaries by placing them in a `bin/` directory :
```
foo
├── Cargo.toml
└── src
    ├── main.rs
    └── bin
        └── my_other_bin.rs
```
* To tell `cargo` to compile / run this binary, we pass the `--bin my_other_bin` flag.
## Tests
* Organizationally we can place unit tests in the modules they test and integration test in their own `tests/` directory :
	* Each file in `tests` is a separate integration test.
```foo
├── Cargo.toml
├── src
│   └── main.rs
└── tests
    ├── my_test.rs
    └── my_other_test.rs
```
* To run tests :
```bash
$ cargo test
```
* To run tests whose name matches a pattern :
```bash
$ cargo test test_foo
```
* Cargo may run multiple tests concurrently.
## Build Scripts
* Cargo will look for a `build.rs` in the project directory by default.
* To add a build script, you can specify it in `Cargo.toml` :
```toml
[package]
...
build = "build.rs"
```
* __How to use a build script__
	* Build script is simply another Rust file that will be compiled and invoked prior to compiling anything else in the package.
	* It can be used to fulfill any pre-requisites of your crate.
	* Cargo provides the script with inputs via environment variables [specified here](https://doc.rust-lang.org/cargo/reference/environment-variables.html#environment-variables-cargo-sets-for-build-scripts).
	* The script provides output via stdout.
		* All lines printed are written to `target/debug/build/<pkg>/output`.
	* Lines prefixed with `cargo:` will be interpreted by Cargo directly and hence can be used to define parameters for the package's compilation.
# References
* https://doc.rust-lang.org/stable/rust-by-example/cargo.html
* https://doc.rust-lang.org/stable/rust-by-example/cargo/deps.html
* https://doc.rust-lang.org/stable/rust-by-example/cargo/conventions.html
* https://doc.rust-lang.org/stable/rust-by-example/cargo/test.html
* https://doc.rust-lang.org/stable/rust-by-example/cargo/build_scripts.html
