```Rust
use std::cmp::Ordering;
use std::io;

use rand::Rng;

pub fn game() {
	println!("Guess the number!");

	let secret_number = rand::thread_rng().gen_range(1..=100);
	//println!("The secret number is: {secret_number}");
	loop {
		println!("Please input your guess.");
		let mut guess = String::new();
		io::stdin()
			.read_line(&mut guess)
			.expect("Failed to read line");
/*

Variable shadowing

allows us to resue the same variable name rather than create two separate variables

often used when converting types

  

parse()

converts a string into another type!

It infers which type so make sure to assign its return value to a type-annotated var!

  

match

"control flow based on pattern matching"

- can be used to take input directly from functions!

- error handling!

the _ in the Err statement is a catchall for any value

*/

		let guess: u32 = match guess.trim().parse() {
			Ok(num) => num,
			Err(_) => continue,
		};
		println!("You guessed: {}", guess);
		
		//this is just a simplification of just using 3 if statements
		// `match` will match against a pattern
		match guess.cmp(&secret_number) {
			Ordering::Less => println!("Guess Higher!"),
			Ordering::Greater => println!("Guess Lower!"),
			Ordering::Equal => {
				println!("You win!");
				break;
			},

		}

	}

}
```


## Making the guessing game a module
- rename the main file to `guessing-game.rs`
-  the main function thumbnail to `pub fn guess()`
	- `pub` will make the new function public so other modules can call it. 
	- functions in a module can't have the same name as the module!
- create a new `src/main.rs` file with a main function
- in that file, define the guessing game module before the main with `mod guessing_game`
- in the main function, call the `guessing_game::guess()` function
	- modules can have a branching structure (like a directory system), so you just call functions of submodules of submodules of modules by chainging them together with double colons like this: `module::sub1::sub2:func()`
- if you are making a library crate (not a binary crate or a program to be run like this game), you will define modules in `src/lib.rs` file

