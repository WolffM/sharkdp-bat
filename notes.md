## Steps to reproduce
1. From the repository root, I ran `cargo tree -e features -i libgit2-sys` to inspect how `git2` is resolved in the default build.
2. I confirmed that default feature resolution enabled only `libgit2-sys/default`, with no vendored libgit2 feature selected.
3. This matches the packaging scenario where a macOS binary can link to a specific Homebrew-provided dynamic libgit2 version at build time.

## Observed
The dependency trace showed `libgit2-sys v0.18.3+1.9.2` with only the `default` feature path under `git2 v0.20.4`, and no `vendored-libgit2` feature activated by default. In Homebrew/macOS packaging, this can produce a runtime dependency like `/opt/homebrew/opt/libgit2@1.7/lib/libgit2.1.7.dylib`, which then fails to load when that exact versioned dylib path is missing after upgrades or cleanup.

## Expected
On macOS, `bat` should run without requiring users to keep a specific Homebrew `libgit2@1.7` dynamic library installed. The default build path should avoid brittle runtime linkage to an older, version-pinned external dylib and instead use a self-contained vendored libgit2 configuration so package-manager library churn does not break launching the executable.
