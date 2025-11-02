From [[ELF file format]], Focus on Static Binaries first
- used in embedded
- more application to work

### File error handling
this following line:
```rust
let mut file = File::open(path)?
```
produces the following error:
```rust
the trait `From<std::io::Error>` is not implemented for `String`
```

the opening file is defined in a function that returns a 

### Some things for the future
- Use a `BufferedReader` - adds buffering to reading a file to make it more efficient