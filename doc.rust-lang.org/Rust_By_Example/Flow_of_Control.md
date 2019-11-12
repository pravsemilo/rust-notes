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
## match
* Rust provides pattern matching via the `match` keyword, which can be used like a C `switch`.
```rustfn main() {
    let number = 13;
    // TODO ^ Try different values for `number`

    println!("Tell me about {}", number);
    match number {
        // Match a single value
        1 => println!("One!"),
        // Match several values
        2 | 3 | 5 | 7 | 11 => println!("This is a prime"),
        // Match an inclusive range
        13...19 => println!("A teen"),
        // Handle the rest of cases
        _ => println!("Ain't special"),
    }

    let boolean = true;
    // Match is an expression too
    let binary = match boolean {
        // The arms of a match must cover all the possible values
        false => 0,
        true => 1,
        // TODO ^ Try commenting out one of these arms
    };

    println!("{} -> {}", boolean, binary);
}
```
### Destructuring
* A `match` block can destructure items in following ways :
	* [Destructuring Tuples]()
	* [Desctructuring Enums]()
	* [Destructuring Pointers]()
	* [Destructuing Structures]()
#### tuples
```rust
fn main() {
    let pair = (0, -2);
    // TODO ^ Try different values for `pair`

    println!("Tell me about {:?}", pair);
    // Match can be used to destructure a tuple
    match pair {
        // Destructure the second
        (0, y) => println!("First is `0` and `y` is `{:?}`", y),
        (x, 0) => println!("`x` is `{:?}` and last is `0`", x),
        _      => println!("It doesn't matter what they are"),
        // `_` means don't bind the value to a variable
    }
}
```
#### enums
```rust
// `allow` required to silence warnings because only
// one variant is used.
#[allow(dead_code)]
enum Color {
    // These 3 are specified solely by their name.
    Red,
    Blue,
    Green,
    // These likewise tie `u32` tuples to different names: color models.
    RGB(u32, u32, u32),
    HSV(u32, u32, u32),
    HSL(u32, u32, u32),
    CMY(u32, u32, u32),
    CMYK(u32, u32, u32, u32),
}

fn main() {
    let color = Color::RGB(122, 17, 40);
    // TODO ^ Try different variants for `color`

    println!("What color is it?");
    // An `enum` can be destructured using a `match`.
    match color {
        Color::Red   => println!("The color is Red!"),
        Color::Blue  => println!("The color is Blue!"),
        Color::Green => println!("The color is Green!"),
        Color::RGB(r, g, b) =>
            println!("Red: {}, green: {}, and blue: {}!", r, g, b),
        Color::HSV(h, s, v) =>
            println!("Hue: {}, saturation: {}, value: {}!", h, s, v),
        Color::HSL(h, s, l) =>
            println!("Hue: {}, saturation: {}, lightness: {}!", h, s, l),
        Color::CMY(c, m, y) =>
            println!("Cyan: {}, magenta: {}, yellow: {}!", c, m, y),
        Color::CMYK(c, m, y, k) =>
            println!("Cyan: {}, magenta: {}, yellow: {}, key (black): {}!",
                c, m, y, k),
        // Don't need another arm because all variants have been examined
    }
}
```
#### pointers / ref
* For pointers, destructurting and dereferencing, are different in rust and C.
	* Dereferencing uses `*`.
	* Destructuring uses `&`, `ref` and `ref mut`.
```rust
fn main() {
    // Assign a reference of type `i32`. The `&` signifies there
    // is a reference being assigned.
    let reference = &4;

    match reference {
        // If `reference` is pattern matched against `&val`, it results
        // in a comparison like:
        // `&i32`
        // `&val`
        // ^ We see that if the matching `&`s are dropped, then the `i32`
        // should be assigned to `val`.
        &val => println!("Got a value via destructuring: {:?}", val),
    }

    // To avoid the `&`, you dereference before matching.
    match *reference {
        val => println!("Got a value via dereferencing: {:?}", val),
    }

    // What if you don't start with a reference? `reference` was a `&`
    // because the right side was already a reference. This is not
    // a reference because the right side is not one.
    let _not_a_reference = 3;

    // Rust provides `ref` for exactly this purpose. It modifies the
    // assignment so that a reference is created for the element; this
    // reference is assigned.
    let ref _is_a_reference = 3;

    // Accordingly, by defining 2 values without references, references
    // can be retrieved via `ref` and `ref mut`.
    let value = 5;
    let mut mut_value = 6;

    // Use `ref` keyword to create a reference.
    match value {
        ref r => println!("Got a reference to a value: {:?}", r),
    }

    // Use `ref mut` similarly.
    match mut_value {
        ref mut m => {
            // Got a reference. Gotta dereference it before we can
            // add anything to it.
            *m += 10;
            println!("We added 10. `mut_value`: {:?}", m);
        },
    }
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
* https://doc.rust-lang.org/stable/rust-by-example/flow_control/match.html
* https://doc.rust-lang.org/stable/rust-by-example/flow_control/match/destructuring.html
* https://doc.rust-lang.org/stable/rust-by-example/flow_control/match/destructuring/destructure_tuple.html
* https://doc.rust-lang.org/stable/rust-by-example/flow_control/match/destructuring/destructure_enum.html
* https://doc.rust-lang.org/stable/rust-by-example/flow_control/match/destructuring/destructure_pointers.html