# HAProxy: ReadyStack build kit

The offline build kit for the Docker image `readystack/haproxy:3.4.6-CE-ubuntu24.04-r9`. It holds what the image is built from (upstream source, base images, toolchain, pinned dependencies) and the documents, so the image can be rebuilt with no network access (`docker build --network=none`).

- Release: [3.4.6-CE-ubuntu24.04-r9](https://github.com/readystack-dev/haproxy/releases/tag/3.4.6-CE-ubuntu24.04-r9)
- Image: [`docker.io/readystack/haproxy:3.4.6-CE-ubuntu24.04-r9`](https://hub.docker.com/r/readystack/haproxy/tags), manifest digest `sha256:30e9f2c8165115a842c40c40bd719e0776acacc765e3bc9879c6b04042d892e7`
- Product page: https://readystack.dev/products/haproxy
- Defects: this repository's Issues (defects recorded from 25 November 2026 on) and https://readystack.dev/defects.html?product=haproxy

HAProxy is distributed under its upstream licence (GPL-2.0-or-later AND LGPL-2.1-only); the kit contains upstream's source under that licence, and each vendored component under its own.

## Download

From the [3.4.6-CE-ubuntu24.04-r9 release](https://github.com/readystack-dev/haproxy/releases/tag/3.4.6-CE-ubuntu24.04-r9): `ReadyStack-haproxy-3.4.6-CE-ubuntu24.04-r9.tar.gz` (530.2 MB), SHA-256 `0f7eb3c829846d57dfa85bbab9b204e618c3d10c33477c6dd90ba74df2a46e48`.

```sh
sha256sum --check --ignore-missing SHA256SUMS
```

## Verify and rebuild

The rebuild statement is signed with the ReadyStack release key. Check the signature before you rely on it:

```sh
cosign verify-blob --key https://readystack.dev/keys/cosign.pub \
  --bundle ReadyStack-haproxy-3.4.6-CE-ubuntu24.04-r9.rebuild.json.sigstore ReadyStack-haproxy-3.4.6-CE-ubuntu24.04-r9.rebuild.json
```

Unpack with `-p` so file modes are kept, then follow the kit's own README.md and docs/REBUILD.md:

```sh
tar -xpzf ReadyStack-haproxy-3.4.6-CE-ubuntu24.04-r9.tar.gz
cd 3.4.6-CE-ubuntu24.04-r9
less README.md docs/REBUILD.md
```

The build trail (`ReadyStack-haproxy-3.4.6-CE-ubuntu24.04-r9.trail.json`, signed the same way, and its Markdown copy) records how this release was built and checked.

## This repository

`recipe/` holds the kit's recipe files (Dockerfiles, build scripts, pins) as published at https://readystack.dev/products/haproxy/recipe. The complete kit is the release download. Each release is a tag here.
