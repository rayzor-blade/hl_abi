# hl_abi

The HashLink C ABI, in Rust.

A HashLink native library includes `hl.h` and links against libhl. This is the
same thing for a library written in Rust: the `#[repr(C)]` layouts the runtime
and a library have to agree on, the functions the runtime exports, and the
`DEFINE_PRIM` macro that publishes a primitive.

```rust
use hl_abi::{define_prim, hlp_alloc_bytes, vbyte};

#[no_mangle]
pub unsafe extern "C" fn my_greet() -> *mut vbyte {
    hlp_alloc_bytes(2)
}
define_prim!(hlp_greet, my_greet, "P_B");
```

## It defines no symbol of its own

Everything here is a layout or a declaration, so a library built on it links
nothing of the runtime into itself. That is the whole point, and the reason
this is a crate rather than a module of a VM: depending on a HashLink runtime
as a Rust crate would put a second copy of its `#[no_mangle]` exports in the
library's archive, and two strong definitions of one name cannot be linked.

**So this crate must never gain a dependency, or define a function of its own.**

## Who uses it

Written for [hlwgpu](https://github.com/rayzor-blade/hlwgpu), and used by the
native libraries that ship with [ash](https://github.com/rayzor-blade/ash).
Nothing in it is specific to either.
