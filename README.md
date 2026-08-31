# pkgs

This repository owns the reviewed policy and public keys for the static
native package repository at <https://pkgs.sakuraindustries.net>. Allowlisted
GitHub Releases are the authoritative package source. Publication runs
through one serialized writer in this repository.

Policy lives in [`.config/package-repository.yaml`](.config/package-repository.yaml).
Public keys live beside it under `.config/keys/`.

## Documentation

- [How to operate the package repository](docs/operations.md)
- [Package repository contract](https://github.com/Sakura-Industries-LLC/release/blob/822116cd4dee07d65a8573834e87ae4e08ffb07a/docs/reference/package-repository-contract.md)
