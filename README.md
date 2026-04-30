# basic-conancenter

Baseline probe: verifies that Mend SCA detects a plain `conanfile.txt` with runtime dependencies sourced from Conan Center Index using Conan 2.x.

## Feature exercised

Plain `[requires]` section in `conanfile.txt` with two direct Conan Center packages (`zlib/1.3.1`, `poco/1.12.6`), exercising Mend's ability to parse a `conanfile.txt` lockfile pair and report all resolved direct and transitive runtime dependencies.

## Expected dependency tree

### Direct dependencies

| Package | Version | Source |
|---------|---------|--------|
| zlib | 1.3.1 | Conan Center (registry) |
| poco | 1.12.6 | Conan Center (registry) |

### Transitive dependencies (via poco/1.12.6)

| Package | Version | Source | Pulled by |
|---------|---------|--------|-----------|
| pcre2 | 10.44 | Conan Center (registry) | poco |
| utf8proc | 2.9.0 | Conan Center (registry) | poco |
| zlib | 1.3.1 | Conan Center (registry) | poco, openssl (shared with direct) |
| openssl | 3.4.5 | Conan Center (registry) | poco (enable_netssl=True, enable_crypto=True defaults) |
| expat | 2.6.4 | Conan Center (registry) | poco (enable_xml=True default) |
| sqlite3 | 3.44.2 | Conan Center (registry) | poco (enable_data_sqlite=True default) |

### zlib diamond

`zlib/1.3.1` is a shared node: declared as a direct dep AND pulled transitively by both `poco` and `openssl`. Mend must report it exactly once in the unique library count.

## Dependency types

- All deps are `requires` (runtime)
- No `tool_requires`, `test_requires`, or `python_requires`
- No private remote — all packages from Conan Center Index

## Probe metadata

- pattern: basic-conancenter
- pm: conan
- conan_version: 2.9.0 (conanio/conan:2.9.0)
- schema_version: "1.0"
- lockfile: conan.lock (Conan 2.x flat format)
- generated: 2026-04-30
- resolver_knowledge: not available (no Conan UA resolver file in knowledge cache — tree is exploratory)
