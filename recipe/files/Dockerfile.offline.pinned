# GENERATED L-009 hermetic C build (--network=none, SBOM+SLSA-attestation-producing).
# Pinned bases; vendored source (in-tree deps) + extra build debs + runtime debs from Dockerfile.vendor.
FROM ubuntu:24.04@sha256:9c7f200c07e3d4a03e7249d2c16bf66a51120fb876aa2242cf31451412543289 AS cbuild
# dpkg-install the vendored EXTRA build debs (pkg-config/libssl-dev/...) — gcc/make are in the builder.
RUN --mount=type=bind,source=vendored/build-debs,target=/tmp/bdebs \
    set --; for f in /tmp/bdebs/*.deb; do [ -e "$f" ] && set -- "$@" "$f"; done; \
    for pass in 1 2 3 4 5; do [ $# -eq 0 ] || dpkg -i --skip-same-version "$@" >/dev/null 2>&1 && break; done; \
    dpkg --configure -a && bad= && for d in "$@"; do p=$(dpkg-deb -f "$d" Package) v=$(dpkg-deb -f "$d" Version); \
      case "$(dpkg-query -W -f='${Version} ${db:Status-Abbrev}' "$p" 2>/dev/null)" in "$v ii"*) ;; *) bad="$bad $p=$v" ;; esac; done && \
    { [ -z "$bad" ] || { echo "vendored debs not installed at their vendored version:$bad" >&2; false; }; }
WORKDIR /src
COPY vendored/haproxy-src/ .
ARG SOURCE_DATE_EPOCH=1795161056
ENV SOURCE_DATE_EPOCH=${SOURCE_DATE_EPOCH}
RUN make -j"$(nproc)" TARGET=linux-glibc USE_OPENSSL=1 USE_QUIC=1 USE_QUIC_OPENSSL_COMPAT=1 USE_LUA=1 USE_PCRE2=1 USE_PCRE2_JIT=1 USE_PROMEX=1
RUN make install-bin PREFIX=/opt/haproxy
RUN set -eu; find /opt/haproxy -type f | LC_ALL=C sort | while read -r f; do \
      readelf -h "$f" >/dev/null 2>&1 || continue; \
      readelf -lW "$f" | sed -n 's/.*Requesting program interpreter: \(.*\)]$/\1/p'; \
      readelf -dW "$f" | sed -n 's/.*Shared library: \[\(.*\)\]$/\1/p'; \
    done | LC_ALL=C sort -u | while read -r lib; do case "$lib" in \
      /*) echo "$lib" ;; *) [ -n "$(find /opt/haproxy -name "$lib" -print -quit)" ] || echo "$lib" ;; esac; \
    done > /kitforge.needs && echo "the install prefix loads: $(tr '\n' ' ' < /kitforge.needs)"
# rs-probe: the HEALTHCHECK helper (standard library only, from the kit's rs-probe.go)
FROM golang:1.27.3-bookworm@sha256:972b4110ba0f47539a975c31e52c64765e1ff19f2d74469ae4a595c6676e80a5 AS build_rs_probe
WORKDIR /probe
COPY rs-probe.go main.go
COPY rs-probe.mod go.mod
ENV CGO_ENABLED=0 GOTOOLCHAIN=local GOFLAGS="-buildvcs=false" GOPROXY=off SOURCE_DATE_EPOCH=1795161056
RUN go build -trimpath -ldflags='-s -w' -o /rs-probe .
# the chiselled rootfs (vendored/chisel, cut by Dockerfile.vendor), the service user, the binaries
FROM ubuntu:24.04@sha256:9c7f200c07e3d4a03e7249d2c16bf66a51120fb876aa2242cf31451412543289 AS rootfs
ARG SOURCE_DATE_EPOCH=1795161056
ADD vendored/chisel/rootfs.tar /rootfs/
RUN printf 'haproxy:x:99:99::/var/lib/haproxy:/bin/sh\n' >> /rootfs/etc/passwd && printf 'haproxy:x:99:\n' >> /rootfs/etc/group && \
    mkdir -p /rootfs/var/lib/haproxy && chown -R 99:99 /rootfs/var/lib/haproxy
COPY --from=build_rs_probe /rs-probe /rootfs/usr/local/bin/rs-probe
COPY haproxy.cfg /rootfs/usr/local/etc/haproxy/haproxy.cfg
COPY --from=cbuild /opt/haproxy /rootfs/opt/haproxy
COPY --from=cbuild /kitforge.needs /tmp/needs/haproxy
RUN chmod 755 /rootfs/usr/local/bin/rs-probe && \
    chmod 0644 /rootfs/usr/local/etc/haproxy/haproxy.cfg
RUN --mount=type=bind,source=vendored/chisel,target=/chisel set -eu; \
    for n in /tmp/needs/*; do while read -r lib; do [ -n "$lib" ] || continue; \
      case "$lib" in /*) [ -e "/rootfs$lib" ] ;; *) [ -n "$(find /rootfs/usr/lib /rootfs/usr/lib64 -name "$lib" 2>/dev/null | head -1)" ] ;; esac || \
      { echo "$(basename "$n") needs $lib, which the chiselled rootfs does not have: add its slice to runtime_base.slices" >&2; exit 1; }; \
    done < "$n"; done; rm -rf /tmp/needs; \
    [ -e /rootfs/etc/os-release ] || { echo "no /etc/os-release: base-files_release-info is missing" >&2; exit 1; }; \
    [ "$(grep -c '^Package: ' /rootfs/var/lib/dpkg/status)" = "$(wc -l < /chisel/packages.tsv)" ] || \
      { echo "/var/lib/dpkg/status does not list every package of vendored/chisel/packages.tsv" >&2; exit 1; }; \
    rm -rf /rootfs/var/log/dpkg.log /rootfs/var/log/alternatives.log /rootfs/var/log/apt /rootfs/var/cache/ldconfig/aux-cache /rootfs/var/cache/fontconfig
FROM scratch
ARG SOURCE_DATE_EPOCH=1795161056
COPY --from=rootfs /rootfs/ /
ENV PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin PATH=/opt/haproxy/sbin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
LABEL org.opencontainers.image.licenses="GPL-2.0-or-later AND LGPL-2.1-only"
LABEL org.opencontainers.image.title="HAProxy"
LABEL org.opencontainers.image.source="https://git.haproxy.org/git/haproxy-3.4.git"
LABEL org.opencontainers.image.description="HAProxy 3.4 LTS load balancer and proxy, compiled from source, non-root"
LABEL org.opencontainers.image.version="3.4.6-CE-ubuntu24.04-r9"
LABEL org.opencontainers.image.vendor="ReadyStack.dev"
LABEL readystack.product="haproxy"
LABEL readystack.edition="CE"
LABEL readystack.build_method="source-rebuild-c"
LABEL com.readystack.license.disclosure="This image includes HAProxy (https://www.haproxy.org/) built from the v3.4.6 source, distributed under the GNU GPL version 2 or later (LGPL 2.1 for its exportable header files) with an exemption for linking with OpenSSL. HAProxy is unmodified except that the unused third-party device-detection add-on directories (51Degrees, DeviceAtlas, WURFL) are removed before the build. The complete corresponding source is in the ReadyStack release archive and is available on request via contact@readystack.dev."
LABEL org.opencontainers.image.revision="56332c50b5291cfbbfd440d958adc988fbf87e2f"
HEALTHCHECK --interval=10s --timeout=30s --start-period=5s \
  CMD /usr/local/bin/rs-probe -code http://127.0.0.1:8080/healthz || exit 1
USER haproxy
EXPOSE 8080
STOPSIGNAL SIGUSR1
ENTRYPOINT ["/opt/haproxy/sbin/haproxy", "-W", "-db"]
CMD ["-f", "/usr/local/etc/haproxy/haproxy.cfg"]
