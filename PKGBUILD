# Maintainer: Vesa Kaihlavirta <vegai@iki.fi>
# Maintainer: Alexander Rødseth <rodseth@gmail.com>
# Contributor: Rémy Oudompheng  <remy@archlinux.org>
# Contributor: Andres Perera <andres87p gmail>
# Contributor: Matthew Bauer <mjbauer95@gmail.com>
# Contributor: Christian Himpel <chressie@gmail.com>
# Contributor: Mike Rosset <mike.rosset@gmail.com>
# Contributor: Daniel YC Lin <dlin.tw@gmail.com>
pkgname=go
pkgver=1.1rc1
pkgrel=1
epoch=2
pkgdesc='Google Go compiler and tools'
arch=('x86_64' 'i686')
url='http://golang.org/'
license=('custom')
depends=('perl' 'gawk')
makedepends=('inetutils')
options=('!strip')
install="$pkgname.install"
backup=('usr/lib/go/bin')
if [ "$CARCH" == 'x86_64' ]; then
  source=("http://go.googlecode.com/files/${pkgname}$pkgver.linux-amd64.tar.gz"
          "$pkgname.sh")
  sha256sums=('fc76b716b031d8afba3f3e6bcaca410e8dbf5fb625db5def00fbaaaa960e108c'
            'a03db71d323ed2794123bb31b5c8ad5febd551c490b5c0b341052c8e5f0ba892')
else
  source=("http://go.googlecode.com/files/${pkgname}$pkgver.linux-386.tar.gz"
          "$pkgname.sh")
  sha256sums=('b4eb8d9b55ae4a78c86213fa968755163ab3a8efa3f5c9f3a472b182b9d9a52e'
              'a03db71d323ed2794123bb31b5c8ad5febd551c490b5c0b341052c8e5f0ba892')
fi

build() {
  cd "$srcdir/$pkgname/src"

  export GOROOT_FINAL=/usr/lib/go

  # Enable ARM crosscompilation for linux
  export GOOS=linux
  export GOARCH=arm
  bash make.bash

  # Crosscompilation for other platforms too (including linux i686 and x86_64)
  for os in linux; do # also possible: darwin freebsd windows
    for arch in amd64 386; do
      export GOOS=$os
      export GOARCH=$arch
      bash make.bash
    done
  done
}

check() {
  cd "$srcdir/$pkgname"

  # Only test for linux and the architecture we're on
  export GOOS=linux
  if [ "$CARCH" == 'x86_64' ]; then
    export GOARCH=amd64
  elif [ "$CARCH" == 'i686' ]; then
    export GOARCH=386
  else
    export GOARCH=arm
  fi

  export GOROOT="$srcdir/$pkgname"
  export PATH="$srcdir/$pkgname/bin:$PATH"

  # TestSimpleMulticastListener will fail in standard chroot.
  cd src && bash run.bash --no-rebuild || true
}

package() {
  cd "$srcdir/$pkgname"

  install -Dm644 LICENSE \
    $pkgdir/usr/share/licenses/go/LICENSE
  install -Dm644 misc/bash/go \
    $pkgdir/usr/share/bash-completion/completions/go
  install -Dm644 misc/emacs/go-mode-load.el \
    $pkgdir/usr/share/emacs/site-lisp/go-mode-load.el
  install -Dm644 misc/emacs/go-mode.el \
    $pkgdir/usr/share/emacs/site-lisp/go-mode.el
  install -Dm644 misc/zsh/go \
    $pkgdir/usr/share/zsh/site-functions/_go

  for f in ftdetect/gofiletype.vim autoload/go/complete.vim indent/go.vim \
    ftplugin/go/fmt.vim ftplugin/go/import.vim syntax/go.vim syntax/godoc.vim \
    plugin/godoc.vim;
  do
    install -Dm644 misc/vim/$f $pkgdir/usr/share/vim/vimfiles/$f
  done

  mkdir -p $pkgdir/{etc/profile.d,usr/{share/go,lib/go,lib/go/src,lib/go/site}}

  cp -r doc misc -t $pkgdir/usr/share/go
  ln -s /usr/share/go/doc $pkgdir/usr/lib/go/doc
  cp -a bin $pkgdir/usr
  cp -a pkg $pkgdir/usr/lib/go
  cp -a $GOROOT/src/pkg $pkgdir/usr/lib/go/src/
  cp -a $GOROOT/src/cmd $pkgdir/usr/lib/go/src/cmd
  cp -a $GOROOT/src/lib9 $pkgdir/usr/lib/go/src/
  cp -a $GOROOT/lib $pkgdir/usr/lib/go/
  cp -a $GOROOT/include $pkgdir/usr/lib/go/

  install -Dm644 src/Make.* $pkgdir/usr/lib/go/src

  # Remove object files from target src dir
  find $pkgdir/usr/lib/go/src/ -type f -name '*.[ao]' -delete

  # Fix for FS#32813
  find $pkgdir -type f -name sql.go -exec chmod -x {} \;
  
  # Remove all executable source files
  find $pkgdir/usr/lib/go/src/pkg -type f -executable -delete

  # Headers for C modules
  install -Dm644 src/pkg/runtime/runtime.h \
    $pkgdir/usr/lib/go/src/pkg/runtime/runtime.h
  install -Dm644 src/pkg/runtime/cgocall.h \
    $pkgdir/usr/lib/go/src/pkg/runtime/cgocall.h

  # For packages that source /etc/profile.d/go.sh
  install -Dm755 $srcdir/$pkgname.sh $pkgdir/etc/profile.d/$pkgname.sh

  # This is to make go get code.google.com/p/go-tour/gotour and
  # then running the gotour executable work out of the box.
  ln -sf /usr/bin $pkgdir/usr/lib/go/bin

  # For godoc
  install -Dm644 favicon.ico $pkgdir/usr/lib/go/favicon.ico

  rm -f $pkgdir/usr/share/go/doc/articles/wiki/get.bin

  install -Dm644 VERSION $pkgdir/usr/lib/go/VERSION

  # Clean up
  #rm -r "$pkgdir/usr/bin/linux_arm"
}

# vim:set ts=2 sw=2 et:
