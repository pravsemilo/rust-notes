# Flow of Control
## if / else
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
## loop
* Rust provides a `loop` keyword to indicate an infinite loop.
* The `break` statement can be used to exit a loop at any time.
* `continue` statement can be used to skip the rest of the iteration and start a new one.
```rust
fn main() {
    let mut count = 0u32;

    println!("Let's count until infinity!");

    // Infinite loop
    loop {
        count += 1;

        if count == 3 {
            println!("three");

            // Skip the rest of this iteration
            continue;
        }

        println!("{}", count);

        if count == 5 {
            println!("OK, that's enough");

            // Exit this loop
            break;
        }
    }
}
```
### Nesting and labels
* It is possible to `break` or `continue` outer loops when dealing with nested loops.
* The loop must be annotated with some `'label` and the label must be passed to the `break / continue` statement.
```rust
#![allow(unreachable_code)]

fn main() {
    'outer: loop {
        println!("Entered the outer loop");

        'inner: loop {
            println!("Entered the inner loop");

            // This would break only the inner loop
            //break;

            // This breaks the outer loop
            break 'outer;
        }

        println!("This point will never be reached");
    }

    println!("Exited the outer loop");
}
```
### Returning from loops
* To return a value, you need to put the return value after the `break`. and it will be returned by the `loop` expression.
```rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    assert_eq!(result, 20);
}
```
## while
```rust
fn main() {
    // A counter variable
    let mut n = 1;

    // Loop while `n` is less than 101
    while n < 101 {
        if n % 15 == 0 {
            println!("fizzbuzz");
        } else if n % 3 == 0 {
            println!("fizz");
        } else if n % 5 == 0 {
            println!("buzz");
        } else {
            println!("{}", n);
        }

        // Increment counter
        n += 1;
    }
}
```
## for loops
### for and range
* `for in` construct can be used to iterate through an `Iterator`.
* One of the easiest ways to create an iterator is to use the range notation `a..b`. This yields values from `a` (inclusive) to `b` (exclusive) in steps of one.
```rust
fn main() {
    // `n` will take the values: 1, 2, ..., 100 in each iteration
    for n in 1..101 {
        if n % 15 == 0 {
            println!("fizzbuzz");
        } else if n % 3 == 0 {
            println!("fizz");
        } else if n % 5 == 0 {
            println!("buzz");
        } else {
            println!("{}", n);
        }
    }
}
```
* Use `a..=b` for a range that is inclusive on both ends.
### for and iterators
* As discussed in the [Iterator](https://github.com/pravsemilo/rust-notes/blob/master/doc.rust-lang.org/Rust_By_Example/Traits.md#iterators) trait, if not specified, the `for` loop will apply the `into_iter` function on the provided collection, to convert the collection into an iterator.
* This is not the only way to covert a collection into an iterator. The other functions are `iter` and `iter_mut`.
	* `iter`
		* Borrows each element from the collection through each iteration.
		* Collection is not modified and can be used after the loop.
	```rust
	fn main() {
	    let names = vec!["Bob", "Frank", "Ferris"];

	    for name in names.iter() {
		match name {
		    &"Ferris" => println!("There is a rustacean among us!"),
		    _ => println!("Hello {}", name),
		}
	    }
	}
	```
	* `into_iter`
		* Consumes the collection so that on each iteration exact data is provided.
		* Once the iteration is finished, the collection is no longer available for reuse.
	```rust
	fn main() {
	    let names = vec!["Bob", "Frank", "Ferris"];

	    for name in names.into_iter() {
		match name {
		    "Ferris" => println!("There is a rustacean among us!"),
		    _ => println!("Hello {}", name),
		}
	    }
	}
	```
	* `iter_mut`
		* Mutably borrows each element from the collection, allowing for the collection to be modified in place.
	```rust
	fn main() {
	    let mut names = vec!["Bob", "Frank", "Ferris"];

	    for name in names.iter_mut() {
		*name = match name {
		    &mut "Ferris" => "There is a rustacean among us!",
		    _ => "Hello",
		}
	    }

	    println!("names: {:?}", names);
	}
	```
# References
* https://doc.rust-lang.org/stable/rust-by-example/flow_control.html
* https://doc.rust-lang.org/stable/rust-by-example/flow_control/if_else.html
* https://doc.rust-lang.org/stable/rust-by-example/flow_control/loop.html
* https://doc.rust-lang.org/stable/rust-by-example/flow_control/loop/nested.html
* https://doc.rust-lang.org/stable/rust-by-example/flow_control/loop/return.html
* https://doc.rust-lang.org/stable/rust-by-example/flow_control/while.html
* https://doc.rust-lang.org/stable/rust-by-example/flow_control/for.html
