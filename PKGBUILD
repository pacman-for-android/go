# Maintainer: Vesa Kaihlavirta <vegai@iki.fi>
# Contributor: Andres Perera <andres87p gmail>
# Contributor: Matthew Bauer <mjbauer95@gmail.com>
# Contributor: Christian Himpel <chressie at gmail dot com>

pkgname=go
pkgver=r59
pkgrel=1
epoch=1
pkgdesc='Google Go compiler and tools (release version)'
arch=('i686' 'x86_64')
url="http://golang.org/"
license=('custom')
depends=('perl' 'ed')
makedepends=('mercurial')
options=('!strip')
install=$pkgname.install
source=($pkgname.sh)
md5sums=('67c472bfcfdb760d1d1f0a87cfe3661f')

build() {
  _hgroot="https://go.googlecode.com/hg/"
  _hgrepo="release"

  cd "$srcdir"
  msg "Connecting to Mercurial server...."

  if [ -d $_hgrepo ] ; then
    cd $_hgrepo
    hg pull -u
    msg "The local files are updated."
  else
    hg clone $_hgroot $_hgrepo
  fi

  msg "Mercurial checkout done or server timeout"
  msg "Starting make..."

  rm -rf "$srcdir/$_hgrepo-build"
  cp -r "$srcdir/$_hgrepo" "$srcdir/$_hgrepo-build"
  cd "$srcdir/$_hgrepo-build"

  export GOROOT="$srcdir/$_hgrepo-build"
  export GOOS=linux
  export GOBIN="$GOROOT/bin"
  export PATH="$GOBIN:$PATH"

  mkdir -p "$GOROOT/bin"
  cd "$GOROOT/src"
  hg update release.$pkgver

  . ./make.bash 
}

check() {
  export GOROOT="$srcdir/$_hgrepo-build"
  export GOOS=linux
  export GOBIN="$GOROOT/bin"
  cd "$GOROOT/src/pkg"
  gomake -k test || true
}

package() {
  cd "$srcdir/release-build"

  install -Dm644 LICENSE $pkgdir/usr/share/licenses/go/LICENSE
  install -Dm644 misc/bash/go $pkgdir/etc/bash_completion.d/go
  install -Dm644 misc/emacs/go-mode-load.el $pkgdir/usr/share/emacs/site-lisp/go-mode-load.el
  install -Dm644 misc/emacs/go-mode.el $pkgdir/usr/share/emacs/site-lisp/go-mode.el
  install -Dm644 misc/vim/ftdetect/gofiletype.vim $pkgdir/usr/share/vim/vimfiles/ftdetect/gofiletype.vim
  install -Dm644 misc/vim/syntax/go.vim $pkgdir/usr/share/vim/vimfiles/syntax/go.vim

  mkdir -p $pkgdir/{etc/profile.d,usr/{share/go,lib/go,lib/go/src}}

  cp -r bin $pkgdir/usr
  cp -r doc misc -t $pkgdir/usr/share/go
  ln -s /usr/share/go/doc $pkgdir/usr/lib/go/doc
  cp -r pkg $pkgdir/usr/lib/go
  cp -r $GOROOT/src/pkg $pkgdir/usr/lib/go/src/ 
  cp -r $GOROOT/src/lib9 $pkgdir/usr/lib/go/src/ 
  cp -r $GOROOT/lib $pkgdir/usr/lib/go/ 

  install -Dm644 src/Make.* $pkgdir/usr/lib/go/src

  # Headers for C modules
  install -Dm644 src/pkg/runtime/runtime.h $pkgdir/usr/lib/go/src/pkg/runtime/runtime.h
  install -Dm644 src/pkg/runtime/cgocall.h $pkgdir/usr/lib/go/src/pkg/runtime/cgocall.h

  install $srcdir/go.sh $pkgdir/etc/profile.d/
}
