# Maintainer: Alexander F. Rødseth <xyproto@archlinux.org>
# Maintainer: Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# Maintainer: Morten Linderud <foxboron@archlinux.org>
# Contributor: Pierre Neidhardt <ambrevar@gmail.com>
# Contributor: Vesa Kaihlavirta <vegai@iki.fi>
# Contributor: Rémy Oudompheng <remy@archlinux.org>
# Contributor: Andres Perera <andres87p gmail>
# Contributor: Matthew Bauer <mjbauer95@gmail.com>
# Contributor: Christian Himpel <chressie@gmail.com>
# Contributor: Mike Rosset <mike.rosset@gmail.com>
# Contributor: Daniel YC Lin <dlin.tw@gmail.com>
# Contributor: John Luebs <jkluebs@gmail.com>

pkgbase=go
pkgname=(go go-pie)
epoch=2
pkgver=1.11.5
pkgrel=2
arch=(x86_64)
url='https://golang.org/'
license=(BSD)
makedepends=(git go)
source=(https://storage.googleapis.com/golang/go$pkgver.src.tar.gz
        default-buildmode-pie.patch)
sha256sums=('bc1ef02bb1668835db1390a2e478dcbccb5dd16911691af9d75184bbe5aa943e'
            '9d2f0d201d4e002d74f548cc82bd131139bab5dd62191004c71dd430fdc1666d')

export GOOS=linux
case "$CARCH" in
  x86_64) export GOARCH=amd64 ;;
esac
export GOROOT_FINAL=/usr/lib/go
export GOROOT_BOOTSTRAP=/usr/lib/go
export GOCACHE=off

prepare() {
  cp -r $pkgbase $pkgbase-pie

  cd $pkgbase-pie
  patch -p1 -i "$srcdir/default-buildmode-pie.patch"
}

build() {
  export GOPATH="$srcdir/"

  for _pkgname in ${pkgname[@]}; do
    export GOROOT="$srcdir/$_pkgname"
    export GOBIN="$GOROOT/bin"

    cd "$srcdir/$_pkgname/src"
    ./make.bash --no-clean -v

    PATH="$GOBIN:$PATH" go install -v -buildmode=shared std
    PATH="$GOBIN:$PATH" go install -v -race std
  done
}

check() {
  # Run test suite only for unpatched Go as it expects non-PIE ldBuildmode
  export GOROOT="$srcdir/$pkgbase"
  export GOBIN="$GOROOT/bin"
  export PATH="$srcdir/$pkgbase/bin:$PATH"
  export GO_TEST_TIMEOUT_SCALE=2

  cd $pkgbase/src
  ./run.bash --no-rebuild -v -v -v -k 
}

_package() {
  options=(!strip staticlibs)
  cd "$srcdir/$1"

  install -d "$pkgdir/usr/bin" "$pkgdir/usr/lib/go" "$pkgdir/usr/share/doc/go"
  cp -a bin pkg src lib misc api "$pkgdir/usr/lib/go"
  cp -r doc/* "$pkgdir/usr/share/doc/go"

  ln -sf /usr/lib/go/bin/go "$pkgdir/usr/bin/go"
  ln -sf /usr/lib/go/bin/gofmt "$pkgdir/usr/bin/gofmt"
  ln -sf /usr/share/doc/go "$pkgdir/usr/lib/go/doc"

  install -Dm644 VERSION "$pkgdir/usr/lib/go/VERSION"

  rm -rf "$pkgdir/usr/lib/go/pkg/bootstrap" "$pkgdir/usr/lib/go/pkg/tool/*/api"

  # TODO: Figure out if really needed
  rm -rf "$pkgdir"/usr/lib/go/pkg/obj/go-build/*

  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/$1/LICENSE"
}

package_go() {
  pkgdesc='Core compiler tools for the Go programming language'

  _package $pkgname
}

package_go-pie() {
  pkgdesc='Core compiler tools for the Go programming language (with PIE enabled by default)'
  provides=(go)
  conflicts=(go)

  _package $pkgname

  # linux_amd64 is essentially the content of linux_amd64_shared, however there might
  # be cases where the user could generate the _shared directory as it's missing in go-pie.
  # Make sure it exists without rebuilding std with -buildmode=pie.
  cp -a "$pkgdir/usr/lib/go/pkg/linux_amd64/" "$pkgdir/usr/lib/go/pkg/linux_amd64_shared"
}

# vim: ts=2 sw=2 et
