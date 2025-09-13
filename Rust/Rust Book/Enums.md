its an enum
a type that can represent a limited set of possible values
"enumerate" possible values

example
```Rust
enum IpAddrKind {
    V4,
    V6,
}
```

creating instances of the enum
```Rust
    let four = IpAddrKind::V4;
    let six = IpAddrKind::V6;
```

allows for giving a struct a kind:
```Rust
    enum IpAddrKind {
        V4,
        V6,
    }

    struct IpAddr {
        kind: IpAddrKind,
        address: String,
    }

    let home = IpAddr {
        kind: IpAddrKind::V4,
        address: String::from("127.0.0.1"),
    };

    let loopback = IpAddr {
        kind: IpAddrKind::V6,
        address: String::from("::1"),
    };
```

or a piece of data can be stored directly in the enum:
```Rust
    enum IpAddr {
        V4(String),
        V6(String),
    }

    let home = IpAddr::V4(String::from("127.0.0.1"));

    let loopback = IpAddr::V6(String::from("::1"));
```
- this is more consise

different types can also be composed of different values
an example where IPv4 addresses are represented as 4 8-bit numbers:
```Rust
    enum IpAddr {
        V4(u8, u8, u8, u8),
        V6(String),
    }

    let home = IpAddr::V4(127, 0, 0, 1);

    let loopback = IpAddr::V6(String::from("::1"));

```

How does the rust standard library define IP addresses
- you can use a struct as the data stored in an enum type
```Rust
struct Ipv4Addr {
    // --snip--
}

struct Ipv6Addr {
    // --snip--
}

enum IpAddr {
    V4(Ipv4Addr),
    V6(Ipv6Addr),
}
```
- allows for you to put a much data as you want, to be specific.
- you can even put another enum in an enum

## Option enum
- alternative for using `None` type
- allows for a value to be something or nothing
- Rust actually doesn't have null value, has this instead

this is what the enum looks like:
```Rust
enum Option<T> {
    None,
    Some(T),
}
```
- its automatically brought into scope
	- you don't need to use the `Option::` prefix
example
```Rust
    let some_number = Some(5);
    let some_char = Some('e');

    let absent_number: Option<i32> = None;
```

**Why is Option better than having a None value**
- you can't even attempt to do an operation on an Option value without getting a compiler error
	- Option values are only for checking if a value exists
	- You have to convert an `Option<T>` to a `T` before performing operations on it
	- to use the value, you explicitly have to verify that it is not null
- prevents assumption that a value is not-null
- look at the `Option` [documentation](https://doc.rust-lang.org/std/option/enum.Option.html) for more info
- 
