# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2024, 2025, 2026
#                Pellegrino Prevete
#    Copyright © 2025, 2026
#                Jelle van der Waa
#                Daniel M. Capella
#                Bruno Pagani
#                Felix Yan
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as
#    published by the Free Software Foundation, either version 3 of the
#    License, or (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public
#    License along with this program.
#    If not, see <https://www.gnu.org/licenses/>.

# Maintainers:
#   Truocolo
#     <truocolo@aol.com>
#     <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
#   Pellegrino Prevete (dvorak)
#     <pellegrinoprevete@gmail.com>
#     <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
#   Jelle van der Waa
#     <jelle@archlinux.org>
# Contributors:
#   Daniel M. Capella
#     <polyzen@archlinux.org>
#   Bruno Pagani
#     <archange@archlinux.org>
#   Felix Yan
#     <felixonmars@archlinux.org>

_os="$(
  uname \
    -o)"
_evmfs_available="$(
  command \
    -v \
    "evmfs" || \
    true)"
if [[ ! -v "_evmfs" ]]; then
  if [[ "${_evmfs_available}" != "" ]]; then
    _evmfs="true"
  elif [[ "${_evmfs_available}" == "" ]]; then
    _evmfs="false"
  fi
fi
if [[ ! -v "_py" ]]; then
  _py="python"
  _pyver="$(
    "${_py}" \
      -V | \
      awk \
        '{print $2}' || \
    true)"
  if [[ "${_pyver}" == "" ]]; then
    _msg=(
      "Python needs to be installed"
      "in order to build this package."
    )
    echo \
      "${_msg[*]}"
    exit \
      1
  fi
  _pymajver="${_pyver%.*}"
  _pyminver="${_pymajver#*.}"
  _pynextver="${_pymajver%.*}.$((
    ${_pyminver} + 1))"
  if (( 13 < "${_pynextver}" )); then
    _py_makedepend="${_py}<3.14"
  else
    _py_makedepend="${_py}3.13"
  fi
fi
_pkg=nodejs
_variant=lts-jod
pkgname="${_pkg}-${_variant}"
pkgver=22.22.0
pkgrel=1
pkgdesc='Evented I/O for V8 javascript (LTS release: Jod)'
arch=(
  "aarch64"
  "arm"
  "mips"
  "x86_64"
)
url="https://${_pkg}.org"
license=(
  "MIT"
)
depends=(
  "brotli"
  "c-ares"
  # http-parser
  "icu"
  "libuv"
  "libnghttp2"
  "openssl"
  # v8
  "zlib"
)
makedepends=(
  "${_py_makedepend}"
  "procps-ng"
)
_npm_optdepends=(
  'npm:'
    'Node.js package manager.'
)
optdepends=(
  "${_npm_optdepends[*]}"
)
provides=(
  "${_pkg}=${pkgver}"
)
conflicts=(
  "${_pkg}"
)
options=(
  !lto
)
source=(
  "update-icu-tests.patch"
  "${url}/dist/v${pkgver}/node-v${pkgver}.tar.xz"
)
# https://nodejs.org/download/release/latest-jod/SHASUMS256.txt.asc
sha256sums=(
  '43da0fb7469e34a239b2711876475f303a4012151e44f72e636a5a7fcf21bff8'
  '4c138012bb5352f49822a8f3e6d1db71e00639d0c36d5b6756f91e4c6f30b683'
)

_set_flags() {
  # /usr/lib/libnode.so uses malloc_usable_size,
  # which is incompatible with fortification level 3
  CFLAGS="${CFLAGS/_FORTIFY_SOURCE=3/_FORTIFY_SOURCE=2}"
  CXXFLAGS="${CXXFLAGS/_FORTIFY_SOURCE=3/_FORTIFY_SOURCE=2}"
}

prepare() {
  cd \
    "node-v${pkgver}"
  # Update ICU tests
  # https://github.com/nodejs/node/pull/60523
  patch \
    -Np1 \
    -i \
    "../update-icu-tests.patch"
}

build() {
  local \
    _configure_opts=()
  _configure_opts+=(
    --prefix="/usr"
    --with-intl="system-icu"
    --without-corepack
    --without-npm
    --shared-openssl
    --shared-zlib
    --shared-libuv
    --experimental-http-parser
    --shared-brotli
    --shared-cares
    --shared-nghttp2
    # --shared-v8
    # --shared-http-parser
  )
  _set_flags
  cd \
    "node-v${pkgver}"
  ./configure \
    "${_configure_opts[@]}"
  make
}

check() {
  local \
    _test_dir \
    _rm_args=()
  _test_dir="test/parallel"
  _rm_args+=(
    "${_test_dir}/test-http-outgoing-end-cork.js"
    "${_test_dir}/test-http2-client-set-priority.js"
    "${_test_dir}/test-http2-client-unescaped-path.js"
    "${_test_dir}/test-http2-max-invalid-frames.js"
    "${_test_dir}/test-http2-misbehaving-flow-control.js"
    "${_test_dir}/test-http2-misbehaving-flow-control-paused.js"
    "${_test_dir}/test-http2-multi-content-length.js"
    "${_test_dir}/test-http2-priority-event.js"
    "${_test_dir}/test-http2-reset-flood.js"
    "${_test_dir}/test-tls-ocsp-callback.js"
    # https://github.com/nodejs/node/pull/60523
    "${_test_dir}/test-datetime-change-notify.js"
    "${_test_dir}/test-icu-env.js"
  )
  _set_flags
  cd \
    "node-v${pkgver}"
  rm \
    "${_rm_args[@]}"
  make \
    test-only
}

package() {
  _set_flags
  cd \
    "node-v${pkgver}"
  make \
    DESTDIR="${pkgdir}" \
    install
  install \
    -vDm644 \
    "LICENSE" \
    -t \
    "${pkgdir}/usr/share/licenses/${pkgname}/"
}
