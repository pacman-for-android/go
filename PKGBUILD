# Maintainer: Vesa Kaihlavirta <vegai@iki.fi>
# Maintainer: Alexander Rødseth <rodseth@gmail.com>
# Contributor: Rémy Oudompheng  <remy@archlinux.org>
# Contributor: Andres Perera <andres87p gmail>
# Contributor: Matthew Bauer <mjbauer95@gmail.com>
# Contributor: Christian Himpel <chressie@gmail.com>
# Contributor: Mike Rosset <mike.rosset@gmail.com>
# Contributor: Daniel YC Lin <dlin.tw@gmail.com>

pkgname=go
pkgver=1.1
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
source=('http://go.googlecode.com/files/go1.1beta1.linux-amd64.tar.gz'
        "$pkgname.sh")
#source=("http://go.googlecode.com/files/${pkgname}$pkgver.src.tar.gz"
sha256sums=('a0ab2aeb1e7845ee9d93c1c52fa62af1956bb903e243d78322000b99fcfd595f'
            'a03db71d323ed2794123bb31b5c8ad5febd551c490b5c0b341052c8e5f0ba892')

build() {
  cd "$srcdir/$pkgname"

  if [ "$CARCH" == 'x86_64' ]; then
    export GOARCH=amd64
  elif [ "$CARCH" == 'i686' ]; then
    export GOARCH=386
  else
    # A friendly gesture to Arch Linux ARM
    export GOARCH=arm
  fi
  export GOROOT_FINAL=/usr/lib/go
  export GOOS=linux

  cd src
  bash make.bash

  # Enable ARM crosscompilation for non-arm platforms
  if [ "$CARCH" == 'x86_64' ]; then
    export GOARCH=arm
  elif [ "$CARCH" == 'i686' ]; then
    export GOARCH=arm
  fi
  bash make.bash
}

check() {
  cd "$srcdir/$pkgname"

  if [ "$CARCH" == 'x86_64' ]; then
    export GOARCH=amd64
  elif [ "$CARCH" == 'i686' ]; then
    export GOARCH=386
  else
    # A friendly gesture to Arch Linux ARM
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
  rm -r "$pkgdir/usr/bin/linux_arm"
}

# vim:set ts=2 sw=2 et:
