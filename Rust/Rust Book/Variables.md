---
source: https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html
---
## Mutability
In rust, you need to indicate a variable as mutable with the keyword `mut`

for example this code will not run
```rust
fn main() {
    let x = 5;
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
```
`x` is not set with the mutable key word. change the first line of `main()` to `let mut x = 5;` to stop getting a mutability error.

## Constants
**What is the difference between a variable and a constant?**
- Can't use the `mut` keyword. They're always constants!
- must annotate the type (like this `const CONST_1 : u64` )
	- essentially defining how much space this will use
- value can't be set from the result of a computation (no variables, only constant values)
	- however, you can perform inline computation like this: `60 * 60 * 3`

## Shadowing
- variables can be shadowed by defining a new variable that has the same name as the variable to be shadowed.
- can be used to change the values of immutable variables in different scopes!
ex: modifying immutable values
```rust
let an_integer = 1u32;

println!("an integer: {:?}", an_integer);

let an_integer = an_integer+5;

println!("new int: {:?}", an_integer);
```
prints:
```
an integer: 1
new int: 6
```

ex: scopes
```rust
let a_boolean = true;

println!("A boolean: {:?}", a_boolean);
{
    //shadowing
    let a_boolean = false;
    println!("shadow value of a boolean: {:?}", a_boolean);
}

println!("back to normal: {:?}", a_boolean);
```

## Data Types

### Scalar Types
- represent a single value
- integers, floating points, booleans, characters

#### Integers
- defined by size, like shorts, longs, but with more descriptive names

| Length  | Signed | Unsigned |
| ------- | ------ | -------- |
| 8 bit   | i8     | u8       |
| 16 bit  | i16    | u16      |
| 32 bit  | i32    | u32      |
| 64 bit  | i64    | u64      |
| 128 bit | i128   | u128     |
| arch    | isize  | usize    |
- signed vars are in twos-compliment
- They can also all be represented in decimal, hex, octal, and binary

| Number literals  | Example       |
| ---------------- | ------------- |
| Decimal          | `98_222`      |
| Hex              | `0xff`        |
| Octal            | `0o77`        |
| Binary           | `0b1111_0000` |
| Byte (`u8` only) | `b'A'`        |
|                  |               |
#### Floating Points
- rust has f32 and f64
- will automatically use f64
- use f32 for older machines (or on embedded!)
- make sure to cast an int to a float before using the two types in a mathematical operation

chars and bools are pretty much the same as they are in any other language

### Compound
- types of multiple values
- includes structs and objects!

#### Tuple
- collection of multiple types
- once declared, cannot grow or shrink in size
- you can use pattern-matching to destructure a tuple
```Rust
let tup: (i32, f64, u8) = (43, 67.98, b'a');

//reference individual value
let forty_three = tup.0;

//destructuring
let (x, y, z) = tup;

println!("The value of y is: {y}");
```


#### Array
- collection of the same type, but can change in size
- you know what an array is, shawty
```rust
let a = [1, 2, 3, 4, 5];

//declaring an array 
let a2: [i32; 5];//[type; length]

//initializing an array with all the same value
let a3 = [0; 5];

let elm1 = a[0];

```

array program
```Rust
use std::io;
use std::cmp::Ordering;

fn main() {
    let a = [1, 2, 3, 4, 5];

	//use the len() function to get the length of the array
    let max_idx = a.len();

    println!("array length is {:?}.", max_idx);

    println!("Please enter a valid array index.");

    let mut index = String::new();

    io::stdin()
        .read_line(&mut index)
        .expect("Failed to read line");

    let index: usize = index
        .trim()
        .parse()
        .expect("Index entered was not a number");

	//this part was added by me. The rest is from the rust book
    match index.cmp(&max_idx)   {
        Ordering::Greater => {
            println!("Not a valid Index!");
            return;
        },
        Ordering::Less => {},
        Ordering::Equal => {},
    }

    let element = a[index];

    println!("The value of the element at index {index} is: {element}");
} 
```



