# Which installer should you use?
* Why might one not want to install using `rustup`?
	* Offline Installation : `rustup` downloads components from the internet on demand.
	* Preference for the system pacakge manager.
	* Preference againt `curl | sh`.
	* Validating Signatures
	* GUI Installation
* Rust's platform support is defined in [three tiers](https://github.com/pravsemilo/rust-notes/blob/master/forge.rust-lang.org/Rust_Platform_Support.md), which correspond closely with the installation methods available.
	* Binary builds are provided for tier 1 and tier 2 platforms and available via `rustup`.
	* Some tier 2 platforms only have standard library available, not the compiler itself.
		* Such platforms are cross-compilation targets.
		* Rust code can run on those platforms.
		* Compiler cannot run on those platforms.
		* Support for such platforms can be installed with `rustup target add`.
# Other ways to install  `rustup`
* On Unix, run `curl https://sh.rustup.rs -sSf | sh`. This downloads and runs `rustup-init.sh`.
* On Windows, download and run `rustup-init.exe`.
* `rustup-init` can be configured interactively and all options can additionally be controlled by command line arguments.
# Standalone Installer
* Contains a single release of Rust.
* Suitable for offline installation.
* Installs `rustc`, `cargo` `rustdoc`, standard library and standard documentation.
* Does not provide access to cross-compilation targets like `rustup` does.
# References
* https://forge.rust-lang.org/other-installation-methods.html
