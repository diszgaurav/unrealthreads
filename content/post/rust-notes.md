---
title: "Rust Notes [Draft]"
date: 2018-07-31T06:47:12+05:30
Description: ""
Tags: [Rust]
Categories: [Programming]
---

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
