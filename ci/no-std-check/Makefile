NIGHTLY_VERSION=nightly-2021-09-01

.PHONY: all setup check-panic-conflict check-cargo-build-std check-wasm check-substrate help

.DEFAULT_GOAL := help

all: setup ## Perform the setup and run all the checks.
	$(MAKE) check-panic-conflict || true
	$(MAKE) check-cargo-build-std || true
	$(MAKE) check-wasm || true
	$(MAKE) check-substrate || true

setup: ## Install the configured nightly Rust version and the wasm32-unknown-unknown target
	rustup install $(NIGHTLY_VERSION)
	rustup target add wasm32-unknown-unknown --toolchain $(NIGHTLY_VERSION)


check-panic-conflict: ## Check for `no_std` compliance by installing a panic handler, and any other crate importing `std` will cause a conflict. Runs on default target.
	cargo build \
		--features use-unsupported

check-cargo-build-std: ## Check for `no_std` compliance using Cargo nightly's `build-std` feature. Runs on the target `x86_64-unknown-linux-gnu`.
	rustup run $(NIGHTLY_VERSION) -- \
		cargo build -j1 -Z build-std=core,alloc \
		--features use-unsupported \
		--target x86_64-unknown-linux-gnu

check-wasm: ## Check for WebAssembly and `no_std` compliance by building on the target `wasm32-unknown-unknown` and installing a panic handler.
	rustup run $(NIGHTLY_VERSION) -- \
		cargo build \
		--features use-unsupported \
		--target wasm32-unknown-unknown

check-substrate: ## Check for Substrate, WebAssembly, and `no_std` compliance by importing Substrate crates and building on `wasm32-unknown-unknown`.
	rustup run $(NIGHTLY_VERSION) -- \
		cargo build \
		--no-default-features \
		--features use-unsupported,use-substrate \
		--target wasm32-unknown-unknown

# Self-documenting Makefile via https://marmelab.com/blog/2016/02/29/auto-documented-makefile.html
help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

