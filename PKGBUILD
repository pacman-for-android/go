# Maintainer: Vesa Kaihlavirta <vegai@iki.fi>
# Maintainer: Alexander Rødseth <rodseth@gmail.com>
# Contributor: Rémy Oudompheng  <remy@archlinux.org>
# Contributor: Andres Perera <andres87p gmail>
# Contributor: Matthew Bauer <mjbauer95@gmail.com>
# Contributor: Christian Himpel <chressie@gmail.com>
# Contributor: Mike Rosset <mike.rosset@gmail.com>
# Contributor: Daniel YC Lin <dlin.tw@gmail.com>

pkgname=go
pkgver=1.0.1
pkgrel=4
epoch=2
pkgdesc='Google Go compiler and tools (release version)'
arch=('x86_64' 'i686')
url="http://golang.org/"
license=('custom')
depends=('perl' 'gawk')
makedepends=('inetutils')
options=('!strip' '!emptydirs')
install=go.install
source=("http://go.googlecode.com/files/${pkgname}$pkgver.src.tar.gz"
        "$pkgname.sh")
sha1sums=('fc8a6d6725f7f2bf7c94685c5fd0880c9b7f67f6'
          '75ff86617eecec4a3a21a99e34f130ba0fa78cad')

build() {
  cd "$srcdir/$pkgname"

  export GOROOT_FINAL="/usr/lib/go"
  export GOOS=linux

  cd src && bash make.bash
}

check() {
  cd "$srcdir/$pkgname"

  export GOROOT="$srcdir/$pkgname"
  export PATH="$srcdir/$pkgname/bin:$PATH"

  # TestSimpleMulticastListener will fail in standard chroot.
  cd src && bash run.bash --no-rebuild || true
}

package() {
  cd "$srcdir/$pkgname"

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
    ftplugin/go/fmt.vim ftplugin/go/godoc.vim ftplugin/go/import.vim \
    syntax/go.vim syntax/godoc.vim plugin/godoc.vim;
  do
    install -Dm644 "misc/vim/$f" "$pkgdir/usr/share/vim/vimfiles/$f"
  done

  mkdir -p "$pkgdir/"{etc/profile.d,usr/{share/go,lib/go,lib/go/src}}

  cp -r doc misc -t "$pkgdir/usr/share/go"
  ln -s /usr/share/go/doc "$pkgdir/usr/lib/go/doc"
  cp -r --preserve=timestamps bin "$pkgdir/usr"
  cp -r --preserve=timestamps pkg "$pkgdir/usr/lib/go"
  cp -r --preserve=timestamps $GOROOT/src/pkg "$pkgdir/usr/lib/go/src/"
  cp -r --preserve=timestamps $GOROOT/src/cmd "$pkgdir/usr/lib/go/src/cmd"
  cp -r --preserve=timestamps $GOROOT/src/lib9 "$pkgdir/usr/lib/go/src/"
  cp -r --preserve=timestamps $GOROOT/lib "$pkgdir/usr/lib/go/"

  install -Dm644 src/Make.* "$pkgdir/usr/lib/go/src"

  # Remove object files from target src dir
  find "$pkgdir/usr/lib/go/src/" -type f -name '*.[ao]' -delete
  find "$pkgdir/usr/lib/go/src/pkg" -type f -executable -delete

  # Headers for C modules
  install -Dm644 src/pkg/runtime/runtime.h \
    "$pkgdir/usr/lib/go/src/pkg/runtime/runtime.h"
  install -Dm644 src/pkg/runtime/cgocall.h \
    "$pkgdir/usr/lib/go/src/pkg/runtime/cgocall.h"

  # For packages that source /etc/profile.d/go.sh
  install -Dm755 "$pkgname.sh" "$pkgdir/etc/profile.d/$pkgname.sh"

  rm -f "$pkgdir/usr/share/go/doc/articles/wiki/get.bin"
}

# vim:set ts=2 sw=2 et:
