# HAProxy: ReadyStack build kit

The offline build kit for the Docker image `readystack/haproxy:3.4.6-CE-ubuntu24.04-r9`. It holds what the image is built from (upstream source, base images, toolchain, pinned dependencies) and the documents, so the image can be rebuilt with no network access (`docker build --network=none`).

- Release: [3.4.6-CE-ubuntu24.04-r9](https://github.com/readystack-dev/haproxy/releases/tag/3.4.6-CE-ubuntu24.04-r9)
- Image: [`docker.io/readystack/haproxy:3.4.6-CE-ubuntu24.04-r9`](https://hub.docker.com/r/readystack/haproxy/tags), manifest digest `sha256:30e9f2c8165115a842c40c40bd719e0776acacc765e3bc9879c6b04042d892e7`
- Product page: https://readystack.dev/products/haproxy
- Source and build recipe: https://readystack.dev/products/haproxy/recipe
- Build trail: https://readystack.dev/products/haproxy/trail/3.4.6-CE-ubuntu24.04-r9
- Defects: this repository's Issues (defects recorded from 25 November 2026 on) and https://readystack.dev/defects.html?product=haproxy

## The image

Single container. HAProxy runs in master-worker mode in the foreground (`haproxy -W -db`; doc/management.txt: -W starts a master that monitors the workers, -db disables background mode): the master is the container's main process and starts the worker that handles traffic. Compiled from source by this image's build (make TARGET=linux-glibc ...). No database, no sidecar, nothing stored on disk. The shipped configuration is a placeholder that proxies nothing: it answers on port 8080 with a placeholder message and serves statistics and metrics only on 127.0.0.1:8404 inside the container. To load-balance your services, mount a directory holding your haproxy.cfg over /usr/local/etc/haproxy (for example `-v "$PWD/haproxy":/usr/local/etc/haproxy:ro`; see Config reload in the handbooks).

## Who uses HAProxy and why

Operations teams and site reliability engineers who run busy websites and services that need load balancing and high availability.

HAProxy is a reverse proxy and load balancer for TCP and HTTP applications. It spreads traffic across servers, checks their health, terminates TLS, and can rate-limit or block abusive clients.

A typical use: An online shop runs its web application on several servers. HAProxy in front terminates HTTPS, sends each request to a server that passes its health checks, keeps a shopper's session on the same server, and slows down or blocks clients that send abusive request rates.

From the upstream project's own description: https://www.haproxy.org/, https://github.com/haproxy/haproxy/blob/master/doc/intro.txt.

## This release

- Docker Scout policy grade at release: **A** (94%), graded 2026-11-22 with `docker scout policy` (CLI 1.26.0, docker-default configuration).
  - Docker Scout counts 10 packages with copyleft licences (GPL, LGPL, MPL), all of them operating-system packages (it counts only packages whose licence it can read; the SBOM lists every component).
- Signed with ReadyStack's release key (fingerprint `04daa92344a52dbc`): https://readystack.dev/keys/cosign.pub.
- Runs as a non-root user (`haproxy`).
- Attestations on the image: an SPDX SBOM and SLSA provenance v1.
- Rebuilt from this kit with network egress blocked on 2026-11-22; the rebuilt image digest is the published one.
- Base: Ubuntu 24.04.

The current grade, measured again every day, is on https://readystack.dev/products/haproxy.

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

— ReadyStack · https://readystack.dev
