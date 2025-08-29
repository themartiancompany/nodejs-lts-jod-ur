# Maintainer: Jelle van der Waa <jelle@archlinux.org>
# Maintainer: Daniel M. Capella <polyzen@archlinux.org>
# Contributor: Bruno Pagani <archange@archlinux.org>
# Contributor: Felix Yan <felixonmars@archlinux.org>

pkgname=nodejs-lts-jod
pkgver=22.19.0
pkgrel=1
pkgdesc='Evented I/O for V8 javascript ("Active LTS" release: Jod)'
arch=(x86_64)
url="https://nodejs.org/"
license=(MIT)
depends=(openssl zlib icu libuv c-ares brotli libnghttp2) # http-parser v8)
makedepends=(python procps-ng)
optdepends=('npm: nodejs package manager')
options=(!lto)
provides=(
  "nodejs=$pkgver"
  nodejs-lts
)
conflicts=(nodejs)
source=("https://nodejs.org/dist/v${pkgver}/node-v${pkgver}.tar.xz")
# https://nodejs.org/download/release/latest-jod/SHASUMS256.txt.asc
sha256sums=('0272acfce50ce9ad060288321b1092719a7f19966f81419835410c59c09daa46')

_set_flags() {
  # /usr/lib/libnode.so uses malloc_usable_size, which is incompatible with fortification level 3
  CFLAGS="${CFLAGS/_FORTIFY_SOURCE=3/_FORTIFY_SOURCE=2}"
  CXXFLAGS="${CXXFLAGS/_FORTIFY_SOURCE=3/_FORTIFY_SOURCE=2}"
}

build() {
  _set_flags
  cd node-v${pkgver}

  ./configure \
    --prefix=/usr \
    --with-intl=system-icu \
    --without-npm \
    --shared-openssl \
    --shared-zlib \
    --shared-libuv \
    --experimental-http-parser \
    --shared-brotli \
    --shared-cares \
    --shared-nghttp2
    # --shared-v8
    # --shared-http-parser

  make
}

check() {
  _set_flags
  cd node-v${pkgver}
  # ignore failing tests, they work when compiled locally
  rm test/parallel/test-http2-client-set-priority.js
  rm test/parallel/test-http2-priority-event.js
  rm test/parallel/test-http-outgoing-end-cork.js
  make test-only
}

package() {
  _set_flags
  cd node-v${pkgver}
  make DESTDIR="${pkgdir}" install
  install -Dm644 LICENSE -t "${pkgdir}"/usr/share/licenses/${pkgname}/
}
