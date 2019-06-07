# Using rustup (Recommended)
```bash
curl https://sh.rustup.rs -sSf | sh
```
# Notes about Rust installation
## Toolchain management with `rustup`
* Rust is installed and managed by the `rustup` tool.
* Rust has a 6-week rapid release process and supports many platforms. So there are many builds of Rust available at any time.
* `rustup` helps to :
	* Manage these builds in a consistent way for each platform.
	* Enable installation from beta and nightly release channels.
	* Support for additional cross-compilation targets.
* To update installation :
```bash
rustup update
```
## Configuring the `PATH` environment variable
* All tools are installed to the `~/.cargo/bin` directory. This is where you will find the Rust toolchain, including `rustc`, `cargo` and `rustup`.
* Need to have `~/.cargo/bin` in `PATH`.
# Other installation methods
* The installation described above, via `rustup` is the preferred way to install Rust. Howover Rust can be installed via other methods as well. See [here](https://github.com/pravsemilo/rust-notes/blob/master/forge.rust-lang.org/Other_Rust_Installation_Methods.md) .
# References
* https://www.rust-lang.org/tools/install
