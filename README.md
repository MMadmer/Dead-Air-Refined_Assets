# Dead Air: Refined — content assets

This repository holds no content.

That is deliberate, not an oversight. The game assets Dead Air: Refined ships —
textures, meshes, animations, sounds — are published as **release assets**, one
GitHub release per content set, and are never committed as git objects. A git
repository is the wrong container for gigabytes: every clone would pay for every
revision ever pushed, and the bytes are already content-addressed and immutable
without git's help.

So this repository is a home for the releases and nothing else. Its working tree
carries a README, an ignore file, and the audit ledger in `index/`.

## What a release contains

Each release is tagged `content-MAJOR.MINOR.PATCH` and carries:

- one asset per content bundle, named
  `xtra_dead_air_x64_content_<group>_<NN>_<16 hex>.xdb0`, where the hex is the
  first 16 characters of the file's own SHA-256;
- one asset per binary delta, `.darpatch`, for upgrading a bundle from an
  earlier revision without re-downloading it whole.

**A published asset is never replaced and a published tag is never deleted.**
Bundle names carry the hash of their own bytes, so replacing bytes under an
existing name makes every installed manifest wrong, in a way that cannot be
repaired in the field: clients holding the old bytes see a mismatch they cannot
resolve, and clients fetching the new ones are told their manifest lies. If
something was published wrong, publish a new tag.

## What is in `index/`

`index/content-<version>.txt` records what was published under which tag: the
bundle names, their sizes and their hashes, as they went up. It is the only
local, diffable record of that, and it is what makes the never-replace rule
auditable without asking GitHub. A few kilobytes per release.

It is written by `tools/package/publish_dead_air_x64_content.ps1` in the engine
repository, which uploads the assets and then leaves the commit to a human.

## What is ignored, and why it still matters

`source/` (the authoring tree) and `build/` (the generated manifest) are ignored
here, and the bundle cache lives outside the tree entirely. None of them can be
regenerated from anything in git:

- the **authoring tree** is the only copy of the authored content;
- the **bundle cache** is what lets an unchanged bundle stay free across
  releases, and a repack is not guaranteed to reproduce the published bytes.

Losing either while a release is still reachable by players is unrecoverable.
Back them up outside git.

## Who reads this

Nobody, normally. The game does not clone this repository — it reads the content
manifest that shipped inside its own installation, and fetches the assets it
names straight from the release download URLs. This page exists for whoever
follows the `repo=` line in a manifest and wants to know what they are looking
at.

The engine, the installer and the whole content contract live in
[MMadmer/Dead-Air-Refined](https://github.com/MMadmer/Dead-Air-Refined); the
format is specified in its `docs/dead-air/CONTENT_BUNDLES.md`.

## Whose work this is

The bundles carry other people's work under their own terms - Stalker Two-K by
Akinaro, Absolute Nature 4 by Cromm Cruac, the first-person animation sets and
the 3D PDA. Who made what, on what terms, what was changed and whom the project
notified is in [`CREDITS.md`](CREDITS.md), the same ledger the engine repository
keeps as `docs/dead-air/ASSET_CREDITS.md`. Both sets of textures are free, for
S.T.A.L.K.E.R. games only, and stay that way here.
