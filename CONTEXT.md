# CONTEXT — Plugin repository

Glossary for the self-hosted DBX plugin catalog that distributes this author's plugins.
Terms only; no implementation detail.

## Distribution

**Repository** — a DBX-installable catalog endpoint: one catalog document plus the
signed packages it references. Participants add a Repository to DBX once, by its
catalog URL, and every plugin it lists becomes discoverable.

**Catalog** — the single index document a Repository serves at a fixed URL. It is the
only thing a participant points DBX at. A Catalog lists plugins; it does not contain
their source code.

**Catalog URL** — the address entered in DBX's Plugin Center to add this Repository.
It is stable across plugin additions: new plugins appear by editing the Catalog, not
by adding a new Repository.

## Trust

**Repository key** — the Ed25519 key pair that signs published packages. There is one
per Repository, identified by a **key ID**. It is owned by the Repository, never by an
individual plugin.

**Signing** — the act of attaching the Repository's signature to already-built package
bytes, after review. Signing never rebuilds the package.

**Public key distribution** — the Repository's 32-byte public key, handed to participants
through a channel independent of the downloaded package. DBX does not trust a new
Repository's key automatically; a participant adds it explicitly.

**Signature seed** — the private half of the Repository key. It never enters the plugin
source repository, the Catalog, a package, workflow logs, or release notes.

**Unsigned candidate** — a package built by plugin CI and offered for review. A Candidate
carries no signature and no key ID. Candidates are not installable through a Catalog.

**Signed artifact** — a published, installable package: the reviewed Candidate bytes plus
the Repository signature, referenced from the Catalog with its checksum, size, and key ID.

## Provenance

**Publisher** — the author identity recorded in a package and its Catalog entry. It
records authorship and catalog ownership; it is not a key owner.

**Immutability** — a published version is never rebuilt in place. Any change to source,
metadata, or bytes means a new version and new artifacts.

## Relationship to plugin source

**Plugin source repository** — where a plugin's code lives, owned by the plugin author.
One Repository can distribute many plugins, each from its own source repository. The
Repository is a distribution container, not a monorepo.
