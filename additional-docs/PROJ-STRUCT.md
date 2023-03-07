# Project Structure
> Brief documenation regarding my project structure and why the architecture was designed said way.

Various key points for my structure include:
- logical components are grouped together (`folders`/`files`)

The main advantage of this is that it allows developers to work on multiple sections of the application at the same time, focusing on parts of the code that change often as opposed to parts that rarely alter.

## Modules
> Modules are used to group code together in Rust.

The `mod` keyword can be used to indicate a new module. `snake_case` is recommended for mod naming. `mod` should be used instead of `use` when working with local files. A `mod.rs` file can be used to link all files to a `main.rs` file.

- `mod`: *define path to module for compiler (stored for* future use)
- `use`: *uses the module, telling the compiler it is available* (path)
    - additionally, the `use` keyword is useful when a single module wants to be used via another
The `pub` keyword can expose components in a file to be used outside of that file.

All files within a directory can be linked through a `mod.rs` file defining them as follows:
```rs
pub mod class;
pub mod course;
pub mod day;
```

At this point, the entire folder can be imported anywhere in the project as follows:
```rs
mod entities;
```

Additionally, `use crate::[file_name/folder_name]` can be used anywhere in the project to import functionality.

Separate independent functionalities, such as my handling of errors, is defined outside of the `src` code directory and in its own handle-errors library. Excluding code like this has several benefits:
- modularizes codebase
- code can be offered as separate library inside company/world
- can help prevent always having to increase version numbers for larger codebases

## Documentation
`///` — One-line doc comment
`/** ... */` — Block doc comment