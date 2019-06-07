* Rust compiler runs on and compiles to many platforms.
* All platforms are not supported equally.
* Rust's support levels are organized into three tiers - each with different set of guarantees.
* Platforms are identifies by their "target triple" which is the string to inform the compiler what output should be produced.
# Tier 1
* Can be thought of as "guaranteed to work".
* Following requirements will be satisfied :
	* Official binary releases.
	* Automated tests can be run for the platform.
	* Documentation for using and building on platform.
# Tier 2
* Can be thought of as "guaranteed to build".
* Automated tests are not run.
* Following requirements will be satisfied :
	* Official binary releases.
	* Automated builds is setup, but may not be running tests.
# Tier 2.5
* Can be thought of as "guaranteed to build" but without builds available through `rustup`.
* Automated tests are not run.
* Following requirements will be satisfied :
	* Automated builds is setup, but may not be running tests.
# Tier 3
* Rust codebase has support but builds and tests are not automated and may not work.
* Official builds are not available.
# References
* https://forge.rust-lang.org/platform-support.html
