```
fn main() {
let mut a_guy = "a guy".to_string();//mutable

kath(&a_guy);
kath(&a_guy); //wrong: use of a moved value
/*
cant pass something by value twice
function takes ownership of variable once it is passed
pass by reference to avoid function taking ownership
- a
*/
a_guy = "another separate guy".to_string();
kath(&a_guy);
}

  

fn kath(name: &String){
println!("Hello {}!", name);
}
```

`cargo.toml` - project information in this file
`main.rs` - the file with the main function, the function that executes when you run the binary
