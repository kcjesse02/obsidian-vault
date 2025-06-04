
- Variables are immutable by default
	- `let apples = 5` is unmutable
	- `let mut apples = 5` is mutable
You will get an error if you try to reassign an immutable variable
```
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:4:5
  |
2 |     let x = 5;
  |         -
  |         |
  |         first assignment to `x`
  |         help: consider making this binding mutable: `mut x`
3 |     println!("The value of x is: {x}");
4 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable

For more information about this error, try `rustc --explain E0384`.
error: could not compile `variables` due to previous error
```

- Constants are always immutable (cant use the `mut` keyword)
	- `const HOURS_IN_A_DAY = 24`

## Shadowing
- you can declare a variable with the same name as a previous variable
- it will use scope rules
```rust
fn main() {
    let x = 5;
    let x = x + 1;
    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {x}");
    }
    println!("The value of x is: {x}");
}
```
- The value of x in the inner scope is: 12
- The value of x is: 6
### Why Shadowing?
- allows for variables of the same name but different types
```rust
    let spaces = "   "; //String type
    let spaces = spaces.len(); //interger type
```

# Data Types
#### Integers
- there is very specific typing for ints based on the size and signedness:

| Length  | Signed  | Unsigned |
| ------- | ------- | -------- |
| 8-bit   | `i8`    | `u8`     |
| 16-bit  | `i16`   | `u16`    |
| 32-bit  | `i32`   | `u32`    |
| 64-bit  | `i64`   | `u64`    |
| 128-bit | `i128`  | `u128`   |
| arch    | `isize` | `usize`  |

##### Integer Overflow
- In debug mode, will panic in the case of an integer overflow
- In compiled mode, will preform integer wrapping and the number will "wrap around" to the lowest (or highest in the case of underflow) possible number of its type

##### Floats
- same size types are used but with f prefix. ex: `f64`

#### Other types

| Data Type | Keyword |
| --------- | ------- |
| boolean   | bool    |
| char      | char    |

#### Tuples
- compound data types
- use parens with the types pattern you want to denote types
- `let mut tup: (u32, bool, i8)`
- access like a struct but with the index
	- `tup.0 = 1`

#### Array
- what it sounds like
- you can manually set the values of the array
	- `let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];`
- or you can set the length and type
	- `let a: [i32; 5]`
- you can also specify a number and repeated value
	- `let a = [3; 5];`

