# Installing Rust
* You can try Rust online in the [Rust Playground](https://play.rust-lang.org/).
## Rustup
* Rust installer and version management tool.
## Cargo
* Rust build tool and package manager.
* Cargo does lots of things :
	* Builds your project.
	```bash
	$ cargo build
	```
	* Run your project.
	```bash
	$ cargo run
	```
	* Test your project.
	```bash
	$ cargo test
	```
	* Build documentation for your project.
	```bash
	$ cargo doc
	```
	* Publish a library to [crates.io](https://crates.io/).
	```bash
	$ cargo pubish
	```
## Other Tools
* Rust support is available in many editors.
* `rustfmt`
	* Code formatting tool.
	```bash
	$ rustup component add rustfmt
	```
* `clippy`
	* Linting tool.
	```bash
	$ rustup component add clippy
	```
# Generatng a new project
* To create a new project
```bash
$ cargo new hello-rust
```
* This will create a new directory called `hello-rust` with the following diretory structure.
	* `Cargo.toml` - Mainfest file for Rust. It's where you keep metadata for your project and its dependencies.
	* `src/main.rs` - Contains application code.
	```
	hello-rust
	|- Cargo.toml
	|- src
	  |- main.rs
	```
* To run the project.
```bash
$ cargo run
```
# Adding dependencies
* [crates.io](https://crates.io/) is the package registry for Rust.
* In Rust, we refer to packages as `crates`.
* To add a dependency, edit your `Cargo.toml`
```rust
[dependencies]
ferris-say = "0.1"
```
* To install the dependency :
```bash
$ cargo build
```
	* This command created a new file for us, `Cargo.lock`.
	* This file is a log of the exact version of dependencies we are using locally.
* To use this dependency, edit `main.rs` :
```rust
use ferris_says::say
```
# A small Rust application
* Edit `main.rs` :
```rust
use ferris_says::say; // from the previous step
use std::io::{stdout, BufWriter};

fn main() {
	let stdout = stdout();
	let out = b"Hello fellow Rustaceans!";
	let width = 24;
	let mut writer = BufWriter::new(stdout.lock());
	say(out, width, &mut writer).unwrap();
}
```
* To run the application :
```bash
$ cargo run
```
# References
* https://www.rust-lang.org/learn/get-started
