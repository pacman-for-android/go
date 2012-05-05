# Maintainer: Vesa Kaihlavirta <vegai@iki.fi>
# Maintainer: Alexander Rødseth <rodseth@gmail.com>
# Contributor: Andres Perera <andres87p gmail>
# Contributor: Matthew Bauer <mjbauer95@gmail.com>
# Contributor: Christian Himpel <chressie at gmail dot com>
# Contributor: Mike Rosset <mike.rosset@gmail.com>
# Contributor: Daniel YC Lin <dlin.tw@gmail.com>

pkgname=go
pkgver=1.0.1
pkgrel=1
epoch=2
pkgdesc='Google Go compiler and tools (release version)'
arch=('x86_64' 'i686')
url="http://golang.org/"
license=('custom')
depends=('perl' 'ed' 'gawk')
makedepends=('inetutils')
options=('!strip' '!emptydirs')
install=go.install
source=("http://go.googlecode.com/files/${pkgname}$pkgver.src.tar.gz"
        "$pkgname.run"
        "$pkgname.sh")
sha1sums=('fc8a6d6725f7f2bf7c94685c5fd0880c9b7f67f6'
          '655754d95688294b0cf2334a1155432884f92859'
          'a760c32cbad5a013f384a086efb3b8b8fd88794f')

build() {
  cd "$srcdir/$pkgname"

  export GOROOT="$srcdir/$pkgname"
  export GOOS_FINAL="/usr/lib/go"
  export GOOS=linux
  export GOBIN="$GOROOT/bin"
  export PATH="$GOBIN:$PATH"

  mkdir -p "$GOROOT/bin"
  cd "$GOROOT/src"

  . ./make.bash 
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

  mkdir -p $pkgdir/{etc/profile.d,usr/{share/go,lib/go,lib/go/src}}

  cp -r bin $pkgdir/usr
  cp -r doc misc -t $pkgdir/usr/share/go
  ln -s /usr/share/go/doc $pkgdir/usr/lib/go/doc
  cp -r pkg $pkgdir/usr/lib/go
  cp -r $GOROOT/src/pkg $pkgdir/usr/lib/go/src/ 
  cp -r $GOROOT/src/cmd $pkgdir/usr/lib/go/src/cmd
  cp -r $GOROOT/src/lib9 $pkgdir/usr/lib/go/src/ 
  cp -r $GOROOT/lib $pkgdir/usr/lib/go/ 

  install -Dm644 src/Make.* $pkgdir/usr/lib/go/src

  # Remove object files from target src dir
  find $pkgdir/usr/lib/go/src/ -type f -name '*.[ao]' -delete
  find $pkgdir/usr/lib/go/src/pkg -type f -executable -delete

  # Headers for C modules
  install -Dm644 src/pkg/runtime/runtime.h \
    $pkgdir/usr/lib/go/src/pkg/runtime/runtime.h
  install -Dm644 src/pkg/runtime/cgocall.h \
    $pkgdir/usr/lib/go/src/pkg/runtime/cgocall.h

  install $srcdir/go.sh $pkgdir/etc/profile.d/

  mv "$pkgdir/usr/bin/go" "$pkgdir/usr/bin/go.elf"
  install -Dm755 "$srcdir/go.run" "$pkgdir/usr/bin/go"
}

# vim:set ts=2 sw=2 et:
