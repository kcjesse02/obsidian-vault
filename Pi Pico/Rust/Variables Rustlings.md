
### Constants
- constants must always be type annotated: `const NUMBER: i32 = 3`
- you aren't allowed to s=use mut with constants
- must be set to a constant expression. cannot be the result of someething
none of this:
```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
```
### Difference between String and &str type
- String is owned
- &str is a borrowed string
- What is Ownership then??

...
### Ownership
- Rust memory management system
	- rules are governed by the compiler
- Each value in rust has an owner. Can only have one owner at a time
- if the owner goes out of scope, the value will be dropped

##### Scope
- range in the program in which an item is valid

## Misc
- For loops are non inclusive