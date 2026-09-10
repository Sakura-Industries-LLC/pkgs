# How to operate the package repository

Replay a verified producer GitHub Release into the public repository at
<https://pkgs.sakuraindustries.net>, inspect the result, and recover a failed run.

## Prerequisites

- permission to run [`.github/workflows/publish.yml`](../.github/workflows/publish.yml)
  in this repository
- a published GitHub Release for an allowlisted producer and exact stable tag
- the reviewed policy in
  [`.config/package-repository.yaml`](../.config/package-repository.yaml)

Producers never receive R2 credentials or aggregate signing keys. Those
secrets stay in this repository's `packages-production` environment. A
producer sends only `{repository, tag}`.

## Replay a publication

Run **Publish package release** with the producer repository and exact tag.

From the Actions tab, enter:

- `repository`: GitHub `owner/name`, such as `Sakura-Industries-LLC/dntls-testnet`
- `tag`: the published stable tag, such as `v0.1.0`

From the CLI:

```bash
gh workflow run publish.yml \
  --repo Sakura-Industries-LLC/pkgs \
  -f repository=Sakura-Industries-LLC/dntls-testnet \
  -f tag=v0.1.0
```

Replace the repository and tag with the published producer release you are
replaying. The workflow calls
`Sakura-Industries-LLC/release/.github/workflows/publish-package-repository.yml`
at `822116cd4dee07d65a8573834e87ae4e08ffb07a` and selects
`packages-production`. Concurrent production writes share the
`package-repository-production` group and are not cancelled.

A producer `repository_dispatch` uses the same path: event type
`package-release` with exactly `{repository, tag}`.

## Read the result

A successful CLI publication writes `state` in its JSON result:

- `published` when at least one object was uploaded
- `unchanged` when every generated object already matched R2

The publish step log contains that envelope. Both states are success.

## Check the public roots

After a successful run, fetch the three repository roots:

```bash
curl --fail --silent --show-error \
  https://pkgs.sakuraindustries.net/apt/dists/stable/InRelease >/dev/null
curl --fail --silent --show-error \
  https://pkgs.sakuraindustries.net/rpm/stable/x86_64/repodata/repomd.xml >/dev/null
curl --fail --silent --show-error \
  https://pkgs.sakuraindustries.net/apk/stable/main/x86_64/APKINDEX.tar.gz >/dev/null
```

A reachable root means that format's metadata was published; it is not a
client installation check.

Client installation commands are omitted until a production publication
completes its installation acceptance check.

## Private testnet RPM signing key

The private Forgejo feed uses
[`dntls-testnet-rpm.asc`](../.config/keys/dntls-testnet-rpm.asc) for producer
signatures. Its fingerprint is
`58A8586219F782DA356F0E175C2FA50C262CAF8A`.

This file is a public verification key, not a change to the public repository's
allowlist. The existing `dntls-rpm.asc` key and publication policy are unchanged.

## Recover a failed run

Do not delete or rename objects in R2. Fix the failed prerequisite, then
replay the same `{repository, tag}` pair.

Publication is convergent:

- matching immutable objects are skipped
- missing objects are uploaded
- replaceable metadata is regenerated and uploaded
- a different value at an immutable path fails instead of overwriting that
  object

A successful replay reports `state: unchanged` when every generated object
already matches R2.
