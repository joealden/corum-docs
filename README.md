# corum-docs

The Documentation for Corum and its API.

To read this book just on Github, open `src/SUMMARY.md`.

## Usage

This documentation is designed to be processed by the rust crate, (package)
`mdbook`, so ensure that you have rust and rust's package manager `cargo`
installed on your machine. Next, install the `mdbook` cargo crate with the
following command:

```
cargo install mdbook
```

Once that has finished, the `mdbook` program should be available. To test that
the installation has worked, run the following command:

```
mdbook --version
```

If you get something like `mdbook vX.X.X`, `mdbook` is installed.

If you get something like `command not found: mdbook`, then the installation
didn't work. The issue might be that cargo's install bin is not in your `PATH`
variable. Add the directory to your `PATH` variable and try again.

Now that `mdbook` has been installed, clone this repo if you haven't already. To
compile the book, run the command `mdbook build` in the root directory. A `book`
directory will be created, this can now be served statically on any web server.

## Development

To ensure consistent formatting of the `md` files, we use the `prettier` tool
that is built with `nodejs`. If you do not have `node` installed, install it
now.

This project is setup to use the `yarn` package manager instead of `npm`, the
package manager that is bundled with node. If you do not have `yarn` installed,
install it now.

Next, run the following command to install the project's dependencies:

```
yarn
```

Now, all `md` files will be automatically formatted to be inline with the
project's style. This is done use the `husky` and `lint-staged` packages.

To find out more about mdbook, visit
[https://github.com/rust-lang-nursery/mdBook](https://github.com/rust-lang-nursery/mdBook).
