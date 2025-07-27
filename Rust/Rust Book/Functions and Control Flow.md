## Functions
- use the `fn` keyword
- name using `snake_case`
- parameters must be type annotated in the function signature
```Rust
fn another_function(x: i32) {
    println!("The value of x is: {x}");
}
```
- Function bodies are made up of statements and expressions
### Statements
- perform an action but do not return a value
- examples
	- `let` statements
	- `break`
- rule of thumb: you can't set other values to a statement
### Expressions
- evaluate to a result
- Examples
	- `if` is actually an expression in rust
	- anything that uses `{}` after it
	- `match`
	- Any expression can be returned from a function
- important: a new scope block is an expression
```Rust
fn main() {
	//everything inside the curly braces is the expression!
    let y = {
        let x = 3;
        x + 1
    };

    println!("The value of y is: {y}");
}
```
- Use semicolons at the end
#### Return Values
- set the return value with an arrow `->`
```Rust
fn plus_five(x: i32) -> i32 {
	x+5//don't add a ; her becuase it will make this an expression and we will get a compiler error
}
```
- you don't need return statements to return a value
- don't use a semicolon at the end
## Control 
### If Expressions
```Rust
//basic if-else
if number < 5 {
    println!("condition was true");
} else {
    println!("condition was false");
}

//else ifs
let number = 6;

if number % 4 == 0 {
    println!("number is divisible by 4");
} else if number % 3 == 0 {
    println!("number is divisible by 3");
} else if number % 2 == 0 {
    println!("number is divisible by 2");
} else {
    println!("number is not divisible by 4, 3, or 2");
}
```
- condition must evaluate to a bool

since ifs are expressions, you can assign the results to a variable. only, all branches must produce the same data type

```Rust
//bad
let number = if condition { 5 } else { "six" };
//good!
let number = if condition { 5 } else { 6 };
```

### Loops
- `loop{ ... }` just runs forever (great for embedded!)
- like ifs, they are expressions and can be set to a variable, which will collect the result
```Rust
let result = loop {
    counter += 1;

    if counter == 10 {
        break counter * 2;
    }
};
```

- break can also return a value with it!
you can label loops, so you can specify which loop continue and break apply to for nested loops!
- also it makes the code easier to read
```Rust
fn main() {
    let mut count = 0;
    'counting_up: loop {
        println!("count = {count}");
        let mut remaining = 10;

        loop {
            println!("remaining = {remaining}");
            if remaining == 9 {
                break;
            }
            if count == 2 {
                break 'counting_up;
            }
            remaining -= 1;
        }

        count += 1;
    }
    println!("End count = {count}");
}
```

#### For Loops
```Rust
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {element}");
    }
}
```

#### While Loops
```Rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{number}!");

        number -= 1;
    }

    println!("LIFTOFF!!!");
}
```