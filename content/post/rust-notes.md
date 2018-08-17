---
title: "Rust Notes [Draft]"
date: 2018-07-31T06:47:12+05:30
Description: ""
Tags: [Rust]
Categories: [Programming]
---

> Important:
<br>1. These are merely notes from __The Book__: [The Rust Programming Language]
(https://doc.rust-lang.org/book/second-edition/foreword.html). For better
understanding of concepts, please read the book.
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
```
[dependencies]<br>
rand = "0.5.4"
```
3. Do cargo build. It will fetch the crate and other dependencies too

> The Cargo.lock file keeps the versions of all crates with which the project was first build. Summarily, it makes sure if the project builds for you once, it builds again anytime and for everybody else too without any change!


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

# Ownership

- Ownership, borrowing and slicing ensure memory safety at compile time.
- Some languages have garbage collection that constantly looks for no longer
used memory as the program runs; in other languages, the programmer must
explicitly allocate and free the memory. Rust uses a third approach: memory is
managed through a system of ownership with a set of rules that the compiler
checks at compile time. None of the ownership features slow down your program
while it’s running.
- Ownership rules:

        Each value in Rust has a variable that’s called its owner.
        There can only be one owner at a time.
        When the owner goes out of scope, the value will be dropped.

## Move: transfer of ownership
- For the variables whose data storage is in _Heap_ (i.e. for which
the size is not fixed), assigning one variable to another, makes the previous
variable go out of
scope.

```rust
let x = 3;
let y = x;
println!("x = {}, y = {}", x, y); // works as storage is on stack only

let s1 = String::from("hello");
let s2 = s1;
println!("s1 = {}, s2 = {}", s1, s2); // ERROR!
```

- s1, s2 example above can be considered as _shallow copy_, but Rust goes
ahead and removes s1, so, it becomes a _move_
- For a deep copy of s2 to s1, there is a method ```clone```:

```rust
let s2 = s1.clone(); // expensive call
```
    then both, s1 and s2 remains in scope.
- So, __the rule__: The types which have __copy__ trait survives during copy:
  - All scalar types and tuple too (only if they contain copy-able scalars)
  - Also, the type should not implement __drop__ trait
- This scheme works similarly for function arguments

```rust
fn main() {
    let x = 2;
    let s = String::from("hello");
    some_func(x, s);
    // x is usable here, but s is not
}
```
    so, we can't use the value we just passed to the function? Well, we can
    have it ownership back in return value but that is just too much moving
    around of ownership. Enter the reference...

## References and borrowing
- We can create a reference by using ```&```:

```rust
fn main() {
    let x = 2;
    let s = String::from("hello");
    some_func(x, &s);
    // x is usable here, and s can also be used. It was just borrowed and we
    // have it back now
}

fn some_func(x: i32, s: &String) {
    println("{}", s); // Borrowed string
    s.push_str(" world!"); // ERROR! can't modify a borrowed stuff
}
```

- s could not be modified as it was borrowed immutable. So, if we want to modify
  it, these conditions should satisfy:
  - s should be mutable in its definition
  - passed as mutable in function argument
  - borrowed as mutable in function parameter

```rust
fn main() {
    let x = 2;
    let mut s = String::from("hello");
    some_func(x, &mut s);
    // x is usable here, and s can also be used. It was just borrowed and we
    // have it back now
}

fn some_func(x: i32, s: &mut String) {
    println("{}", s); // Borrowed string
    s.push_str(" world!"); // works!
}
```
- Only one mutable reference can exist in a scope. This avoids data races at compile
  time itself - Rust, just doesn't compile code.
- Also, we can't have a mutable and an immutable reference together in a scope.
  The user of immutable reference doesn't want to data to change.
- It is possible to have any number of immutable references at a time.
- Rust avoids _dangling pointers_ situation by making sure that data doesn't go
  out of scope before its reference

```rust
// ERROR! s goes out of scope, &s becomes invalid
fn dangle() -> &String {
    let s = String::from("hello");
    &s
}
// WORKS! returns ownership of s data
fn dangle() -> String {
    let s = String::from("hello");
    s
}
```
- References must always be valid.

## Slice
- Why slices? String indices makes sense when the related string is also in the
  scope. There could be a possibility to have indices around and string has gone
  out of scope.

```rust
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```

> More on [String v/s str](https://stackoverflow.com/questions/24158114/what-are-the-differences-between-rusts-string-and-str/24159933#24159933)

- String literals are slices. ```&str``` is immutable reference.

```rust
let s = "Hello World!";
let s: &str = "Hello World!";
```

- Slicing can be done on other collections as well:

```rust
let a = [1, 2, 3, 4, 5]
let b = &a[1..3];
```
