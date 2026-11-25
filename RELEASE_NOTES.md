# HAProxy 3.4.6 — ReadyStack release `3.4.6-CE-ubuntu24.04-r9`

Generated 2026-11-22 from this release's build records and strategy. Statements that come from a single record name it in brackets; the others come from this release's strategy and build log.

## Release

- **Image:** `readystack/haproxy:3.4.6-CE-ubuntu24.04-r9` (linux/amd64). Index digest `sha256:fa735aa2321931a6b69a04d1f9be6ac1fae8c8809d028c3a036def69fa4e76af` [`docs/provenance/LOCKED_DIGEST.txt`].
- **Image manifest digest:** `sha256:30e9f2c8165115a842c40c40bd719e0776acacc765e3bc9879c6b04042d892e7` [`TARGET_DIGEST.txt`].
- **Upstream:** HAProxy 3.4.6 from https://git.haproxy.org/git/haproxy-3.4.git [`docs/provenance/BUILD_RECIPE.json`].
- **Base image:** none (`FROM scratch`): a chiselled Ubuntu 24.04 runtime, the files Canonical's chisel cut from Ubuntu's own packages (`vendored/chisel/slices.txt`, `vendored/chisel/packages.tsv`); runtime user `haproxy`.
- **Build:** `source-rebuild-c`, compiled offline with `--network=none`.
- **Supersedes:** ReadyStack release `3.4.6-CE-ubuntu24.04-r8`.

## What changed in this release

- Upstream version unchanged (3.4.6); ReadyStack rebuild.
- HAProxy 3.4.6 is unchanged from the release this supersedes (3.4.6-CE-ubuntu24.04-r8); this release rebuilds it. Changes: the image is rebuilt on the current ubuntu:24.04 image (sha256:9c7f200c07e3…), which its maintainers updated after 3.4.6-CE-ubuntu24.04-r8 was built on sha256:2198e4c65076…. Settings, ports and data volumes are those of 3.4.6-CE-ubuntu24.04-r8: replace the container with this image on the same volumes.

## Security status at release (measured 2026-11-20)

- **Docker Scout:** 0 fixable critical/high vulnerabilities; runs as `haproxy` (non-root); SBOM + provenance attestations present (SPDX SBOM + SLSA v1) [`docs/provenance/SCOUT_VERDICT.json`].
- **OSV cross-check:** no fixable critical/high vulnerability beyond Docker Scout's findings.
- **Signature:** cosign signature verified by ReadyStack's release gate before publishing; check it yourself with `cosign verify --key https://readystack.dev/keys/cosign.pub readystack/haproxy:3.4.6-CE-ubuntu24.04-r9` (signatures are recorded in the public Sigstore transparency log).
- **ReadyStack release check at release:** passed (ReadyStack's own test, not a Docker Scout grade). Vulnerabilities are disclosed every day and the image never changes, so it is re-checked daily, beside Docker Scout's own dated grade, at https://readystack.dev/queue.
- **Before exposing this service:** The statistics page and Prometheus exporter have no password; the shipped configuration binds them to 127.0.0.1 inside the container. Before binding the stats frontend to a published address, protect the whole frontend: a `userlist` plus `http-request auth unless { http_auth(<userlist>) }` placed before the prometheus-exporter rule, with TLS on the listener. `stats auth` alone protects only the statistics page, not /metrics.
- **Before exposing this service:** The shipped configuration has no TLS: add `bind :8443 ssl crt <pem>` with your own certificate or keep the port on a trusted network.

## Reproducibility

Rebuilds offline from this archive to the identical image digest `sha256:30e9f2c8165115a842c40c40bd719e0776acacc765e3bc9879c6b04042d892e7`: run `./BUILD_OFFLINE.sh`, then `./VERIFY_OFFLINE.sh` (add `--online` to confirm the Docker Hub tag holds the same digest). Before publishing, ReadyStack rebuilt the kit with `./BUILD_OFFLINE.sh` and compared the result with the published digest [`docs/provenance/REPRO_VERIFICATION.json`], then ran `./BUILD_OFFLINE.sh` and `./VERIFY_OFFLINE.sh` from this archive (without `--online`).

## Validation

- 1 of 1 use cases passed (4 authored, 1 kept in the suite; 3 not exercised, see below) against the running image [`docs/validation/test-results.json`].
- The only case is `GET /healthz` (200); the suite checks nothing else.
- 3 further authored checks were not exercised and are not counted above: 1 (`GET /`) were answered with the same page the build's test container served for a made-up path, so they could not show the path exists; 2 targeted port 8404, which the build's test container does not publish, so they never reached the product (listed under Not exercised in `docs/validation/FAILED_TESTS.md`).

## Known issues

- Open review finding about the release notes of `3.4.6-CE-ubuntu24.04-r2`, carried to this release because nothing shows it no longer applies: The image's placeholder answer (a build input, unchanged from the previous release) recommends the single-file mount that the handbooks and the kit's own cfg header say never sees edits; the… [`RSD-haproxy-20fe5b7136`]

## Differences from upstream (by design)

- Unused third-party device-detection add-ons are removed from the source before the build: addons/51degrees, addons/deviceatlas, addons/wurfl [`RSD-haproxy-8100e6c8b5`]

## Defects fixed

- 27 problems found before release and fixed before it shipped (27 by review); each is listed in `docs/defects/defects.json`.

## Verify this archive

```
sha256sum -c CHECKSUMS.sha256
./BUILD_OFFLINE.sh && ./VERIFY_OFFLINE.sh --online
cosign verify --key https://readystack.dev/keys/cosign.pub readystack/haproxy:3.4.6-CE-ubuntu24.04-r9
```

ReadyStack records every signature in the public Sigstore transparency log (Rekor), so the command needs no extra flags.

