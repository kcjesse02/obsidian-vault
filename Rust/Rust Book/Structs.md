yeah its a struct
```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}
//instantiate the struct
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn main() {
    let user1 = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    };

	user1.email = String::from("anotheremail@example.com");
}
```
- entire struct must be either mutable or immutable, can't specify specific fields

try making a wrapper function to define an instance of a struct

shorthand for instantiating structs
```rust
fn build_user(email: String, username: String) -> User {
    User {
        active: true,
        username,
        email,
        sign_in_count: 1,
    }
}
```
- since the names for the arguments:`username` and `email` are the same as the field names, we can just set the username and email

create a struct from another struct
- bad way: you can just reference the struct field
- rust way: just say "yeah and the rest is from struct1"
```rust
	//dumb
    let user2 = User {
        active: user1.active,
        username: user1.username,
        email: String::from("another@example.com"),
        sign_in_count: user1.sign_in_count,
    };

	//and the rest is ..user1
    let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    };
    //different value for email
    //same value for all other fields
```

we can also create specific, named tuples with `struct` keyword
```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);
```
- allow for seemingly identical values to mean different things

Unit like structs
```rust
struct AlwaysEqual;

fn main() {
    let subject = AlwaysEqual;
}struct AlwaysEqual;
```
- used to implement a trait on a type, but don't have any data you want to store in the type
- allows us to implement behavior later on (for example, `AlwaysEqual` can be a type that will always be considered equal when compared to another type or value!)

### Ownership of Structs
- in the `User` struct, the `String` type is used rather than `&str` so the struct has ownership over its field
	- but we only do this if we want the struct to own the data
	- structs can store refs to other data
	- `&str` would have to pass in a reference to another `&str` 
If you set a `&str` a newly defined string, you will get this error
```console
 --> src/main.rs:3:15
  |
3 |     username: &str,
  |               ^ expected named lifetime parameter
  |
help: consider introducing a named lifetime parameter
```

### Derived Traits
What happens if you try to put a struct as an argument in a `println!` statement
```Rust
println!("The value of struct1 is {struct1}");
```
- you will get this error
```console
error[E0277]: `Rectangle` doesn't implement `std::fmt::Display`
```
- curly brackets in `println!` tell the program to use `Display` formatting
- structs don't implement `Display` by default because there are more possibilities on how to show a struct
- we can use the `{struct1:?}` for debug, but the struct doesn't implement `Debug`
```console
   = help: the trait `Debug` is not implemented for `Rectangle`
   = note: add `#[derive(Debug)]` to `Rectangle` or manually `impl Debug for Rectangle`
```
- put `#[derive(Debug)]` before the struct definition
```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {rect1:?}");
}
```

this is how the `Rectange` will be displayed
```console
rect1 is Rectangle { width: 30, height: 50 }
```

you can also use `dbg!` macro, which takes ownership of the expression as oposed to `println!`
- `dbg!` also prints to stderr, instead of stdout like `println!`

We can put `dbg!` around any expression
```rust
    let rect1 = Rectangle {
        width: dbg!(30 * scale),
        height: 50,
    };
```

output
```console
[src/main.rs:10:16] 30 * scale = 60
```

zamn print statement debugging
