# 0004 — Repository catalog is a separate repository from plugin source

Status: accepted

## Context

The author will publish several DBX plugins over time and wants participants to add a
single catalog URL, as the Plugin Center's custom-repository field expects.

DBX's distribution model separates four artifacts: plugin source, unsigned review
candidates, repository-signed installable packages, and catalog metadata. It states
these have different owners and should not share one Git repository. A repository's
Ed25519 signing seed must never enter a source repository, a catalog, a package, or
workflow logs.

## Decision

Two repositories:

- `jzj2621899857/dbx-plugins` — the distribution repository. Holds the catalog document, the
  public key, and the signed release artifacts. This is the URL entered in DBX.
- `jzj2621899857/dbx-board-games` — plugin source for this plugin. Holds code, tests, docs, and the
  packaging workflow that produces unsigned candidates.

Future plugins add a source repository and one catalog entry; the catalog URL never
changes.

## Consequences

- One catalog URL serves every plugin, present and future.
- The signing seed lives only in a protected CI environment, never in either repository.
- Two repositories to maintain, and a release spans both: build in the source repo,
  sign and index in the distribution repo.
- A custom Repository's public key must reach participants out of band, because DBX does
  not auto-trust it.

## Alternatives rejected

- **One repository with a `catalog/` subdirectory and plugin sources.** Simpler to
  maintain, but mixes signed release artifacts into a source tree and puts the signing
  workflow next to the code it signs.
- **One repository per plugin, each with its own catalog.** Full isolation, but every new
  plugin means every participant adds another repository and another public key.
