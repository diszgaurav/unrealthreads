---
title: "Rust Notes [Draft]"
date: 2018-07-31T06:47:12+05:30
Description: ""
Tags: [Rust]
Categories: [Programming]
---

> Important:
<br>1. These are merely notes from __The Book__: [The Rust Programming Language](https://doc.rust-lang.org/book/second-edition/foreword.html). For better understanding of concepts, please read the book.
<br>2. These notes are targeted on only Linux machine (Debian).

# Installation

[Details here](https://www.rust-lang.org/en-US/install.html)

Install the tool-chain:
``` shell
curl https://sh.rustup.rs -sSf | sh
```

Add it in your PATH:
``` shell
source ~/.cargo/env
```

# Hello World
- ```.rs``` extension
- starts with ```main```
- Spaces(level*4) for indentations
- macros have ```!``` in name

    ```rust
    // hello_world.rs file
    fn main() {
        println!("Hello World!");
    }
    ```

    ```shell
    $ rustc hello_world.rs ;# generates hello_world executable in cwd
    ```

## cargo
- ```cargo``` is Rust's build and package manager
- ```cargo new <project_name> --bin``` generates an executable project
- Generated dir contains ```src``` dir for all the code and a ```Cargo.toml``` file for project configuration
- Packages in Rust are called ```crates```
- ```cargo build``` builds project for debugging without optimizations, and generated binary is available under ```target/debug```
- ```cargo check``` checks project just for compilation pass, no binary is generated; faster than ```build``` so preferred during development
- ```cargo run``` builds the project and runs the executable
- ```cargo build --release``` builds the project for release with optimizations; executable gets generated under ```target/release```

    > See https://crates.io for available crates
    
    Quick steps to use a crate:
    1. Search for required crate at above location
    2. Add it in your dependencies in Cargo.toml file as, for example rand:
       [dependencies]<br>
       rand = "0.5.4"
    3. Do cargo build. It will fetch the crate and other dependencies too

    > The ```Cargo.lock``` file keeps the versions of all crates with which the project was first build. Summarily, it makes sure if the project builds for you once, it builds again anytime and for everybody else too without any change!

# Common Programming Concepts

## Variables and Constants
- Variable are immutable by default: ```let x = 23; x = 12; // Error```
- Want mutable? Use __mut__: ```let mut x = 23; x = 12; // ok```
- __Shadowing__ is allowed: ```let x = 23; let x = 12; // ok```
- Constants are immutable always. Type is must to be annotated. ```const MAX_SIZE: u32 = 10_000;```

## Data Types

### Scalar Types

#### Integer
i8/u8, i16/u16, i32/u32, i64/u64, isize/usize (arch based). Default is i32 (fastest even on 64-bit machines). Integer Literals:

- Decimal: 23_12
- Hex: 0x2312
- Octal: 0o2312
- Binary: 0b
- Byte(u8): b'A'
        
#### Floating point
f32, f64 (based on IEEE-754 standard). Default is f64.

#### Boolean
true, false

#### Character
Unicode scalar values range [U+0000, U+D7FF] and [U+E000, U+10FFF]. ```let c = 'z';```

### Compound Types

#### Tuple
Comma separated values of any types in parentheses
```rust
let tup: (i32, f64, u8) = (23, 23.12, 'B');
println!("0th element of tup: ", tup.0); // accessing as tup.<pos>
let (x, y, z) = tup; // destructuring
```

#### Array
Comma separated values of same types in brackets

``` rust
let arr = ["Jan", "Feb", "Mar"];
println!("Index0: {}", arr[0]);
```

> Rust provides a safety mechanism which prevents accessing out of bound array indices during runtime.

## Functions
- Function names should be in snake case i.e. all small letters and words separated with underscore
- Function definition location doesn't matter i.e. before main or after main.
- Function definition:
    
    ```rust
    fn sum(a: u32, b: u32) -> u32 {
        a + b
    }
    ```

> statements are those which don't return a value and expressions are otherwise

In above example, ```a+b``` isn't terminated with semicolon, so, it is an expression which results in a value. Statements return empty tuple ().

## Comments

``` rust
// single line comment
// extended to multi-line
let x = 3; // an inline comment
```

## Control Flow

### if

``` rust
if <boolean> {
} else if <boolean> {
} else {
}
```
- if is an expression, so when the return value is assigned to some var, each arm of if-else-if should return a consistent type

### loop

``` rust
loop {
    // snip
    break;
}
```

### while
- conditional loop

``` rust
while condition_true {
    // run it
}
```

### for
- loop through an iterable

``` rust
let arr = [1, 2, 3];
for elem in arr.iter() {
    println!("{}", elem);
}
```
