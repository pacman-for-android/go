# Maintainer: Vesa Kaihlavirta <vegai@iki.fi>
# Maintainer: Alexander Rødseth <rodseth@gmail.com>
# Contributor: Andres Perera <andres87p gmail>
# Contributor: Matthew Bauer <mjbauer95@gmail.com>
# Contributor: Christian Himpel <chressie at gmail dot com>
# Contributor: Mike "str1ngs" Rosset

pkgname=go
pkgver=1
pkgrel=3
epoch=2
pkgdesc='Google Go compiler and tools (release version)'
arch=('x86_64' 'i686')
url="http://golang.org/"
license=('custom')
depends=('perl' 'ed' 'gawk')
makedepends=('mercurial' 'inetutils')
options=('!strip' '!emptydirs')
source=("$pkgname"
        "$pkgname.sh")
md5sums=('fb4d2508c44b3823e05ed360c2976322'
         'e7087d190fd519db3be5adb3a51194cc')

build() {
  _hgroot="https://go.googlecode.com/hg/"
  _hgrepo="release"

  cd "$srcdir"

  msg "Connecting to Mercurial server...."
  if [[ -d $_hgrepo ]] ; then
    cd $_hgrepo
    hg pull -u
    msg "The local files are updated."
  else
    hg clone $_hgroot $_hgrepo
  fi
  msg "Mercurial checkout done or server timeout"

  rm -rf "$srcdir/$_hgrepo-build"
  cp -r "$srcdir/$_hgrepo" "$srcdir/$_hgrepo-build"
  cd "$srcdir/$_hgrepo-build"

  export GOROOT="$srcdir/$_hgrepo-build"
  export GOOS_FINAL="/usr/lib/go"
  export GOOS=linux
  export GOBIN="$GOROOT/bin"
  export PATH="$GOBIN:$PATH"

  mkdir -p "$GOROOT/bin"
  cd "$GOROOT/src"
  hg update release-branch.go$pkgver
  . ./make.bash 
}

package() {
  cd "$srcdir/release-build"

  install -Dm644 LICENSE $pkgdir/usr/share/licenses/go/LICENSE
  install -Dm644 misc/bash/go $pkgdir/etc/bash_completion.d/go
  install -Dm644 misc/emacs/go-mode-load.el \
    $pkgdir/usr/share/emacs/site-lisp/go-mode-load.el
  install -Dm644 misc/emacs/go-mode.el \
    $pkgdir/usr/share/emacs/site-lisp/go-mode.el
  install -Dm644 misc/vim/ftdetect/gofiletype.vim \
    $pkgdir/usr/share/vim/vimfiles/ftdetect/gofiletype.vim
  install -Dm644 misc/vim/syntax/go.vim \
    $pkgdir/usr/share/vim/vimfiles/syntax/go.vim

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
  install -Dm755 "$srcdir/go" "$pkgdir/usr/bin/go"
}

# vim:set ts=2 sw=2 et:
