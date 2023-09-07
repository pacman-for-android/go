# Maintainer: Morten Linderud <foxboron@archlinux.org>
# Contributor: Daniel Martí <mvdan@mvdan.cc>
# Contributor: Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# Contributor: Alexander F. Rødseth <xyproto@archlinux.org>
# Contributor: Pierre Neidhardt <ambrevar@gmail.com>
# Contributor: Vesa Kaihlavirta <vegai@iki.fi>
# Contributor: Rémy Oudompheng <remy@archlinux.org>
# Contributor: Andres Perera <andres87p gmail>
# Contributor: Matthew Bauer <mjbauer95@gmail.com>
# Contributor: Christian Himpel <chressie@gmail.com>
# Contributor: Mike Rosset <mike.rosset@gmail.com>
# Contributor: Daniel YC Lin <dlin.tw@gmail.com>
# Contributor: John Luebs <jkluebs@gmail.com>

pkgname=go
epoch=2
pkgver=1.21.0
pkgrel=1.1
pkgdesc='Core compiler tools for the Go programming language'
arch=(x86_64 aarch64)
url='https://golang.org/'
license=(BSD)
makedepends=(git go)
replaces=(go-pie)
provides=(go-pie)
options=(!strip staticlibs)
source=(https://go.dev/dl/go${pkgver}.src.tar.gz{,.asc}
        go-change-prefix.patch)
validpgpkeys=('EB4C1BFD4F042F6DDDCCEC917721F63BD38B4796')
sha256sums=('818d46ede85682dd551ad378ef37a4d247006f12ec59b5b755601d2ce114369a'
            'SKIP'
            '2dfc427534d9bcdedbddb24824c69a7d7c49c50dca42b33cee2ba96da3b09f7a')

prepare() {
  cd "$pkgname"
  patch -Np2 -i ../go-change-prefix.patch
  sed -i '1s|.*|#!/data/usr/bin/bash|' src/*.bash
}

build() {
  export GOARCH=arm64
  export GOOS=linux
  # export GOAMD64=v1 # make sure we're building for the right x86-64 version
  export GOROOT_FINAL=/data/usr/lib/go
  export GOROOT_BOOTSTRAP=/data/usr/lib/go

  cd "$pkgname/src"
  ./make.bash -v
}

check() {
  export GO_TEST_TIMEOUT_SCALE=3

  cd $pkgname/src
  ./run.bash --no-rebuild -v -v -v -k
}

package() {
  cd "$pkgname"

  install -d "$pkgdir/data/usr/bin" "$pkgdir/data/usr/lib/go" "$pkgdir/data/usr/share/doc/go" \
    "$pkgdir/data/usr/lib/go/pkg/linux_aarch64_"{dynlink,race}

  cp -a bin pkg src lib misc api test "$pkgdir/data/usr/lib/go"
  # We can't strip all binaries and libraries,
  # as that also strips some testdata directories and breaks the tests.
  # Just strip the packaged binaries as a compromise.
  strip $STRIP_BINARIES "$pkgdir/data/usr/lib/go"{/bin/*,/pkg/tool/*/*}

  cp -r doc/* "$pkgdir/data/usr/share/doc/go"

  ln -sf /data/usr/lib/go/bin/go "$pkgdir/data/usr/bin/go"
  ln -sf /data/usr/lib/go/bin/gofmt "$pkgdir/data/usr/bin/gofmt"
  ln -sf /data/usr/share/doc/go "$pkgdir/data/usr/lib/go/doc"

  install -Dm644 VERSION "$pkgdir/data/usr/lib/go/VERSION"

  rm -rf "$pkgdir/data/usr/lib/go/pkg/bootstrap" "$pkgdir/data/usr/lib/go/pkg/tool/*/api"

  # TODO: Figure out if really needed
  rm -rf "$pkgdir"/data/usr/lib/go/pkg/obj/go-build

  # https://github.com/golang/go/issues/57179
  install -Dm644 go.env "$pkgdir/data/usr/lib/go/go.env"

  install -Dm644 LICENSE "$pkgdir/data/usr/share/licenses/$pkgname/LICENSE"
}

# vim: ts=2 sw=2 et
