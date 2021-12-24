# cargo-release bug

This crate demonstrates a bug in cargo-release v0.18.6.

Background:

  * This workspace contains two crates, `slow` and `speedy`.
  * The `slow` crate is at v1.0.0.
  * The `speedy` crate is at v2.0.0.

To reproduce the bug, run:

```
cargo release -p slow 1.1.0
```

This *should* be a valid version bump, because speedy is at v1.0.0. But
cargo-release will fail with the error "Fatal: Support for modifying Cannot
release version smaller than current one is currently unsupported".

Note that invoking cargo-release with the "minor" instruction, rather than the
explicit version "1.1.0", works fine:

```
cargo release -p slow minor
```

The bug is a result of cargo-release asking the question "is 1.1.0 a valid
version?" for every crate in the graph, not just those that are filtered by the
`-p` argument. When you specify "minor" instead of "1.1.0", cargo-release
instead computes a separate minor version bump for each crate in the
graph (v1.1.0 for slow and v2.1.0 for speedy).
