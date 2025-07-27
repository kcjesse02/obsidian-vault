- Rust doesn't have a garbage collector because it is a system language (garbage collection would be too performance intensive for lower-level and embedded stuff)
- It doesn't give the programmer control over the allocation of memory like in C either! B
because that can cause memory errors and 
- Compilation ensures that certain rules are followed around memory. If those rules aren't followed, the program won't compile!
what are the rules then?
## The Rules
- Each value has an owner
- There can only be one owner at a time
- When the owner goes out of scope, the value will be dropped (memory freed)
	- all function local values are dropped once the function has concluded.

**Some Questions**
- What is the criteria for data to be placed on the stack or heap?
- What constitutes the owner? A process? A thread?

## Variable Scope
- scope: a range within a program for which an item is valid
	- So scopes are entirely defined by the protection of the values inside of it
```Rust
    {   // s is not valid here, it’s not yet declared
        let s = "hello";   // s is valid from this point forward

        // do stuff with s
    }  // this scope is now over, and s is dropped
```
- you can use scopes to control when values are dropped!
## Stack and Heap
[[Memory Virtualization]]
- Because there is no manual control of memory, rust follows some rules about how it allocates stuff on the stack or heap
- mutable stuff on the heap, immutable on the stack
- simple data types (integers, bools, chars, string literal(immutable))
- more complex data and more permanent data stored on the the heap (string)
```rust
let s = "hello"; //this is a string literal, stored on 
//just represnts a string of 8-bit characters (all as one)
let mut s = String::from("hello"); //this is a String, stored on heap
//can be mutated with push_str()
//represents a pointer to a char *
```

#### Mut vs immut values moving

known size data
```rust
    let x = 5;
    let y = x;
```
- the value of x is copied to y
- x and y are both independently equal to 5
- type of 

unknown/changing size data
```rust
let s1 = String::from("string")
let s2 = s1
```
- s1 represents a pointer to the value of type string
- s2 is set to that pointer. 
- if s2 modifies the string it points to, s1 will also read that change
- this is the case for all other objects and structs!

![string_copying](images/rust_str_copying.png) 

- rust compiler will automatically drop the earlier reference if there are two references to the same memory location
- if you try to access `s1` after `s2` is set, you will get an error that tells you where the value was moved
- because the first pointer variable is let go, this is known as a move in rust

In order to actually copy complex values, use the `.clone()` function
```rust
    let s1 = String::from("hello");
    let s2 = s1.clone();

    println!("s1 = {s1}, s2 = {s2}");
```

## Functions
```Rust
fn main() {
    let s = String::from("hello");  // s comes into scope

    takes_ownership(s);             // s's value moves into the function...
                                    // ... and so is no longer valid here

    let x = 5;                      // x comes into scope

    makes_copy(x);                  // because i32 implements the Copy trait,
                                    // x does NOT move into the function,
    println!("{}", x);              // so it's okay to use x afterward

} // Here, x goes out of scope, then s. But because s's value was moved, nothing
  // special happens.

fn takes_ownership(some_string: String) { // some_string comes into scope
    println!("{some_string}");
} // Here, some_string goes out of scope and `drop` is called. The backing
  // memory is freed.

fn makes_copy(some_integer: i32) { // some_integer comes into scope
    println!("{some_integer}");
} // Here, some_integer goes out of scope. Nothing special happens.
```
- when a value is passed into a function
	- it is effectively copied, which happens to be a move for variable length data
- For variable size data: the function takes ownership of the argument value
- for static sized data: the function does not need to take ownership

### Returning
```Rust

fn gives_ownership() -> String {       // gives_ownership will move its
                                       // return value into the function
                                       // that calls it

    let some_string = String::from("yours"); // some_string comes into scope

    some_string                        // some_string is returned and
                                       // moves out to the calling
                                       // function
}

// This function takes a String and returns a String.
fn takes_and_gives_back(a_string: String) -> String {
    // a_string comes into
    // scope

    a_string  // a_string is returned and moves out to the calling function
}
```
- to give ownership of a value created in a function, it must be returned