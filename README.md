# hl_abi

`hl_abi` lets Rust native libraries talk to HashLink through its C ABI. It
provides the Rust layouts that match `hl.h`, declarations for functions exported
by the runtime, and `define_prim!` for exposing a primitive to HashLink.

For example:

```rust
use hl_abi::{define_prim, hl_alloc_bytes, vbyte};

#[no_mangle]
pub unsafe extern "C" fn my_greet() -> *mut vbyte {
    hl_alloc_bytes(2)
}
define_prim!(hlp_greet, my_greet, "P_B");
```

## Why it has no runtime dependency

HashLink supplies the runtime symbols when a native library loads. If this
crate depended on the runtime as a Rust crate, the library could pull in a
second copy of its `#[no_mangle]` exports and hit duplicate-symbol linker
errors. `hl_abi` keeps the boundary to layouts and declarations; the
`define_prim!` macro generates the resolver in your library.

Keep this crate dependency-free and don't add exported functions to it.

## Who uses it

It was written for [hlwgpu](https://github.com/rayzor-blade/hlwgpu) and is also
used by the native libraries shipped with [ash](https://github.com/rayzor-blade/ash).
The crate itself isn't tied to either project.
