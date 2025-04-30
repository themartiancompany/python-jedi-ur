
#    ----------------------------------------------------------------------
#    Copyright © 2025  Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.  If not, see <https://www.gnu.org/licenses/>.

# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Truocolo <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
# Maintainer: Pellegrino Prevete (dvorak) <pellegrinoprevete@gmail.com>
# Maintainer: Pellegrino Prevete (dvorak) <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Maintainer: Daniel M. Capella <polyzen@archlinux.org>
# Contributor: Jelle van der Waa <jelle@archlinux.org>
# Contributor: Danilo Bargen <gezuru@gmail.com>
# Contributor: Simon Conseil <contact+aur at saimon dot org>
# Contributor: Jesus Alvarez

if [[ ! -v "_docs" ]]; then
  _docs="false"
fi
_pkg=jedi
_py="python"
_pyver="$( \
  "${_py}" \
    -V | \
    awk \
      '{print $2}')"
_pymajver="${_pyver%.*}"
_pyminver="${_pymajver#*.}"
_pynextver="${_pymajver%.*}.$(( \
  ${_pyminver} + 1))"
pkgbase="${_py}-${_pkg}"
pkgname=(
  "${pkgbase}"
)
if [[ "${_docs}" == "true" ]]; then
  pkgname+=(
    "${pkgbase}-docs"
  )
fi
pkgver=0.19.2
pkgrel=1
pkgdesc="Awesome autocompletion for python"
arch=(
  'any'
)
_http="https://github.com"
_ns="davidhalter"
url="${_http}/${_ns}/${_pkg}"
license=(
  'MIT'
)
depends=(
  "${_py}>=${_pymajver}"
  "${_py}<${_pynextver}"
  "${_py}-parso"
)
makedepends=(
  'git'
  "${_py}-build"
  "${_py}-installer"
  "${_py}-setuptools"
  "${_py}-wheel"
)
if [[ "${_docs}" == "true" ]]; then
  makedepends+=(
    "${_py}-sphinx"
    "${_py}-sphinx_rtd_theme"
  )
fi
checkdepends=(
  "${_py}-pytest"
  "${_py}-parso"
)
_url="${url}"
source=(
  "git+${_url}.git#tag=v${pkgver}"
  "git+${_http}/${_ns}/typeshed.git"
  "git+${_http}/${_ns}/django-stubs.git"
)
b2sums=(
  '6d9da16b255a847e8b60e993a1f3907730bfce2918fc4796d0bafa30d5242bb91a567d19a6ae06ad51d562ea5a491a3d7d1740e423b48bc61e8c2fd080fd1de3'
   'SKIP'
   'SKIP'
 )

pkgver() {
  cd \
    "${_pkg}"
  git \
    describe \
    --tags \
    --match \
      'v*' | \
    sed \
      's/^v//;s/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
  cd \
    "${_pkg}"
  git \
    submodule \
      init
  git \
    config \
      "submodule.${_pkg}/third_party/typeshed.url" \
      "${srcdir}/typeshed"
  git \
    config \
      "submodule.${_pkg}/third_party/django-stubs.url" \
      "${srcdir}/django-stubs"
  git \
    -c \
      "protocol.file.allow=always" \
    submodule \
      update \
        --recursive
}

build() {
  cd \
    "${_pkg}"
  "${_py}" \
    -m \
      "build" \
      --wheel \
      --skip-dependency-check \
      --no-isolation
  if [[ "${_docs}" == "true" ]]; then
    sphinx-build \
      -b \
        "text" \
      "docs" \
      "docs/_build/text"
    sphinx-build \
      -b \
        "man" \
      "docs" \
      "docs/_build/man"
  fi
}

check() {
  cd \
    "${_pkg}"
  # skip pytest 6 test issues
  # https://github.com/davidhalter/jedi/issues/1660
  # these are also skipped in upstream's Travis CI
  pytest \
    test \
    -k \
      'not test_completion[pytest'
}

package_python-jedi() {
  cd \
    "${_pkg}"
  "${_py}" \
    -m \
      "installer" \
      --destdir="${pkgdir}" \
      "dist/"*".whl"
  install \
    -Dm644 \
    "README.rst" \
    "CHANGELOG.rst" \
    -t \
    "${pkgdir}/usr/share/doc/${pkgname}"
}

package_python-jedi-docs() {
  local \
    _site_packages
  _site_packages="$( \
    "${_py}" \
      -c \
        "import site; print(site.getsitepackages()[0])")"
  install \
    -Dm644 \
    "docs/_build/text/"*".txt" \
    -t \
    "${pkgdir}/usr/share/doc/${pkgname}"
  install \
    -Dm644 \
    "docs/_build/man/${_pkg}.1" \
    "${pkgdir}/usr/share/man/man1/${pkgname}.1"
  # Symlink license file
  install \
    -d \
      "${pkgdir}/usr/share/licenses/${pkgname}"
  ln \
    -s "${_site_packages}/${_pkg}-${pkgver}.dist-info/LICENSE.txt" \
    "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE.txt"
}

# vim: ts=2 sw=2 et:
