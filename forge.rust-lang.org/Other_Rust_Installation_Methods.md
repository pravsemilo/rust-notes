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
# References
* https://forge.rust-lang.org/other-installation-methods.html
