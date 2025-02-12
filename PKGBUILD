# Maintainer: Jelle van der Waa <jelle@archlinux.org>
# Maintainer: Daniel M. Capella <polyzen@archlinux.org>
# Contributor: Bruno Pagani <archange@archlinux.org>
# Contributor: Felix Yan <felixonmars@archlinux.org>

pkgname=nodejs-lts-jod
pkgver=22.14.0
pkgrel=2
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
source=(https://nodejs.org/dist/v${pkgver}/node-v${pkgver}.tar.xz)
# https://nodejs.org/download/release/latest-jod/SHASUMS256.txt.asc
sha256sums=('c609946bf793b55c7954c26582760808d54c16185d79cb2fb88065e52de21914')

build() {
  cd node-v${pkgver}

  # this uses malloc_usable_size, which is incompatible with fortification level 3
  export CFLAGS="${CFLAGS/_FORTIFY_SOURCE=3/_FORTIFY_SOURCE=2}"
  export CXXFLAGS="${CXXFLAGS/_FORTIFY_SOURCE=3/_FORTIFY_SOURCE=2}"

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
  cd node-v${pkgver}
  make test-only
}

package() {
  cd node-v${pkgver}

  # this uses malloc_usable_size, which is incompatible with fortification level 3
  export CFLAGS="${CFLAGS/_FORTIFY_SOURCE=3/_FORTIFY_SOURCE=2}"
  export CXXFLAGS="${CXXFLAGS/_FORTIFY_SOURCE=3/_FORTIFY_SOURCE=2}"

  make DESTDIR="${pkgdir}" install
  install -Dm644 LICENSE -t "${pkgdir}"/usr/share/licenses/${pkgname}/
}
