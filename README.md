# pkgs

This repository owns the reviewed policy and public keys for the static
native package repository at <https://pkgs.meigma.dev>. Allowlisted producer
GitHub Releases are the authoritative package source. Publication runs
through one serialized writer in this repository.

Policy lives in [`.config/package-repository.yaml`](.config/package-repository.yaml).
Public keys live beside it under `.config/keys/`.

## Documentation

- [How to operate the package repository](docs/operations.md)
- [Package repository contract](https://github.com/meigma/release/blob/c1ee17342ec62b37fe728834975ce678f334ffd0/docs/reference/package-repository-contract.md)
