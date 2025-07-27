- like functions, but defined within the context of a [[Structs]], `enum`, or `trait`

```Rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn width(&self) -> bool {
        self.width > 0
    }
}
```
- create a method for a defined thing by creating an `impl` block for that object
- Use a reference to the object that is being implemented with `&self`
	- allows for the same things to be done with python and its `self` parameter for methods in classes
	- the type of the `&self` is `Self`. use `Self` to refer to the type of the struct (or whatever)
	- `&self` is short for `self: &Self`
	- use the & to indicate the method borrows self. You can take [[Ownership]] of self if you want or do whatever you do with any other parameter
- referenceing fields
	- use `self.field`
	- just like python
- width method note
	- a getter 
	- rust does not implement gtters and setters automatically
- return type
	- indicated by `->` (like in python)
	- does not mean to run a method on a pointer to an object like in c
	- rust has automatic referencing and dereferencing. You can just use the `.` on any reference.
	- automatic pointer handling is rare in rust so don't get used to it

>Given the receiver and name of a method, Rust can figure out definitively whether the method is reading (&self), mutating (&mut self), or consuming (self). The fact that Rust makes borrowing implicit for method receivers is a big part of making ownership ergonomic in practice.


How do we call these methods?
```Rust
fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area()
    );
}
```
- just obj.method() syntax bro

