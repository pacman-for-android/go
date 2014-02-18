# Maintainer: Vesa Kaihlavirta <vegai@iki.fi>
# Maintainer: Alexander Rødseth <rodseth@gmail.com>
# Contributor: Rémy Oudompheng  <remy@archlinux.org>
# Contributor: Andres Perera <andres87p gmail>
# Contributor: Matthew Bauer <mjbauer95@gmail.com>
# Contributor: Christian Himpel <chressie@gmail.com>
# Contributor: Mike Rosset <mike.rosset@gmail.com>
# Contributor: Daniel YC Lin <dlin.tw@gmail.com>

pkgname=go
pkgver=1.2
pkgrel=3
epoch=2
pkgdesc='Compiler and tools for the Go programming language from Google'
arch=('x86_64' 'i686')
url='http://golang.org/'
license=('custom')
depends=('perl' 'gawk')
makedepends=('inetutils')
options=('!strip' 'staticlibs')
optdepends=('liteide: IDE for editing and building projects written in Go')
install="$pkgname.install"
backup=('usr/lib/go/bin')

if [ "$CARCH" == 'x86_64' ]; then
  source=("http://go.googlecode.com/files/${pkgname}$pkgver.linux-amd64.tar.gz"
          'go.sh')
  sha256sums=('1252ca0aa0a96d53c0592fbc4ea9c9ff5c6b588169c92e08d06da9d89d9d91f2'
              'b6209904c88bde96efcac846c267e308e4dc39c8a45f2690204c2175de4b9b0a')
else
  source=("http://go.googlecode.com/files/${pkgname}$pkgver.linux-386.tar.gz"
          'go.sh')
  sha256sums=('3f51aacc7af60c520fe1071f3cb1d9ceee5db0e8fdfb7168a73e12fd7603e651'
              'b6209904c88bde96efcac846c267e308e4dc39c8a45f2690204c2175de4b9b0a')
fi

build() {
  cd "$pkgname/src"

  export GOROOT_FINAL=/usr/lib/go

  #
  # Arch Linux doesn't support SSE2 for i686 (to support older CPUs)
  # The SSE2 version is _a lot_ faster for i686.
  # This line disables it.
  #
  export GO386=387

  # Enable ARM crosscompilation for linux
  export GOOS=linux
  export GOARCH=arm

  bash make.bash

  # Crosscompilation for various platforms (including linux)
  for os in linux; do # darwin freebsd windows; do
    for arch in amd64 386; do
      export GOOS="$os"
      export GOARCH="$arch"
      bash make.bash --no-clean
    done
  done
}

check() {
  cd "$pkgname"

  export GO386=387

  export GOOS=linux
  if [ "$CARCH" == 'x86_64' ]; then
    export GOARCH=amd64
  elif [ "$CARCH" == 'i686' ]; then
    export GOARCH=386
  fi

  export GOROOT="$srcdir/$pkgname"
  export PATH="$srcdir/$pkgname/bin:$PATH"

  # TestSimpleMulticastListener will fail in standard chroot
  cd src && bash run.bash --no-rebuild || true
}

package() {
  cd "$pkgname"

  install -Dm644 LICENSE \
    "$pkgdir/usr/share/licenses/go/LICENSE"
  install -Dm644 misc/bash/go \
    "$pkgdir/usr/share/bash-completion/completions/go"
  install -Dm644 misc/emacs/go-mode-load.el \
    "$pkgdir/usr/share/emacs/site-lisp/go-mode-load.el"
  install -Dm644 misc/emacs/go-mode.el \
    "$pkgdir/usr/share/emacs/site-lisp/go-mode.el"
  install -Dm644 misc/zsh/go \
    "$pkgdir/usr/share/zsh/site-functions/_go"

  for f in ftdetect/gofiletype.vim autoload/go/complete.vim indent/go.vim \
    ftplugin/go/fmt.vim ftplugin/go/import.vim syntax/go.vim syntax/godoc.vim \
    plugin/godoc.vim;
  do
    install -Dm644 "misc/vim/$f" "$pkgdir/usr/share/vim/vimfiles/$f"
  done

  mkdir -p \
    "$pkgdir/"{etc/profile.d,usr/{share/go,lib/go,lib/go/src,lib/go/site/src}}

  cp -r doc misc -t "$pkgdir/usr/share/go"
  ln -s /usr/share/go/doc "$pkgdir/usr/lib/go/doc"
  cp -a bin "$pkgdir/usr"
  cp -a pkg "$pkgdir/usr/lib/go"
  cp -a "$GOROOT/src/pkg" "$pkgdir/usr/lib/go/src/"
  cp -a "$GOROOT/src/cmd" "$pkgdir/usr/lib/go/src/cmd"
  cp -a "$GOROOT/src/lib9" "$pkgdir/usr/lib/go/src/"
  cp -a "$GOROOT/lib" "$pkgdir/usr/lib/go/"
  cp -a "$GOROOT/include" "$pkgdir/usr/lib/go/"

  install -Dm644 src/Make.* "$pkgdir/usr/lib/go/src"

  # Remove object files from target src dir
  find "$pkgdir/usr/lib/go/src/" -type f -name '*.[ao]' -delete

  # Fix for FS#32813
  find "$pkgdir" -type f -name sql.go -exec chmod -x {} \;
  
  # Remove all executable source files
  find "$pkgdir/usr/lib/go/src/pkg" -type f -executable -delete

  # Headers for C modules
  install -Dm644 src/pkg/runtime/runtime.h \
    "$pkgdir/usr/lib/go/src/pkg/runtime/runtime.h"
  install -Dm644 src/pkg/runtime/cgocall.h \
    "$pkgdir/usr/lib/go/src/pkg/runtime/cgocall.h"

  # In order to make godoc work, see FS#38597
  install -Dm755 "$srcdir/$pkgname.sh" "$pkgdir/etc/profile.d/$pkgname.sh"

  # This is to make go get code.google.com/p/go-tour/gotour and
  # then running the gotour executable work out of the box.
  ln -sf /usr/bin "$pkgdir/usr/lib/go/bin"

  # For godoc
  install -Dm644 favicon.ico "$pkgdir/usr/lib/go/favicon.ico"

  rm -f "$pkgdir/usr/share/go/doc/articles/wiki/get.bin"

  install -Dm644 VERSION "$pkgdir/usr/lib/go/VERSION"

  find "$pkgdir/usr/"{lib/go/pkg,bin} -type f -exec touch '{}' +

  # Adding useful go tools (FS#38860)
  go get code.google.com/p/go.tools/cmd/benchcmp
  go get code.google.com/p/go.tools/cmd/cover
  go get code.google.com/p/go.tools/cmd/godoc
  go get code.google.com/p/go.tools/cmd/goimports
  go get code.google.com/p/go.tools/cmd/gotype
  go get code.google.com/p/go.tools/cmd/html2article
  go get code.google.com/p/go.tools/cmd/ssadump
  go get code.google.com/p/go.tools/cmd/vet
}

# vim:set ts=2 sw=2 et:
