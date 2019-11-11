# Flow of Control
## if/else
* Boolean condition doesn't need to be surrounded by parentheses.
* Each condition is followed by a block.
* `if-else` conditionals are expressions and all branches must return the same type.
```rust
fn main() {
    let n = 5;

    if n < 0 {
        print!("{} is negative", n);
    } else if n > 0 {
        print!("{} is positive", n);
    } else {
        print!("{} is zero", n);
    }

    let big_n =
        if n < 10 && n > -10 {
            println!(", and is a small number, increase ten-fold");

            // This expression returns an `i32`.
            10 * n
        } else {
            println!(", and is a big number, halve the number");

            // This expression must return an `i32` as well.
            n / 2
            // TODO ^ Try suppressing this expression with a semicolon.
        };
    //   ^ Don't forget to put a semicolon here! All `let` bindings need it.

    println!("{} -> {}", n, big_n);
}
```
# References
* https://doc.rust-lang.org/stable/rust-by-example/flow_control.html
* https://doc.rust-lang.org/stable/rust-by-example/flow_control/if_else.html
