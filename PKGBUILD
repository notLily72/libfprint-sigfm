# Maintainer: Jan Alexander Steffens (heftig) <heftig@archlinux.org>
# Contributor: Tom Gundersen <teg@jklm.no>
# Contributor: Thomas Baechler <thomas@archlinux.org>
# Contributor: ZhenDong Wu <wzd04062@sina.com>
# Contributor: someone5678 <someone5678 dot dev at google dot com>

pkgname=libfprint-sigfm
_pkgname=libfprint
pkgver=1.94.9+44.r1855.20260105.921f70dd
pkgrel=1
pkgdesc="libfprint with the SIGFM Merge Request #418"
url="https://fprint.freedesktop.org/"
arch=(x86_64)
license=(LGPL-2.1-or-later)
depends=(
  gcc-libs
  glib2
  glibc
  libgudev
  libgusb
  openssl
  pixman
)
makedepends=(
  git
  glib2-devel
  gobject-introspection
  gtk-doc
  meson
  python-cairo
  python-gobject
  systemd
  opencv
  cmake
  doctest
)
checkdepends=(
  cairo
  umockdev
  doctest
)
provides=("libfprint=${pkgver}" "libfprint-2.so" "libfprint-sigfm")
replaces=("libfprint-sigfm<=1.94.8")
conflicts=("libfprint>=1.1")
groups=(fprint)
source=("git+https://gitlab.freedesktop.org/libfprint/libfprint.git"
        "sigfm::git+https://gitlab.freedesktop.org/Tooniis/libfprint#branch=sigfm"
)
b2sums=('SKIP'
        'SKIP'
        )

pkgver() {
  cd "${_pkgname}"

  _ver="$(git describe --tags | sed -E -e 's|^[vV]||' -e 's|\-g[0-9a-f]*$||' | tr '-' '+')"
  _rev="$(git rev-list --count HEAD)"
  _date="$(git log -1 --date=format:"%Y%m%d" --format="%ad")"
  _hash="$(git rev-parse --short HEAD)"

  if [ -z "${_ver}" ]; then
    error "Version could not be determined."
    return 1
  else
    printf '%s' "${_ver}.r${_rev}.${_date}.${_hash}"
  fi
}

prepare() {
  cd "${_pkgname}"
  git reset --hard origin/master && git clean -fdd
  git config remote.sigfm.url >&- || git remote add sigfm $srcdir/sigfm
  git config user.email >&- || git config user.email "makepkg@local.com"
  git config user.name >&- || git config user.name "makepkg"
  git fetch sigfm makepkg
  git merge sigfm/makepkg --no-edit
  # Revert "libfprint: Use fatal-warnings on g-i-scanner" to fix build
  sed -i '/fatal_warnings: true,/d' libfprint/meson.build
  sed -i 's|import shutil|import shutil\n    import traceback|g' tests/virtual-image.py
}

build() {
  local meson_options=(
    # Add virtual drivers for integration tests (e.g. in fprintd)
    # -D drivers=all

    -D installed-tests=false
  )

  arch-meson "${_pkgname}" build "${meson_options[@]}"
  meson compile -C build
}

# check() {
#   meson test -C build --print-errorlogs
# }

package() {
  meson install -C build --destdir "$pkgdir"
}

# vim:set sw=2 sts=-1 et:

