# H2

**WARNING:** This is a fork of h2 that provides interoperability with Google's Go implementation of gRPC (specifically, the one used in Kubernetes' Kubelet). To achieve this, we ignore parts of the HTTP/2 spec that h2 implements correctly. Unless you're working in a similar vein, you probably want to use the _real_ h2 library. _end warning_

To use this "in place of" the real _h2_, add this section to your project's `Cargo.toml`:

```toml
[patch.crates-io]
h2 = { git = "https://github.com/technosophos/h2.git" }
```

The essential issue is that the Go gRPC library [does not appropriately vet](https://github.com/grpc/grpc-go/blob/master/clientconn.go#L261-L270) the `:authority` header, and [some libraries](https://github.com/kubernetes/kubernetes/blob/master/pkg/kubelet/util/util_unix.go#L82-L92) set it to values like `unix:///tmp/my.sock`, which translates to `/tmp/my.sock`. Slashes are illegal inside of the `:authority` header (and a file path doesn't really qualify as an authority section anyway). However, the HTTP/2 spec seems to remain silent about what should be done here. So for now, I have just set it to `localhost` (though there may be a better solution).

---

A Tokio aware, HTTP/2.0 client & server implementation for Rust.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Crates.io](https://img.shields.io/crates/v/h2.svg)](https://crates.io/crates/h2)
[![Documentation](https://docs.rs/h2/badge.svg)][dox]

More information about this crate can be found in the [crate documentation][dox].

[dox]: https://docs.rs/h2

## Features

* Client and server HTTP/2.0 implementation.
* Implements the full HTTP/2.0 specification.
* Passes [h2spec](https://github.com/summerwind/h2spec).
* Focus on performance and correctness.
* Built on [Tokio](https://tokio.rs).

## Non goals

This crate is intended to only be an implementation of the HTTP/2.0
specification. It does not handle:

* Managing TCP connections
* HTTP 1.0 upgrade
* TLS
* Any feature not described by the HTTP/2.0 specification.

The intent is that this crate will eventually be used by
[hyper](https://github.com/hyperium/hyper), which will provide all of these features.

## Usage

To use `h2`, first add this to your `Cargo.toml`:

```toml
[dependencies]
h2 = "0.2"
```

Next, add this to your crate:

```rust
extern crate h2;

use h2::server::Connection;

fn main() {
    // ...
}
```

## FAQ

**How does h2 compare to [solicit] or [rust-http2]?**

The h2 library has implemented more of the details of the HTTP/2.0 specification
than any other Rust library. It also passes the [h2spec] set of tests. The h2
library is rapidly approaching "production ready" quality.

Besides the above, Solicit is built on blocking I/O and does not appear to be
actively maintained.

**Is this an embedded Java SQL database engine?**

[No](https://www.h2database.com).

[solicit]: https://github.com/mlalic/solicit
[rust-http2]: https://github.com/stepancheg/rust-http2
[h2spec]: https://github.com/summerwind/h2spec
