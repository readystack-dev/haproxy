# Provenance — HAProxy (`3.4.6-CE-ubuntu24.04-r9`)

Every item below is copied from a record in this archive; the record is named in brackets.

## Source

- Upstream repository: https://git.haproxy.org/git/haproxy-3.4.git
- Revision built: `v3.4.6` (commit `56332c50b5291cfbbfd440d958adc988fbf87e2f`)
- Source tree: `98cbc2f2c71839bc24fb4454799e109179f1a5360386051ccc40b4b2b88c6216` over 1497 files [`docs/provenance/SOURCE_TREE_HASH.json`]
- Community Edition only: the source was checked for enterprise code before the build (CE audit at acquire). Unused third-party device-detection add-ons are removed from the source before the build: addons/51degrees, addons/deviceatlas, addons/wurfl.

## Build

- Method: `source-rebuild-c`; base image `scratch + a chiselled Ubuntu 24.04 runtime (vendored/chisel/slices.txt, packages.tsv)`
- Compiled with `--network=none` from vendored source, dependencies and base images; BuildKit pinned to `moby/buildkit:v0.31.2` (shipped in `toolchain/`).
- SOURCE_DATE_EPOCH: `1795161056` [`SOURCE_DATE_EPOCH.txt`]
- Frozen inputs: [`docs/provenance/FREEZE_MANIFEST.json`], build recipe: [`docs/provenance/BUILD_RECIPE.json`].

## Output

- Published image: `readystack/haproxy:3.4.6-CE-ubuntu24.04-r9` — index digest `sha256:fa735aa2321931a6b69a04d1f9be6ac1fae8c8809d028c3a036def69fa4e76af` [`docs/provenance/LOCKED_DIGEST.txt`]
- linux/amd64 image digest `sha256:30e9f2c8165115a842c40c40bd719e0776acacc765e3bc9879c6b04042d892e7` — reproduced from this archive before publishing [`docs/provenance/REPRO_VERIFICATION.json`].

## Attestations and signature

- SBOM + SLSA provenance attached to the image: attached: an SPDX SBOM and SLSA v1 provenance [`docs/provenance/SCOUT_VERDICT.json`]
- cosign signature by the ReadyStack release key: verified before publishing. Check it: `cosign verify --key https://readystack.dev/keys/cosign.pub readystack/haproxy:3.4.6-CE-ubuntu24.04-r9`

## Pinned inputs, and why

- 25 pinned inputs, each with where it came from, why this one, how it was checked and when it was decided: `docs/PINS.md` [`docs/provenance/PINS.json`] (upstream source 2, base and build images 3, image digests 2, dependency versions readystack sets 5, pins in the kit's dockerfiles 5, readystack's build engine 6, dependencies upstream chose 2).

## Build trail

- How this release passed through the ReadyStack harness, record by record (every chain, drive, stage, review round, defect and check, each naming the record it is copied from): `ReadyStack-haproxy-3.4.6-CE-ubuntu24.04-r9.trail.json`, published beside this archive and signed with the release key (`ReadyStack-haproxy-3.4.6-CE-ubuntu24.04-r9.trail.json.sigstore`), and readable at https://readystack.dev/products/haproxy/trail/3.4.6-CE-ubuntu24.04-r9.

