# Variable Bindings
* Rust provides type safety via static typing.
* Variable bindings can be type annotated when declared.
	* However compiler will be able to infer the type of the variable.
* Values (like literals) can be bound to variables, using the `let` binding.
```rust
fn main() {
    let an_integer = 1u32;
    let a_boolean = true;
    let unit = ();

    // copy `an_integer` into `copied_integer`
    let copied_integer = an_integer;

    println!("An integer: {:?}", copied_integer);
    println!("A boolean: {:?}", a_boolean);
    println!("Meet the unit value: {:?}", unit);

    // The compiler warns about unused variable bindings; these warnings can
    // be silenced by prefixing the variable name with an underscore
    let _unused_variable = 3u32;

    let noisy_unused_variable = 2u32;
    // FIXME ^ Prefix with an underscore to suppress the warning
}
```
## Mutability
* Variable binding are immutable by default, but this can be overridden using the `mut` modifier.
* The compiler will throw a detailed diagnostic about mutability errors.
```rust
fn main() {
    let _immutable_binding = 1;
    let mut mutable_binding = 1;

    println!("Before mutation: {}", mutable_binding);

    // Ok
    mutable_binding += 1;

    println!("After mutation: {}", mutable_binding);

    // Error!
    _immutable_binding += 1;
    // FIXME ^ Comment out this line
}
```
# References
* https://doc.rust-lang.org/stable/rust-by-example/variable_bindings.html
* https://doc.rust-lang.org/stable/rust-by-example/variable_bindings/mut.html
