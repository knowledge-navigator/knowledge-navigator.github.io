# Why Rust
A brief overview as to why I have chosen Rust to support my web server as a microservice.

## Key Points
- **systems programming language** (not interpreted language)
    - has a compiler
- no **overhead** (e.g. no garbage collection)
- easy-to-read syntax
- offers performance like **C**
- safety (memory-safe)
- productivity
    - built-in tooling is great for **maintainability**:
        - testing
        - documentation
        - package manager
- versatile
- can compile to WebAssembly

## Rust Toolings
> Batteries Included

| Purpose | Tool Name |
|:---------------:|:-----------:|
| Version Manager | [Rustup](https://rustup.rs/) |
| Rust Compiler | [Rustc](https://doc.rust-lang.org/rustc/what-is-rustc.html) |
| Code Formatter | [Rustfmt](https://github.com/rust-lang/rustfmt) |
| Linter | [Clippy](https://github.com/rust-lang/rust-clippy) |
| Package Manager | [Cargo](https://doc.rust-lang.org/cargo/) |
| Package Registry | [crates.io](https://crates.io/) |

## Rust Compiler
> Rust's compiler is the main advantage over other languages.
- compiles down to binary code
- no garbage collection invoked at runtime

The above two points, **give it C-like speed** while still enforcing memory safety at compile time, something C does not do.

![Comparing languages in compiling source code to machine code.](../assets/images/compiling.jpg)

Even though GO's compiler is faster, it takes a small runtime performance in doing so.

## Rust's Integration with Web Services
> All efforts for implementing HTTP and other related features has been left up to the community, due to Rust being a systems language.

- **TCP** & **IP** are supported by the *Rust standard library*.
- **TLS** is supported by various *HTTP Server Crates*.
- **HTTP** is supported by several *Web Framework Crates*.

While this may be seen as a disadvantage, it is far from it. Most languages will have standard library support for HTTP and Web, but usually these are the bare minimum and dedicated frameworks are utilized instead.

Async, type, thread scheduler and kernel abstractions are all supported by Rust out of the box, providing all the building blocks we need for a web server.

## Maintainability of Rust Applications
> Built in language functionalities help improve the maintainability of Rust applications.

Rust has built-in **documentation** tools, allowing the generation of documentation with a single command (utilizing code comments). Tests will also run the code comments.

**Modularizing** code is clean as well, with the `Cargo.toml` file.

**Testing** is supported by default, thus additional crate, or helper tools/libraries are not required to write and run tests.

Generally speaking, these **tools being built-in** reduces friction within a team and lets development squads **immediately get to making progress rather than wasting time weighing different options**.