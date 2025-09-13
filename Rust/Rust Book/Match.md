- compares a value against a series of patterns
- compiler confirms that all cases are handled!

Here's an example:
```Rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```
- "converts" an enum to another type
- ensures each possible value or category of `Coin` is covered
- similar to a conditional 
-  follows this format: `Pattern => Code`
	- code can be executed from the code arm of a `match` statement
	- Use curly brackets (a scope) if code is longer than one line
	- This should be a 

example of code:
```Rust
fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => {
            println!("Lucky penny!");
            1
        }
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

**Patterns can bind to values**
```Rust
#[derive(Debug)] // so we can inspect the state in a minute
enum UsState {
    Alabama,
    Alaska,
    // --snip--
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {state:?}!");
            25
        }
    }
}
```
- the value for state can match to the value stored in the quarter option

Since matches must be exhaustive, they are very good for dealing with `Option<T>`

This causes a bug
```Rust
    fn plus_one(x: Option<i32>) -> Option<i32> {
        match x {
            Some(i) => Some(i + 1),
        }
    }
```

- it must handle the `None` case as well
	- and all possible values for the enum of `x`
- this is why `match` is useful for handling Options!


## Catch-all patterns
- we can use a value to represent all other values in an exhaustive match
- so we don't have to be exhaustive in covering objects
```Rust
    let dice_roll = 9;
    match dice_roll {
        3 => add_fancy_hat(),
        7 => remove_fancy_hat(),
        other => move_player(other),
    }

    fn add_fancy_hat() {}
    fn remove_fancy_hat() {}
    fn move_player(num_spaces: u8) {}
```

- `other` is a stand in for every other value
- We can use any var name in place of other
- the value of `dice_roll` is transfered to other though...

with rust `_`:
```Rust
    let dice_roll = 9;
    match dice_roll {
        3 => add_fancy_hat(),
        7 => remove_fancy_hat(),
        _ => reroll(),
    }

    fn add_fancy_hat() {}
    fn remove_fancy_hat() {}
    fn reroll() {}
```
- this ignores all other values, so unwanted values are immediately dropped

another example: nothing else happens on your turn if you don't roll a 3 or 7
