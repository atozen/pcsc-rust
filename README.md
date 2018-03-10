# pcsc-rust

[![crates.io](https://img.shields.io/crates/v/pcsc.svg)](https://crates.io/crates/pcsc)
[![docs.rs](https://docs.rs/pcsc/badge.svg)](https://docs.rs/pcsc)
[![license](http://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/bluetech/pcsc-rust/blob/master/LICENSE-MIT)

Linux, macOS: [![Travis CI](https://travis-ci.org/bluetech/pcsc-rust.svg?branch=master)](https://travis-ci.org/bluetech/pcsc-rust)
Windows: [![AppVeyor](https://ci.appveyor.com/api/projects/status/s16sb4kt79v7yop4/branch/master?svg=true)](https://ci.appveyor.com/project/bluetech/pcsc-rust/branch/master)

Rust bindings to the PC/SC API for smart card communication.

- Nice, safe API.
- Works on all major operating systems.
- Mostly zero overhead.

See the [Documentation](https://docs.rs/pcsc) for more details.

See the `pcsc/examples` directory for some common tasks.

## Contents

The [`pcsc-sys`](https://docs.rs/pcsc-sys) crate contains the direct,
low-level bindings to the C API.

The [`pcsc`](https://docs.rs/pcsc) crate contains the high-level Rust
wrappers.

## Usage

In your `Cargo.toml`:

```toml
[dependencies]
pcsc = "1"
```

In your crate:

```rust
extern crate pcsc;
```

## Example

Connect to the card in the first available reader and send the card an
APDU command.

```rust
extern crate pcsc;

use pcsc::*;

fn main() {
    // Establish a PC/SC context.
    let ctx = Context::establish(Scope::User)
        .expect("failed to establish context");

    // List available readers.
    let mut readers_buf = [0; 2048];
    let mut readers = ctx.list_readers(&mut readers_buf)
        .expect("failed to list readers");

    // Use the first reader.
    let reader = readers.next().ok_or(())
        .expect("no readers are connected");
    println!("Using reader: {:?}", reader);

    // Connect to the card.
    let card = ctx.connect(reader, ShareMode::Shared, Protocols::ANY)
        .expect("failed to connect to card");

    // Send an APDU command.
    let apdu = b"\x00\xa4\x04\x00\x0A\xA0\x00\x00\x00\x62\x03\x01\x0C\x06\x01";
    let mut rapdu_buf = [0; MAX_BUFFER_SIZE];
    let rapdu = card.transmit(apdu, &mut rapdu_buf)
        .expect("failed to transmit APDU to card");
    println!("{:?}", rapdu);
}
```

## Status

- There are still some minor [TODOs](https://github.com/bluetech/pcsc-rust/search?q=TODO)
  remaining.

Help is welcome!

## License

The MIT license.
