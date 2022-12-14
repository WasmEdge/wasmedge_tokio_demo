# WasmEdge Reqwest Demo

In this project, we demonstrate how to use **reqwest** and **tokio** to build async http client in WebAssembly and execute it using [WasmEdge]().

## Why tokio in WasmEdge?

There are growing demands to perform network requests in WASM and cloud computing. But it would be inefficient to perform network requests synchronously so we need async in WASM. 

As tokio is widely accepted, we can bring many projects that depend on tokio to WASM if we can port tokio into WASM. After that, the developers can have async functions in WASM as well as efficient programs.

With the help of tokio support of WasmEdge, the developers can compile the projects that use tokio into WASM and execute it using WasmEdge.


## Prequsites

We need install rust and wasm target first.

```bash 
# install rust 
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# install wasm target 
rustup target add wasm32-wasi
```

Then install the WasmEdge.

```bash
curl -sSf https://raw.githubusercontent.com/WasmEdge/WasmEdge/master/utils/install.sh | bash
```

## Add dependencies in **Cargo.toml**

In this project, we add tokio and reqwest as dependencies.

```toml
[dependencies]
reqwest = {git = "https://github.com/MediosZ/reqwest.git", branch = "wasmedge"}
tokio = { git="https://github.com/WasmEdge/tokio.git", branch = "wasmedge", features=["rt", "macros", "net", "time"]}
```

## Write the code 

We need to add some code into `src/main.rs`.

## Build and run it

First we need to compile the code.

```bash 
cargo build --target wasm32-wasi
```

Then we can run it using WasmEdge.

```bash
wasmedge target/wasm32-wasi/debug/wasmedge_reqwest_demo.wasm <url>
```

For simpilicity, we can add the following configs to `.cargo/config.toml`.

```toml
[build]
target="wasm32-wasi"

[target.wasm32-wasi]
runner = "wasmedge"
```

And then we can use `cargo run <url>` to execute it directly.

## Hyper server

We have an example showing how to setup an echo server using `hyper` in `src/bin/server.rs`.

We can run it with `cargo r --bin server` and open `http://127.0.0.1:3000` in the browser.
Or just run `cargo r --bin client` to receive the response.

# FAQ

## use of unstable library feature 'wasi_ext'

If you are using rustc 1.64, you may encounter this error. There are two options:

1. Update rustc to newer version. Validated versions are `1.65` and `1.59`.
2. Add `#![feature(wasi_ext)]` to the top of `mio/src/lib.rs`.
