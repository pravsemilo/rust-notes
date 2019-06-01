# Hello World
```rust
fn main() {
	println!("Hello World!");
}
```
* `println!` is a macro that prints next to the console.
* A binary can generated using Rust compiler `rustc`. It will produce a `hello` binary that can be executed.
```bash
$ rustc hello.rs
$ ./hello
```
## Comments
* *Regular Comments*
```rust
// Single line comments
/* Block comments */
```
* *Doc Comments*
```rust
/// Generate library docs for the following item.
//! Generate library docs for the enclosing item.
```
# References
* https://doc.rust-lang.org/stable/rust-by-example/hello.html
* https://doc.rust-lang.org/stable/rust-by-example/hello/comment.html
